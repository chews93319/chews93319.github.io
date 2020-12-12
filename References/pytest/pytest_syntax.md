---
sort: 1
---

# pytest Syntax


## Formatting

#### Whitespace and Blank Lines

No space between a decorator and test case function signature.

Surround unique test function definitions with two blank lines [Pep8 Blank Lines](https://www.python.org/dev/peps/pep-0008/#blank-lines)

Method definitions inside a test function are surrounded by a single blank line [Pep8 Blank Lines](https://www.python.org/dev/peps/pep-0008/#blank-lines)

#### Comments

Each test case should contain a docstring to explain:
1. the intent and functionality of the target code under test
2. the details of assertion set and the reason for the criteria

Fixtures should have docstrings to explain benefits and interface details, especially if the fixture is dynamic and provides additional functionality.

#### Test Case Structure

Basic structure of a unit test case should follow the Arrange-Act-Assert model:

1. **Arrange**, or set up, the conditions for the test
2. **Act** by calling some function or method
3. **Assert** that some end condition is true



## Examples and Explanations

### Basic pytest Unit Test Case

Given the following target module:

Content of `../project_root/src/my_math.py`

```python
def adder(i, j):

    return i + j
```

The test might be as follows:

Content of `../project_root/tests/unit/my_math_tests/test_adder.py`

```python
import pytest
import my_math

def test_adder_returns_sum_of_inputs():
    '''
    expected: adder should return the sum of provided values
    assertions: the result should match the independently calculated sum
    '''
    # Arrange
    input1 = 13
    input2 = 29

    # Act
    result = my_math.adder(input1, input2)

    # Assert
    assert result == input1 + input2
```

### Skip a Test Case

Specific test cases can be skipped by using a decorator immediately above the test case signature.
It is best to include an explicit reason for why the test is being skipped.

Content of `../project_root/tests/unit/my_math_tests/test_adder.py`

```python
import pytest
import my_math

@pytest.mark.skip(reason='deprecated test')
def test_adder_returns_12():
    '''
    expected: returns sum of 7+5
    assertions: the result is 12
    '''
    # Arrange

    # Act
    result = my_math.adder(7,5)

    # Assert
    assert result == 12
```

A conditional `skipif` decorator is available to skip a test case if a certain condition is met, which is useful when testing across platforms and/or specific environments.


### Parameterized Inputs for a Test Case

`pytest` can run multiple iterations of a test using specified values from a List.

```note
Parameterized variables are defined in the decorator and identified in the test case signature
```

Content of `../project_root/tests/unit/my_math_tests/test_adder.py`

```python
import pytest
import my_math

@pytest.mark.parametrize('input1, input2', [
    (0,5),
    (18,32),
    (-8,27),
    (12,-19),
    (-10,-5)
])
def test_adder_return_input_add_5(input1,input2):
    '''
    expected: adder should return the sum of provided values
    assertions: the result should match the independently calculated sum
    '''
    # Arrange

    # Act
    result = my_math.adder(input1, input2)

    # Assert
    assert result == input1 + input2
```

### Patching Target Code Dependencies with `pytest` decorators

Content of `out_of_scope.py` within `<project dir>/src`

```python
def demo_function():
    return "out of scope response"
```

Content of `target_code.py` within `<project dir>/src`

```python
from out_of_scope import *

def use_the_function():
    result = out_of_scope.demo_function()
    print(result)

def main():
    use_simple_function()
    use_simple_function()
    use_simple_function()

if __name__ == '__main__':
    main()
```

Content of `test_main.py` within `<project dir>/tests/unit/target_code_tests`

```python
import pytest
from mock import Mock, patch
from target_code import *

# hard-coded response
@patch('target_code.demo_function')
def test_mock_hardcoded_response(mock_simple_func):
    mock_simple_func.return_value = "\n" + "hardcoded mocked response" + "\n"

    main()
    # prints the hardcoded response 3x


# interated response
@patch('target_code.demo_function')
def test_mock_scripted_response(mock_simple_func):
   script = [
       "\n" + "first mocked response" + "\n",
       "\n" + "second mocked response" + "\n",
       "\n" + "third mocked response" + "\n",
   ]

   mock_simple_func.side_effect = script

   main()
    # prints the iterated responses


# function response
@patch('target_code.demo_function')
def test_mock_programmatic_response(mock_simple_func):

    counter = [1]
    def new_responses():
        value = counter.pop()
        msg = "\n" + "function returned response #" + str(value) + "\n"
        counter.append(value + 1)
        return msg

    mock_simple_func.side_effect = new_responses

    main()
    # prints the dynamic responses
```

### Patching Target Code Dependencies with `mocker` fixture

```note
WIP: refactor test case above with mocker fixture
```

### Patching Target Code Dependencies with `monkeypatch` fixture

```python
def test_monkeypatch_response(monkeypatch):
    monkeypatch.setattr('target_code.demo_function', lambda: '\nmonkeypatch response\n')
    main()
    # prints the hardcoded monkeypatch response
```

## Using Custom Fixtures

```note
WIP: Review and correct example of random number generator fixture
```
Custom fixtures can be implemented within the test file directly, within dedicated Fixture definitions files and imported, or within a shared `conftest.py` file ( See [guidelines](guidelines.md) ).
All of the custom fixtures within a shared `conftest.py` are automatically imported and available for use in neighboring Test files.

In order to use a pytest fixture, include the name of the fixture as a parameter in the Test Case signature. (Before any Parameterized variable names)

Content of `conftest.py`

```python
from __future__ import print_function
import pytest
import random

@pytest.fixture(scope='module')
def random_integer():
    # Sets a random value for entire test suite
    return random.randint(100,200)

@pytest.fixture(scope='function')
def random_generator():
    # Provides a function that will generate unique values each call
    print('\n' + 'Setup of fixture')
    def _generator():
        return random.randint(0,100)
    yield _generator
    print('\n' + 'Teardown of fixture')
```

Content of test_my_math.py

```python
import pytest
import my_math


def test_adder_return_sum_of_randomconstant(random_generator, random_integer):
    module_constant = random_integer
    random_integer = random_generator()
    result = my_math.adder(module_constant, random_integer)
    assert result == module_constant + random_integer
```

Fixtures will also handle specific teardown code, which will run after each test. To do this, the keyword `yield` is utilized to mark the fixture return value prior to any required teardown steps.

Get the docstrings from all conftest.py custom fixtures:

```python
pytest --fixtures conftest.py
```
