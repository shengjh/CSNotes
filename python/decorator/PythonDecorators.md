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

# Decorator 
## Decorator Function
### 1. Timing
```py
import functools
import time

def timer(func):
    """Print the runtime of the decorated function"""
    @functools.wraps(func)
    def wrapper_timer(*args, **kwargs):
        start_time = time.perf_counter()    # 1
        value = func(*args, **kwargs)
        end_time = time.perf_counter()      # 2
        run_time = end_time - start_time    # 3
        print(f"Finished {func.__name__!r} in {run_time:.4f} secs")
        return value
    return wrapper_timer

@timer
def waste_some_time(num_times):
    for _ in range(num_times):
        sum([i**2 for i in range(10000)])
```
### 2. Debugging Code
```py
import functools

def debug(func):
    """Print the function signature and return value"""
    @functools.wraps(func)
    def wrapper_debug(*args, **kwargs):
        args_repr = [repr(a) for a in args]                      # 1
        kwargs_repr = [f"{k}={v!r}" for k, v in kwargs.items()]  # 2
        signature = ", ".join(args_repr + kwargs_repr)           # 3
        print(f"Calling {func.__name__}({signature})")
        value = func(*args, **kwargs)
        print(f"{func.__name__!r} returned {value!r}")           # 4
        return value
    return wrapper_debug
```

### 3. Slowing Down Code
```py
import functools
import time

def slow_down(func):
    """Sleep 1 second before calling the function"""
    @functools.wraps(func)
    def wrapper_slow_down(*args, **kwargs):
        time.sleep(1)
        return func(*args, **kwargs)
    return wrapper_slow_down

@slow_down
def countdown(from_number):
    if from_number < 1:
        print("Liftoff!")
    else:
        print(from_number)
        countdown(from_number - 1)
```

### 4. Repeat times(With arguments)
```py
def repeat(_func=None, *, num_times=2):
    def decorator_repeat(func):
        @functools.wraps(func)
        def wrapper_repeat(*args, **kwargs):
            for _ in range(num_times):
                value = func(*args, **kwargs)
            return value
        return wrapper_repeat

    if _func is None:
        return decorator_repeat
    else:
        return decorator_repeat(_func)
```
```py
@repeat
def say_whee():
    print("Whee!")

@repeat(num_times=3)
def greet(name):
    print(f"Hello {name}")
```



## Decorator Class
### 1. decorators on classes is to decorate the whole class

```py
from dataclasses import dataclass

@dataclass
class PlayingCard:
    rank: str
    suit: str
```

## Nesting Decorators
 the decorators being executed in the order they are listed
 ```py
 from decorators import debug, do_twice

@debug
@do_twice
def greet(name):
    print(f"Hello {name}")
```
same to `debug(do_twice(greet()))`
