# Apache Beam

A pipeline is a groups of steps called Ptransform which modify collections of data called Pcollection.

[Basics of the Beam model (apache.org)](https://beam.apache.org/documentation/basics/#aggregation)

## PCollections

- Can come from different sources, as files, databases or messaging systems.
- Each step in the pipeline creates a new one, instead of modifying the previously used.
- Each one can only be part of a single pipeline.
- Can be bounded or unbounded in case of unknown data size coming from streaming.
- All elements within it must be the same type and must be able to be encoded as byte string for proper distributed processing.
- Each element has an intrinsic element initially assigned by the source or user defined.

## Create a pipeline

`pipeline` encapsulates all the steps in the process.

```python
import apache_beam as beam

with beam.Pipeline() as pipeline:
  pass  # build your pipeline here
```

### Add pipeline configurations

Pass different configurations to the pipeline, which can be written in the code, defining own arguments in the [command-line parser](https://docs.python.org/3/library/argparse.html).

```python
import apache_beam as beam
from apache_beam.options.pipeline_options import PipelineOptions

# defining arguments than can be parsed
parser = argparse.ArgumentParser()
# one argument which defines the input and has a default value
parser.add_argument(
        "--input",
        required=False,
        help="The Cloud Pub/Sub topic to read from.",
        default='projects/prueba-sj22/topics/sensors-topic-sj22'
    )
# other argument to define the duration of the windows, which is not mandatory and has a default value
parser.add_argument(
    "--window_size",
    type=float,
    default=1.0,
    help="Output file's window size in minutes.",
)


# defining all the options
pipeline_options = PipelineOptions(
    pipeline_args # variables read from the parser
    streaming=True, # variable passed directly
    save_main_session=True # variable passed directly
)

# passing the options when creating the pipeline
with beam.Pipeline(options=pipeline_options) as pipeline:
  pass  # build your pipeline here
```

## Read from a source

```python
import apache_beam as beam

with beam.Pipeline() as pipeline:
  lines = (
    pipeline
    | 'ReadMyFile' >> beam.io.ReadFromText(
    'gs://some/inputData.txt')
```

A PCollection can also be created internally with `Create`

```python
import apache_beam as beam

with beam.Pipeline() as pipeline:
  lines = (
      pipeline
      | beam.Create([
          'To be, or not to be: that is the question: ',
          "Whether 'tis nobler in the mind to suffer ",
          'The slings and arrows of outrageous fortune, ',
          'Or to take arms against a sea of troubles, ',
      ]))
```

## Transformations

- Applied to every element of the PCollection(s).

- It can be passed additional PCollections as side inputs or also produced additional PCollections as output

### Apply a transformation

Using the pipe operator `|`.

```python
[Output PCollection] = [Input PCollection] | [Transform]
```

```python
[Final Output PCollection] = ([Initial Input PCollection] | [First Transform]
              | [Second Transform]
              | [Third Transform])
```

Not only a single path must be followed. Here, we obtain to end PCollections.

```python
[PCollection of database table rows] = [Database Table Reader] | [Read Transform]
[PCollection of 'A' names] = [PCollection of database table rows] | [Transform A]
[PCollection of 'B' names] = [PCollection of database table rows] | [Transform B]
```

### ParDo

- For generic parallel processing
- Considers each element of the PCollection, performs a user defined processing function on each one and emits zero or more elements to an output PCollection
- Useful for apply different operations to the elements:
	- Filtering
	- Formatting or type-converting
	- Extracting parts
	- Perform computations
- To be applied, it is used an instance of a user defined subclass of the DoFn class.

```python
# defining a subclass of DoFn called ElementLengthFn
class ElementLengthFn(beam.DoFn):
	# it must be defined a process method, which receives the element as the input, applies the user defined logic and puts the results in the output PCollection
	# the element argument must not be modified
	def process(self, element):
    # it must be returned either individual elements using yield or an iterable like a list with return
    # yield suspends the function execution, send a value to the caller and then resumes the execution. Instead, return computes all the values first and then sends them all at the same time. A function returning yield becomes a generator and can generate unlimited values, like a fibonnaci sequence
    return [len(element)]
	# once a value is returned with return or yield, it should not be modified



# Apply a ParDo to the PCollection "words" to compute lengths for each word.
[Output PCollection] = [Initial PCollection] | beam.ParDo(ElementLengthFn())
```

If the ParDo is lightweight, it can be applied directly with a lambda function.

```python
# apply a lambda function to the PCollection words.
# in this case, it returns the length of each element

[Output PCollection] = [Initial PCollection] | beam.FlatMap(lambda x: [len(x)])
```

If the ParDo applies a one-to-one mapping of input elements to output elements (Exactly one output per input), the simpler Map transform can be used.

```python
# in this case, it returns the length of each element

[Output PCollection] = [Initial PCollection] | beam.Map(len)
```

For elements which are (key, value) pairs, MapTuple is used

```python
import apache_beam as beam

with beam.Pipeline() as pipeline:
  plants = (
      pipeline
      | 'Gardening plants' >> beam.Create([
          ('🍓', 'Strawberry'),
          ('🥕', 'Carrot'),
          ('🍆', 'Eggplant'),
          ('🍅', 'Tomato'),
          ('🥔', 'Potato'),
      ])
      | 'Format' >>
      beam.MapTuple(lambda icon, plant: '{}{}'.format(icon, plant))
      | beam.Map(print))

'''
🍓Strawberry
🥕Carrot
🍆Eggplant
🍅Tomato
🥔Potato
'''
```

#### Access metadata of the element

Get the timestamp of the element

```python
import apache_beam as beam

class ProcessRecord(beam.DoFn):

  def process(self, element, timestamp=beam.DoFn.TimestampParam):
     # access timestamp of element.
     pass
```

Get the window(s) the element is part of

```python
import apache_beam as beam

class ProcessRecord(beam.DoFn):

  def process(self, element, window=beam.DoFn.WindowParam):
     # access window e.g. window.end.micros
     pass
```

### GroupByKey

- To process a collection of key/value pairs
- It collects are values with the same key
- Having (cat,3) and (cat,8) we would have, having cat as the key: (cat, [3,8])
- For unbounded PCollections, non-global windowing or an aggregation trigger must be used

```python
import apache_beam as beam

with beam.Pipeline() as pipeline:
  produce_counts = (
      pipeline
      | 'Create produce counts' >> beam.Create([
          ('spring', '🍓'),
          ('spring', '🥕'),
          ('spring', '🍆'),
          ('spring', '🍅'),
          ('summer', '🥕'),
          ('summer', '🍅'),
          ('summer', '🌽'),
          ('fall', '🥕'),
          ('fall', '🍅'),
          ('winter', '🍆'),
      ])
      | 'Group counts per produce' >> beam.GroupByKey()
      | beam.MapTuple(lambda k, vs: (k, sorted(vs)))  # sort and format
      | beam.Map(print))

'''
('spring', ['🍓', '🥕', '🍆', '🍅'])
('summer', ['🥕', '🍅', '🌽'])
('fall', ['🥕', '🍅'])
('winter', ['🍆'])
'''
```

### CoGroupByKey

- Performs a relational join of two or more key/value PCollections with the same key type
- For unbounded PCollections, non-global windowing or an aggregation trigger must be used
- It accepts dictionaries in Python
- The output is a PCollection with one key/value tuple for each key in the input PCollections

```python
import apache_beam as beam

with beam.Pipeline() as pipeline:
  icon_pairs = pipeline | 'Create icons' >> beam.Create([
      ('Apple', '🍎'),
      ('Apple', '🍏'),
      ('Eggplant', '🍆'),
      ('Tomato', '🍅'),
  ])

  duration_pairs = pipeline | 'Create durations' >> beam.Create([
      ('Apple', 'perennial'),
      ('Carrot', 'biennial'),
      ('Tomato', 'perennial'),
      ('Tomato', 'annual'),
  ])

plants = ({'icons': icon_pairs, 'durations': duration_pairs}
          | 'Merge' >> beam.CoGroupByKey())
output = plants | beam.Map(print)

'''
('Apple', {'icons': ['🍎', '🍏'], 'durations': ['perennial']})
('Carrot', {'icons': [], 'durations': ['biennial']})
('Tomato', {'icons': ['🍅'], 'durations': ['perennial', 'annual']})
('Eggplant', {'icons': ['🍆'], 'durations': []})
'''

# it can also be used a function to display the data as wanted
def join_info(name_info):
	(name, info) = name_info
	return '%s; %s; %s' %\
		(name, sorted(info['emails']), sorted(info['phones']))

output2 = plants | beam.Map(join_info) | beam.Map(print)

'''
"Apple; ['🍎', '🍏']; ['perennial']",
"Carrot; []; ['biennial']",
"Tomato; ['🍅']; ['perennial', 'annual']",
"Eggplant; ['🍆']; []"
'''
```

### Combine

- Function to combine elements of a PCollection
- It can work on the entire PCollection or on the values with the same key
- The function used should be commutative and associative
- For complex functions, a subclass of CombineFn is created

A simple combine:

```python
pc = [1, 10, 100, 1000]

def bounded_sum(values, bound=500):
  return min(sum(values), bound)

small_sum = pc | beam.CombineGlobally(bounded_sum)  # [500]
large_sum = pc | beam.CombineGlobally(bounded_sum, bound=5000)  # [1111]
```

Combine per key:

```python
import apache_beam as beam

with beam.Pipeline() as pipeline:
  total = (
      pipeline
      | 'Create plant counts' >> beam.Create([
          ('🥕', 3),
          ('🥕', 2),
          ('🍆', 1),
          ('🍅', 4),
          ('🍅', 5),
          ('🍅', 3),
      ])
      | 'Sum' >> beam.CombinePerKey(lambda values: min(sum(values), 8))
      | beam.Map(print))

'''
('🥕', 5)
('🍆', 1)
('🍅', 8)
'''
```

The advanced way is creating a subclass from CombineFn with 4 methods defined:

- `create_accumulator` to create an empty accumulator
- `add_input` to be called once per element which combines the accumulator and the input
- `merge_accumulator` to merge multiple accumulators if were processed in parallel
- `extract_output` to add final calculations before extracting a result

```python
import apache_beam as beam

class AverageFn(beam.CombineFn):
  def create_accumulator(self):
    sum = 0.0
    count = 0
    accumulator = sum, count
    return accumulator

  def add_input(self, accumulator, input):
    sum, count = accumulator
    return sum + input, count + 1

  def merge_accumulators(self, accumulators):
    # accumulators = [(sum1, count1), (sum2, count2), (sum3, count3), ...]
    sums, counts = zip(*accumulators)
    # sums = [sum1, sum2, sum3, ...]
    # counts = [count1, count2, count3, ...]
    return sum(sums), sum(counts)

  def extract_output(self, accumulator):
    sum, count = accumulator
    if count == 0:
      return float('NaN')
    return sum / count

with beam.Pipeline() as pipeline:
  average = (
      pipeline
      | 'Create plant counts' >> beam.Create([
          ('🥕', 3),
          ('🥕', 2),
          ('🍆', 1),
          ('🍅', 4),
          ('🍅', 5),
          ('🍅', 3),
      ])
      | 'Average' >> beam.CombinePerKey(AverageFn())
      | beam.Map(print))
```

### Flatten

To merge PCollections that store the same data type. IF the PCollections are using a windowing strategy, it should be the same for all.

```python
# flatten takes a tuple of PCollection objects.
# returns a single PCollection that contains all of the elements in the PCollection objects in that tuple.

merged = (
    (pcoll1, pcoll2, pcoll3)
    # a list of tuples can be "piped" directly into a Flatten transform.
    | beam.Flatten())
```

### Partition

Splits a single PCollection into smaller collections according a user provided partitioning function. It can not be decided mid pipeline but at the start.

```python
# Provide an int value with the desired number of result partitions, and a partitioning function (partition_fn in this example).
# Returns a tuple of PCollection objects containing each of the resulting partitions as individual PCollection objects.
students = ...

def partition_fn(student, num_partitions):
  return int(get_percentile(student) * num_partitions / 100)

by_decile = students | beam.Partition(partition_fn, 10)


# You can extract each partition from the tuple of PCollection objects as follows:

fortieth_percentile = by_decile[4]
```

### Composite transforms

- A complex transform performing simple transforms

- Defined creating a subclass of the PTransform class

```python
@beam.ptransform_fn
def CountWords(pcoll):
  return (
      pcoll
      # Convert lines of text into individual words.
      | 'ExtractWords' >>
      beam.FlatMap(lambda x: re.findall(r'[A-Za-z\']+', x))

      # Count the number of times each word occurs.
      | beam.combiners.Count.PerElement())

counts = lines | CountWords()
```

## Windowing

To divide unbounding PCollection into window of limited data.

Three ways:

- Fixed windows: Divides PCollections in fixed times
- Sliding windows: Start every defined period of time capturing a defined amount of time of data (Data may be used in multiple windows creating a copy of the element)
- Session windows: Groups all data until a certain time n has passed with no new data
- Global windows: Includes all data. (Can't be used for unbounded by default if any aggregation is done]

### Creating a fixed window

```python
# the variable window_size_minutes can be defined with a command-line option
[Output collection] = [Input collection] | beam.WindowInto(window.FixedWindows(60 * window_size_minutes))
```

### Creating a slicing window

```python
from apache_beam import window
# one windows created every 5 second with the last 30 seconds of data
[Output collection] = [Input collection] | 'window' >> beam.WindowInto(window.SlidingWindows(30, 5))
```

### Creating a session window

```python
# groups in a window data separated at least 10 minutes from new data
from apache_beam import window
session_windowed_items = (
    items | 'window' >> beam.WindowInto(window.Sessions(10 * 60)))
```

### Creating a global window

```python
from apache_beam import window
global_windowed_items = (
    items | 'window' >> beam.WindowInto(window.GlobalWindows()))
```

### Defining a timestamp

[Add a timestamp](https://beam.apache.org/documentation/programming-guide/#adding-timestamps-to-a-pcollections-elements) to the elements of a PCollection to window the data with a user defined timestamp instead of the given by default (In some inputs). It is done using a ParDo.

```python
class AddTimestampDoFn(beam.DoFn):
  def process(self, element):
    # create the logic to extract the timestamp from the element
    timestamp_new = element[2]
    # wrap and emit the current entry and new timestamp in a
    # TimestampedValue.
    yield beam.window.TimestampedValue(element, timestamp_new)
    
timestamped_items = items | 'timestamp' >> beam.ParDo(AddTimestampDoFn())
```

This PTransform is usually applied after applying the windowing.

### Watermarks and triggers

There is always a lag between the event time and the processing time. Also, no guarantee that data will appear in the same order that was generated. Late data is data arriving later than than the watermark after closing the window. Watermark is a defined time to wait for data that may arrive later before processing the window with the available data.

By default, Beam determines when all data has arrived and advances the watermark. The triggers determines when each window is aggregated and emitted and can be modified.

Different options:

- Event time: Operate on the event time from the timestamp of the elements (Trigger AfterWatermark)
- Processing time: Operate on the processing time in the pipeline transformation (Trigger AfterProcessingTime)
- Data-driven: Operate examining the data arriving in the window and checking if there a certain number of elements (Trigger AfterCount)
- Composite: Combination of the previous

Defining triggers allow for emitting early results before all the data arrives, and also to process late data.

#### Processing time trigger

```python
pcollection | beam.WindowInto(
    FixedWindows(1 * 60), # 1 minute window size
    trigger=AfterProcessingTime(1 * 60), # triggers 1 minute after the first element in the window has been processed
    accumulation_mode=AccumulationMode.DISCARDING), # what to do with data already triggered for the next triggered: DISCARDING OR ACCUMULATING
	allowed_lateness=1800 # if allowed_lateness is specifies, data arriving after the watermark is processed and and the defauly trigger will emit new results whenever new late data arrives
```

#### Event and processing time trigger

One common trigger is AfterWatermark. It triggers after the watermark and when late data arrives (With a delay of 10 minutes) for two days until the trigger stops

```python
pcollection | WindowInto(
    FixedWindows(1 * 60), # fixed windows of 1 minute
    trigger=AfterWatermark(late=AfterProcessingTime(10 * 60)),
    allowed_lateness=10,
    accumulation_mode=AccumulationMode.DISCARDING)
```

#### Data-driven and processing time trigger

Trigger is executed every 100 elements or after a minute

```python
pcollection | WindowInto(
    FixedWindows(1 * 60),
    trigger=Repeatedly(
        AfterAny(AfterCount(100), AfterProcessingTime(1 * 60))),
    accumulation_mode=AccumulationMode.DISCARDING)
```

#### Allow late data

```python
  pc = [Initial PCollection]
  pc | beam.WindowInto(
            FixedWindows(60),
            trigger=AfterProcessingTime(60),
            allowed_lateness=1800) # if allowed_lateness is specifies, data arriving after the watermark is processed and and the defauly trigger will emit new results whenever new late data arrives
     | ...
```

## Schemas

Defining an schema for a PCollection defines its elements as an ordered list of named fields.

### Create an schema

Considering the following schema

**Purchase:**

| **Field Name**  | **Field Type**          |
| :-------------- | :---------------------- |
| userId          | STRING                  |
| itemId          | INT64                   |
| shippingAddress | ROW(ShippingAddress)    |
| cost            | INT64                   |
| transactions    | ARRAY[ROW(Transaction)] |

**ShippingAddress:**

| **Field Name** | **Field Type**  |
| :------------- | :-------------- |
| streetAddress  | STRING          |
| city           | STRING          |
| state          | nullable STRING |
| country        | STRING          |
| postCode       | STRING          |

**Transaction:**

| **Field Name** | **Field Type** |
| :------------- | :------------- |
| bank           | STRING         |
| purchaseAmount | DOUBLE         |

It is defined like:

```python
import typing

class Purchase(typing.NamedTuple):
  user_id: str  # The id of the user who made the purchase.
  item_id: int  # The identifier of the item that was purchased.
  shipping_address: ShippingAddress  # The shipping address, a nested type.
  cost_cents: int  # The cost of the item
  transactions: typing.Sequence[Transaction]  # The transactions that paid for this purchase (a list, since the purchase might be spread out over multiple credit cards).

class ShippingAddress(typing.NamedTuple):
  street_address: str
  city: str
  state: typing.Optional[str]
  country: str
  postal_code: str

class Transaction(typing.NamedTuple):
  bank: str
  purchase_amount: float
```

This wraps an input tuple, assigning a name to each element and restricts it to a particular type.

```python
class Transaction(typing.NamedTuple):
  bank: str
  purchase_amount: float

pc = input | beam.Map(lambda ...).with_output_types(Transaction)
```

It can also be done directly without the need of creating a subclass

```python
input_pc = ... # {"bank": ..., "purchase_amount": ...}
output_pc = input_pc | beam.Select(bank=lambda item: item["bank"],
                                   purchase_amount=lambda item: item["purchase_amount"])
```

With a schema defined, certain elements can be retreived by name

```python
input_pc = ... # {"user_id": ...,"bank": ..., "purchase_amount": ...}
output_pc = input_pc | beam.Select("user_id")
```

