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

Applied to every element of the PCollection(s).

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

### GroupByKey

### CoGroupByKey

### Combine

### Flatten

### Partition

#### Add timestamp

[Add a timestamp](https://beam.apache.org/documentation/programming-guide/#adding-timestamps-to-a-pcollections-elements) to the elements of a PCollection.

```python
class AddTimestampDoFn(beam.DoFn):
  def process(self, element):
    # Extract the numeric Unix seconds-since-epoch timestamp to be
    # associated with the current log entry.
    unix_timestamp = extract_timestamp_from_log_entry(element)
    # Wrap and emit the current entry and new timestamp in a
    # TimestampedValue.
    yield beam.window.TimestampedValue(element, unix_timestamp)

timestamped_items = items | 'timestamp' >> beam.ParDo(AddTimestampDoFn())
```

