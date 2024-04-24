1. Suppose someone runs this T-SQL batch script to insert three rows. After execution they get an error, how many rows will be created? INSERT INTO HumanResources.PossibleSkills (SkillName, Category, Credit) VALUES('Database Administration', 'IT Professional', 5);INSERT INTO HumanResources.PossibleSkills (SkillName, Category, Credit) VALUES('C#.NET', 'Developer', 4);INSERT INTO HumanResources.PossibleSkills (SkillName, Category, Credit) VALUES('Project Management', 'Management', 'Two');GO 

0

1

2
Correct. Two rows are created. There's no syntax error in this batch, so SQL can begin to execute the statement. The first two INSERT statements are executed before the error arises.

2. Someone wants to populate a table by creating 15 new rows. Before they create the rows, they need to check that the table exists. From the following T-SQL keywords, which one will they need to use? 

IF
Correct. They'll use the IF statement to check that the table exists before they begin inserting the rows.


WHILE

INSERT
3. Can a variable be declared in one batch and referenced multiple batches? 

Yes

No
Correct. Variables are local to the batch in which they're declared.


Sometimes