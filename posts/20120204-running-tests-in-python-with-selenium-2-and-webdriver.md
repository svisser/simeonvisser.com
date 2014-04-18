<!--
.. title: Running Tests in Python with Selenium 2 and WebDriver
.. slug: running-tests-in-python-with-selenium-2-and-webdriver
.. date: 2012/02/04 11:00:00
.. tags: python, software
.. link:
.. description:
-->

### Why use Selenium?

If your website or web application is growing beyond a certain size, it is no longer feasible to
manually test everything. There are simply too many features and edge case scenarios
to keep clicking through manually. This is where automated testing software comes into view.

Selenium is a set of tools to automatically test your web project in multiple
browsers. You can let it click on butttons, enter text in forms and browse your website to see
if everything is still behaving as it should.

### The approach

In this tutorial we are going to use [Selenium WebDriver](http://seleniumhq.org/) and Python to write our
tests.

The basic idea is:

* Launch Selenium
* Write some unit tests in Python
* Execute the tests in one or browsers

We'll run the tests on Firefox but the approach below can be scaled to multiple browsers, running
both locally and in virtual machines.

Before we continue, we'll need to discuss some Selenium terminology:

* **webdriver**: a webdriver instance of Selenium that runs the tests for a particular browser configuration.
For example, you can run tests for Firefox or for a particular version of Internet Explorer.
* **hub**: the hub is the Selenium server which handles communication to all the webdrivers.
Each of the webdrivers registers itself with the hub so the hub knows which browser
configurations are available for testing.

### Install Selenium

First of all, download the [stand-alone Selenium server](http://code.google.com/p/selenium/downloads/list).
You'll need to put this .jar file on all the computers or virtual machines that you're going to
use for automated testing.

The stand-alone server can run as a hub or as a remote depending on the parameters that you pass
upon launching it. Obviously, you'll only need one hub and at least one webdriver to run your tests.

For the Python test scripts, install the
[Selenium binding](http://pypi.python.org/pypi/selenium) from PyPI. To check that
you've installed it correctly, run the python command and type:

<pre>import selenium</pre>

If you get no errors then you have installed it correctly.

### Running Selenium

Starting a hub is pretty straightforward:

<pre>
java -jar selenium-server-standalone-2.18.0.jar -host &lt;IP address&gt; -port 4444 -role hub
</pre>

Starting a webdriver:

<pre>
java -jar selenium-server-standalone-2.18.0.jar -host &lt;IP address&gt; -port 5555 -role webdriver -hub http://&lt;IP address of hub&gt;:4444/grid/register -browser browserName=firefox,platform=MAC
</pre>

In the above commands the _IP address_ is where the hub or webdriver runs.

You don't necessarily need to pass all the parameters (for example, Selenium uses port 4444 by default
for the hub) but I think it's better to be explicit.

The **-role parameter** specifies whether a hub or a webdriver is being launched.

The **-hub parameter** is needed for webdrivers and it specifies the registration URL to the hub.

The **-browser parameter** is used to specify the browser configuration. These details will be useful
later on to ask the hub if we can run the tests on a particular browser. This means we
don't need to know where the webdriver instances are running, we can simply ask the hub to run the
tests on, e.g., Firefox and it will handle the rest for us.

You can visit the following URL to view the status of the hub and the registered webdrivers:

<pre>
http://&lt;IP address of hub&gt;:4444/grid/console
</pre>

### Writing Python tests

For the Python tests we use the [unittest](http://docs.python.org/library/unittest.html) module
from the Python Standard Library.

<pre>
from selenium import webdriver
import unittest
import sys

class ExampleTestCase(unittest.TestCase):
    
    def setUp(self):
        self.driver = webdriver.Remote(desired_capabilities={
            "browserName": "firefox",
            "platform": "MAC",
        })
    
    def test_example(self):
        self.driver.get("http://www.google.com")
        self.assertEqual(self.driver.title, "Google")

    def tearDown(self):
        self.driver.quit()

if __name__ == "__main__":
    unittest.main()
</pre>

### Running Python tests

You can now simply run the tests with the following command:

<pre>python filename.py</pre>

where filename.py is the the file with the above Python code. It may take
a while for Selenium to open the browser window and execute your commands but
after a while it should print the test results in the console. Selenium loads
the Google homepage and checks whether the title is "Google".

### Multiple browsers and multiple platforms

The above approach is suitable for testing your website in a particular
browser but the real benefit comes from testing it in multiple browsers on multiple
operating systems.

The browser configuration is specified in the setUp method
of the test case. You could create some functionality (for example, by turning
the above script into a script with some command-line parameters) to run the test case
multiple times with different browser configurations.

You could specify the capabilities as part of the test case itself:

<pre>
class ExampleTestCase(unittest.TestCase):
    capabilities = None
</pre>

which means you can instantiate the driver as follows: 

<pre>
self.driver = webdriver.Remote(desired_capabilities=self.capabilities)
</pre>

Lastly, you need to pass the command-line parameters to the test case before
running it:

<pre>
if __name__ == "__main__":
    ExampleTestCase.capabilities = {
        "browserName": sys.argv[1],
        "platform": sys.argv[2],
    }
    unittest.main()
</pre>

You can now launch multiple Selenium drivers and run the same test code for
multiple browsers and platforms:

<pre>
python filename.py firefox MAC
python filename.py firefox LINUX
</pre>
