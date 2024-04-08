# What is this __all__ in python code and why can't I find its usage?
__all__ = ["SnowflakeSession"]

The __all__ attribute is a list that you can define within a module in Python. It's used to specify a list of 
names that should be exported when the module is imported using the from module import * syntax. 
This attribute serves as a way to control what names are considered public and accessible from outside the module.

When you import a module using the from module import * syntax, Python will only import names that are listed 
in the __all__ attribute of that module. This is a way to explicitly define which symbols are intended to be 
part of the module's public API.


# What is __future__ in python?
The from __future__ import annotations statement is used at the beginning of the file to enable annotations
as strings.

__future__ is a special module in Python that allows you to enable features from future versions of Python in 
your current code. This module is used to help with forward compatibility, allowing developers to write code 
that will work with upcoming versions of Python.

When you import features from __future__, you're essentially telling the Python interpreter to use certain
behavior or syntax that will become standard in future versions of Python, even if it's not the default 
behavior in the current version.

For example, in Python 3, print is a function rather than a statement like in Python 2. To use this behavior 
in Python 2, you would import it from __future__:



# What does the triple dot mean in (callable[..., ConnectionWrapper])

In Python type hints, the ... syntax within the parentheses of a function signature indicates that the 
function can accept any number of additional positional arguments of any type.

So, (callable[..., ConnectionWrapper]) means that the builder parameter of the register method should be a
callable (i.e., a function or any callable object) that can accept any number of additional positional arguments
of any type, and it should return a ConnectionWrapper instance.
