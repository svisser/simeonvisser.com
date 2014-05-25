<!--
.. title: How to Mock Logging in Python
.. slug: how-to-mock-logging-in-python
.. date: 2013/04/13 22:08:00
.. tags: python, software
.. link:
.. description:
-->

As you become familiar with software testing and unit testing it can be 
necessary to assure yourself that information will be logged when
your software platform raises an exception or behaves unexpectedly.

In other words, you've developed a new feature and you want to make sure that certain
calls to Python's logging module are performed in certain cirumstances.

That's where the [mock](https://pypi.python.org/pypi/mock) module comes in:
it allows us to mock the Python logging module and assert that certain calls
are made. If you're using Python 3.3 (or higher) than you can import from the
`unittest.mock` ([documentation](http://docs.python.org/3/library/unittest.mock.html))
module rather than installing `mock` from PyPI.

### Example of mocking in unit tests

Let's say our code looks like this:

```python
import logging

def check_value(data_dict, value):
    try:
        return data_dict[value] > 10
    except KeyError:
        logging.warn("Data does not contain '%s'", value)
    return False
```

What we wish to test is that `logging.warn` is called when the `KeyError`
is raised. So in our unit test we call `check_value` with parameters in such
a way that the `KeyError` is indeed raised. For example:

```python
import unittest
from my_module import check_value

class MyUnitTest(unittest.TestCase):

    def test_check_value_logs_warning(self):
        check_value({}, 'key')
```

The idea behind mocking is as follows: instead of calling the real
`warn` function of the `logging` module we call a fake mocked version and
that allows us to keep track of how many times the function is
called (if at all).

We can do that using the `patch` function of the `mock` library: this
specifies what we wish to mock and what the name of the mocked object should be.

We can now update our test by writing:

```python
import unittest
from mock import patch
from my_module import check_value

class MyUnitTest(unittest.TestCase):

    @patch('my_module.logging')
    def test_check_value_logs_warning(self, mock_logging):
        check_value({}, 'key')
        self.assertTrue(mock_logging.warn.called)

if __name__ == '__main__':
    unittest.main()
```

This unit test passes as the assertion is indeed true. If you modify the
code in a way that it no longer logs a warning then you'll find that the
assertion in the unit test fails.

---
