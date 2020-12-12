---
sort: 1
---

# - Testing Guidelines

This is a collection of guidelines and philosophies to consider when creating test cases.
Though exceptions may apply, the intent of these ideas should be considered carefully.

## Check functionality not Syntax

Tests should verify or validate **behavior, logic, and functionality** of the target code rather than the specific **syntax or implementation**.

A unit test should validate the **response or logic** of the target code, which results from a specific input set or condition criteria.

The target code's scope of functionality should be clearly understood and described by the target code docstring.
Likewise, a test case should clearly describe the response or results to be verified.

## Tests are always safe to run

Each test should be able to be run alone or within the test suite, regardless of run order, which means that any required test data or enivornment setup should be created and cleared for each test.

An exception may be considered if a large data set, which takes time to create, is applicable for multiple tests.
In this case, it would be best for each test case to revert any data set changes, so that the data set is the same as before the test executed.

```note
Refer to `Setup()` and `Teardown()` methods for `unittest`.

Refer to `fixtures` for `pytest`.
```

## One Assertion Set per Test Case

A single test case should verify or validate a unique defined response or result set, which utilize a single `assert` or multiple.

Subsequently, multiple test cases may be required for some target code because of the multiple responses or result sets that can be defined.
The benefits of focused and dedicated test cases include:
- easier to read, write, and review test cases
- demonstrating the unique capabilities of the target code
- identifying refactoring opportunities when target code is too complicated

Test cases may leverage input parameterization to increase **CONDITION** coverage when multiple input sets would yield the same unique response or result set.

( TDD Benefit: A Dev will think about 1) the result set, 2) building the test case, then 3) implementing the target code. )

A test case name should clearly describe the case under test and the test case docstring should explain the result set being verified.

## Avoid conditional `assert` statements

The test intent, assertion set, and testing method should be clearly presented and readable without a lot of logical complexity.

Test cases should assist future contributors to easily review, understand, and refactor the target code and test cases without much support.

## Mock/Stub/Patch target code dependencies

Since a test case should validate specific functionality within the scope of the target code, replacing target code dependencies, which are out of scope, allow the test case to fully explore and exercise the functionality of the target code.

By mocking/patching behavior of dependencies, a test can explore any possible output/response from the dependent function call or external module, thereby, removing any testing limitation related to 'forcing the dependency to behave differently'.

Mocking target code dependencies enable test cases to verify edge-case result sets by temporarily providing mocked dependency condition criteria.


#### Mock details

`mock` is a module for testing in Python.
It enables a test case to replace some external code behavior or response, so that the target code functionality can be verified.
Specifically, mocked functions and objects are replaced with instances of "mock objects", which are instances of the `Mock` or `MagicMock` class.

```note
`pytest-mock` imports the original `unittest` Mock library, so all the the [original documentation](https://docs.python.org/3/library/unittest.html) applies
```

The differnece between the Mock and MagicMock class is that Mock is a completely generic object and MagicMock is a specialized Mock with ["Magic methods"](https://docs.python.org/3/library/unittest.mock.html#mocking-magic-methods).

It is possible to `assert` that a Mock instance was called or used in the intended way, either by number of times called or by a detailed call history list.

More importantly, a Mock can be configured to return static values, sequential values from an iterable, dynamic values from a function, or further 'mock objects'.

#### Patch/Stup details

Using the module `Mocker`, enables convenient replacement (patching) of specific functions, methods, or object attributes.

Additionally, a test case can completely disable (stub) an undesirable behavior, so that the test case can run through the target code, but the real behavior will not affect anything.

Another module, `Monkeypatch`, provides convenient syntax for small scale and simple behavior replacement, as well as, environment variable patching and dictionary value patching.

```note
A Test case should verify the target code functionality, **not** the functionality of an external dependency.
```

## Test File Organization

Use a dedicated directory to store the test files, which is adjacent to the source directory of target files.

Consider creating separate directories within the `test` directory for different test types ( e.g. Unit, Integration, WhiteBox, BlackBox ).

For each target file to test, create a dedicated folder as a 'test container' for separate test files per function or class method.
Using a 'test container' directory with `pytest` will allow for a local dedicated set of `conftest.py` fixtures, which can be available for use within all of the test files within the 'test container'.

```note
Refer to `fixtures` for `pytest`.
```

```bash
../<project_root>
├── src
│   ├── __init__.py
│   ├── module_1.py
│   :
│   └── module_N.py
├── tests
│   ├── __init__.py
│   ├── conftest.py
│   ├── integration
│   |   ├── __init__.py
│   |   ├── conftest.py
│   |   └── README.md
│   └── unit
│       ├── __init__.py
│       ├── conftest.py
│       ├── misc_tests
│       │   ├── __init__.py
│       |   ├── test_random.py
│       │   :
│       │   └── test_random_N.py
│       ├── module1_tests
│       │   ├── __init__.py
│       │   ├── conftest.py
│       │   ├── test_function_1.py
│       │   :
│       │   └── test_function_N.py
│       :
│       ├── moduleN_tests
│       │   ├── __init__.py
│       │   ├── conftest.py
│       │   ├── test_classmethod_1.py
│       │   :
│       │   └── test_classmethod_N.py
│       └── README.md
└── README.md
```

```note
The `__init__.py` files within the tests direcory tree are blank files that help `pytest` auto-discovery navigate and apply `conftest.py` fixture scope
```

## Fixtures for `pytest`

Fixtures are functions, which will run before each test case that calls it.
Fixtures help by preparing possible test data, implementing some functionality, or setting some importants connections, mocks, or stubs.
Instead of repeating sections of code for every test, a test case can identify a fixture name in the test case signature and reference it internally.

In addition to automatically loading the `conftest.py` fixtures contained within the parent `../tests` directory, test files will also automatically load a local `conftest.py` file within the test container directory.
A local `conftest.py` file can provide fixtures specifically for testing the specific target file.

#### References for built-in fixtures

[pytest fixtures documentation](https://docs.pytest.org/en/latest/fixture.html)

[pytest API Reference](https://docs.pytest.org/en/latest/reference.html#reference)


## Naming Conventions

### Test Container Directory

The test container directory name should match the name of the target file under test with the suffix `_tests`

Example directory name for target file under test: `../target_file_tests/`

### Test File Name

Each test file name must begin with the prefix `test_` and end with the extension `.py`.

The main portion of each test file name should match the function or class, which is under test.

Example file name: `test_target_classmethod.py`

### Test Group (class) Name

Within `pytest`, it is possible to group tests together for reporting organization and defining group specific supporting functions and variables.

A Test Group (class) name should describe the reason for grouping test cases.

The class name should start with the prefix `Test`.

The remainder of the class name should be written in `CamelCase`.

Example class name: `TestUsingCommonFixture`

### Test Case Name

Each test case name must begin with the prefix `test_`.

The remainder of the test case name should be a unique descriptor of the test functionality, written in `snake_case`.

Example test case name: `test_summary_of_intent`

### Fixture Names

Within `pytest`, fixtures are specifically decorated functions, which prepare and clean up test data, criteria, and environment.

```note
WIP Items
- should the names of fixtures within the project/tests conftest.py file have a convention?
- should the names of fixtures within the local test suite conftest.py file have a convention?
- should the names of unique fixture definition files have a convention? (eg. local_test_data.py)?
```

### Factory Names

```note
- should specific types of fixtures have naming conventions?
- the prefix is `factory_`
- the stem is the target object name in 'snake case'
```
