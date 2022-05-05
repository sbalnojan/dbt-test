# DBT-test writing unit tests with ease in python
The basic usage pattern for writing dbt-tests is to:
1. Write your tests in python using the default library pytest, and run them 
with e.g. ``` pytest ```
2. Use dbt-test to execute dbt in the background on your test infrastructure 
(e.g. a dockerized postgres)
3. Use the tools built into dbt-test to verify that the dbt output meets 
your expectations.
4. Roll everything back using dbt-test at the end of the test. (Which usually dbt doesn't do)

To make this sort of testing really easy, dbt-test provides a bunch of 
helper functions for common tests such as "column exists", "table non-empty"
etc. The things that are a bunch of ugly lines of SQL.

To make it even easier, dbt-test supports & provides helper for two ways of developing tests:
1. Local mode (always preferred) should use a dockerized DB running on your machine to run tests & develop against. Dbt-test offers helper wrapper to set this up. 
2. default ... we recommend the first one...!!! Cloud mode (most likely needed for Snowflake/Redshift) - if you cannot run your DB locally, you'll need to test against a running DB instance. That's just as  
uses generated identifiers to then delete models...

## Example #1 "Hello World"
True, in TDD we start really minimal... Let's take this model: 
###### **`models/hello_world.sql`** 
```SQL
select "hello world"
```
We'd simply like to check that dbt works, this model works,
and that it produces one line. You can head over to
(https://github.com/sbalnojan/dbt-tdd-workflow) to see 
the dbt native test code for that. 

Manually you'd probably simply do a 
1. ``` dbt run ```
2. Check your local database for the resulting table.


Using dbt-test you can write python code that does 
just that.

###### **`dbt_tests/test_hello_world.py`** 
```python
import dbt_test as dt

def test_hello_world():

  dbt_test.setup() # runs dbt run inside a docker container.
  assert dbt_test.models.not_empty("hello_world"), 
  "Model hello world contains no entries."
  dbt_test.tear_down() # deletes the docker container.
```

Let's run this for fun
```shell 
python pytest
...
[dbt_test]== Docker Container Setup ...
[dbt_test]== Running dbt run ...
[dbt_test]== Running python pytest ...

....
```

## Example #1 "Hello World" in Docker Mode (Preferred, but needs extra setup)
dbt-test encourages you to have a proper local development environment on your machine. So if you're testing against postgres, you can use the script ... to launch a local postgres following your conventions.

## Example #1 "Hello World" in Cloud  Mode (Default)


## [WIP] Example #2 "Hello World from a source..."


Now seeds & replaces on the fly...

Replaces the   select * from {{ ref('raw_orders') }}
 with our test ref... on the fly...
 
 Test 1 should be though the one I wrote on the other side...
 Does this work at all?

## Related
I loosely based the package on 
- terratest
- [TestContainers](https://github.com/testcontainers/testcontainers-python)
- Here's a drop old stuff macro: https://discourse.getdbt.com/t/clean-your-warehouse-of-old-and-deprecated-models-snowflake/1547/5.
