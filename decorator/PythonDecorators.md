Python Decorators
================

# Functions
## what is functin?
**A function returns a value based on the given arguments**

```python
>>> def add_one(number):
...     return number + 1

>>> add_one(2)
3

```
Of course, it will returns None.

## Function is First-Class Objects
In Python, functions are first-class objects. This means that functions can be passed around and used as arguments.

For example:
```python
def say_hello(name):
    return f"Hello {name}"

def be_awesome(name):
    return f"Yo {name}, together we are the awesomest!"

def greet_bob(greeter_func):
    return greeter_func("Bob")


>>> greet_bob(say_hello)
'Hello Bob'

>>> greet_bob(be_awesome)
'Yo Bob, together we are the awesomest!'
```

## Inner Functions
It’s possible to **define functions inside other functions**.
```python
def parent():
    print("Printing from the parent() function")

    def first_child():
        print("Printing from the first_child() function")

    def second_child():
        print("Printing from the second_child() function")

    second_child()
    first_child()
```

When you call parent, The output will be as follows:
```python
>>> parent()
Printing from the parent() function
Printing from the second_child() function
Printing from the first_child() function
```

## Returning Functions From Functions
Python also allows you to **use functions as return values**.
```python
def parent(num):
    def first_child():
        return "Hi, I am Emma"

    def second_child():
        return "Call me Liam"

    if num == 1:
        return first_child
    else:
        return second_child
```
Here is a exampple:
```python
>>> first = parent(1)
>>> second = parent(2)

>>> first
<function parent.<locals>.first_child at 0x7f599f1e2e18>

>>> second
<function parent.<locals>.second_child at 0x7f599dad5268>

>>> first()
'Hi, I am Emma'

>>> second()
'Call me Liam'
```
