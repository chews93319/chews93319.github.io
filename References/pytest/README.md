---
sort: 1
---

# - pytest references

This page describes possible project standards for the implementation of tests using the Python `pytest` framework.

The intent is that this is a living document, so questions, comments, and revisions are welcome and encouraged.


## References

### official

[pytest website](https://docs.pytest.org/en/latest/index.html)

[pytest-mock website](https://github.com/pytest-dev/pytest-mock)

### tutorial websites

[PyBites Start Using Fixtures](https://pybit.es/pytest-fixtures.html)

### useful forum posts


## Examples of Running pytest

- Run pytest in discovery mode with verbose (-v, --verbose) output and show locals in tracebacks (-l, --showlocals)

```note
'show locals in tracebacks' will print all the local variables contained within a failed test.
```

```bash
cd ../<project directory>
python -m pytest -vl
```

- Run pytest for specific Test Container (Directory)

```bash
cd ../<project directory>/<path>/<to>/<test>/<container>/<parent>
python -m pytest -vl directory_name
```

- Run pytest for specific Test Case (Function)

```bash
cd ../<project directory>/<path>/<to>/<test>/<container>
python -m pytest -vl test_file_name::test_function
```

- Run pytest for specific Test Group (Class)

```bash
cd ../<project directory>/<path>/<to>/<tests>
python -m pytest -vl test_file_name::TestClass
```

- Run pytest for specific Case (Function) of a Test Group (Class)

```bash
cd ../<project directory>/<path>/<to>/<tests>
python -m pytest -vl test_file_name::TestClass::test_function
```

- Run pytest (with pytest-cov installed) to generate a line coverage report to stdout, as well as an html report

```bash
cd ../<project directory>
python -m pytest --cov=<DirOfModules> --cov-report=html
```

