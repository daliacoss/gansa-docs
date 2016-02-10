title: Installation
__block__: content
sections: Requirements for all systems
          Anything but Windows
          Windows

Installation
============

Requirements for all systems
----------------------------

In order to install Gansa, the following must already be installed on your system:

* [Python 2.7 or higher](https://www.python.org/)
* [setuptools](https://pypi.python.org/pypi/setuptools)

Anything but Windows
--------------------

1. [Download the source code](https://github.com/deckycoss/gansa).
2. From the command line, navigate to the source code directory and enter: `python setup.py install`.
    * You may need to run this command from root, which generally looks like this: `sudo python setup.py install`.

Windows
-------

Gansa consists of two main components: a library and an executable script. On Windows, these must be installed separately.

### Installing the library ###

1. [Download the source code](https://github.com/deckycoss/gansa).
2. From the command line, navigate to the source code directory and enter: `python setup.py install`.

### Installing the script ###

There are two methods for installing the script:

* Copy bin/gansa to your Python scripts folder (generally C:\Python\Scripts), and rename the new copy to gansa.py; or
* Copy bin/gansa and bin/gansa.bat to your Python scripts folder.

The first method will work only if your system has associated .py files with Python. For more information, consult the [official Python Windows FAQ](https://docs.python.org/2/faq/windows.html#how-do-i-make-python-scripts-executable).