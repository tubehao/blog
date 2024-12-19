---

title: CS50P

published: 2024-10-24

description: 'rt'

image: ''

tags: [notes]

category: 'learn'

draft: false 

---

# CS50P

# filei/o

```
import csv

students = []

with open("students.csv") as file:
    reader = csv.reader(file)
    for row in reader:
        students.append({"name": row[0], "home": row[1]})

for student in sorted(students, key=lambda student: student["name"]):
    print(f"{student['name']} is from {student['home']}")
```

```
import csv

students = []

with open("students.csv") as file:
    reader = csv.DictReader(file)
    for row in reader:
        students.append({"name": row["name"], "home": row["home"]})

for student in sorted(students, key=lambda student: student["name"]):
    print(f"{student['name']} is in {student['home']}")
```

```
import csv

name = input("What's your name? ")
home = input("Where's your home? ")

with open("students.csv", "a") as file:
    writer = csv.DictWriter(file, fieldnames=["name", "home"])
    writer.writerow({"name": name, "home": home})
```

---

# libraries

random.shuffle 把列表随机排序

- `sys` is a module that allows us to take arguments at
the command line.
- `argv` is a function within
  the `sys` module that allows us to learn about what the user
  typed in at the command line. Notice how you will
  see `sys.argv` utilized in the code below. In the terminal
  window, type `code name.py`. In the text editor, code as
  follows: ``` import sys
  
    print(“hello, my name is”, sys.argv[1]) ```
  

---

# [APIs](https://cs50.harvard.edu/python/2022/notes/4/#apis)

- APIs or “application program interfaces” allow you to connect to
the code of others.
- `requests` is a package that allows your program to
behave as a web browser would.
- In your terminal, type `pip install requests`. Then,
type `code itunes.py`.
- It turns out that Apple iTunes has its own API that you can
access in your programs. In your internet browser, you can visit [https://itunes.apple.com/search?entity=song&limit=1&term=weezer](https://itunes.apple.com/search?entity=song&limit=1&term=weezer) and
a text file will be downloaded. David constructed this URL by reading
Apple’s API documentation. Notice how this query is looking for
a `song`, with a `limit` of one result, that
relates to the `term` called `weezer`. Looking at
this text file that is downloaded, you might find the format to be
similar to that we’ve programmed previously in Python.
- The format in the downloaded text file is called JSON, a
text-based format that is used to exchange text-based data between
applications. Literally, Apple is providing a JSON file that we could
interpret in our own Python program.
- In the terminal window, type `code itunes.py`. Code as
  follows:
  
    ```
    import requests
    import sys
    
    if len(sys.argv) != 2:
        sys.exit()
    
    response = requests.get("https://itunes.apple.com/search?entity=song&limit=1&term=" + sys.argv[1])
    print(response.json())
    ```
  
    Notice how the returned value of `requests.get` will be
    stored in `response`. David, having read the Apple
    documentation about this API, knows that what is returned is a JSON
    file. Running `python itunes.py weezer`, you will see the
    JSON file returned by Apple. However, the JSON response is converted by
    Python into a dictionary. Looking at the output, it can be quite
    dizzying!
  
- It turns out that Python has a built-in JSON library that can
  help us interpret the data received. Modify your code as follows:
  
    ```
    import json
    import requests
    import sys
    
    if len(sys.argv) != 2:
        sys.exit()
    
    response = requests.get("https://itunes.apple.com/search?entity=song&limit=1&term=" + sys.argv[1])
    print(json.dumps(response.json(), indent=2))
    ```
  
    Notice that `json.dumps` is implemented such that it
    utilizes `indent` to make the output more readable.
    Running `python itunes.py weezer`, you will see the same JSON
    file. However, this time, it is much more readable. Notice now that you
    will see a dictionary called `results` inside the output.
    Inside that dictionary called `results` there are numerous
    keys present. Look at the `trackName` value in the output.
    What track name do you see in your results?
  
- How could we simply output the name of just that track name?
  Modify your code as follows:
  
    ```
    import json
    import requests
    import sys
    
    if len(sys.argv) != 2:
        sys.exit()
    
    response = requests.get("https://itunes.apple.com/search?entity=song&limit=50&term=" + sys.argv[1])
    
    o = response.json()
    for result in o["results"]:
        print(result["trackName"])
    ```
  
    Notice how we are taking the result
    of `response.json()` and storing it in `o` (as in
    the lowercase letter). Then, we are iterating through
    the `results` in `o` and printing
    each `trackName`. Also notice how we have increased the limit
    number of results to `50`. Run your program. See the
    results.
  
- You can learn more about `requests` through the [library’s
documentation](https://docs.python-requests.org/).
- You can learn more about JSON in Python’s documentation of [JSON](https://docs.python.org/3/library/json.html).

---

# 正则表达式

```
.   any character except a new line
*   0 or more repetitions
+   1 or more repetitions
?   0 or 1 repetition
{m} m repetitions
{m,n} m-n repetitions
^   matches the start of the string
$   matches the end of the string or just before the newline at the end of the string
[]    set of characters
[^]   complementing the set

\d    decimal digit
\D    not a decimal digit
\s    whitespace characters
\S    not a whitespace character
\w    word character, as well as numbers and the underscore
\W    not a word character

A|B     either A or B
(...)   a group
(?:...) non-capturing version
```

一些例子： - `^abc`: 匹配以 “abc” 开头的行。 -
`123$`: 匹配以 “123” 结尾的行。 - `[0-9]+`:
匹配一个或多个数字。 - `(\d{3}-)?\d{4}`:
匹配可选的三位数区号，后面跟着四位数字。 - `\bword\b`:
匹配一个完整的单词 “word”。 - `(cat|dog)`: 匹配 “cat” 或
“dog”。 - `.*`: 匹配任意字符零次或多次（通配符）。 ``` import
re

email = input(“What’s your email?”).strip()

if re.search(r“[1](about:blank#fn1)+@[a-zA-Z0-9_].+.edu$”,
email): print(“Valid”) else: print(“Invalid”)

```

 Notice that `[a-zA-Z0-9_]` tells the validation that characters must be between `a` and `z`, between `A` and `Z`, between `0` and `9` and potentially include an `_` symbol. Testing the input, you’ll find that many potential user mistakes can be indicated.
Thankfully, common patterns have been built into regular expressions by hard-working programmers. In this case, you can modify your code as follows:

```

import re

email = input(“What’s your email?”).strip()

if re.search(r“^+@.+.edu$”, email): print(“Valid”) else:
print(“Invalid”) ```

Notice that `\w` is the same as `[a-zA-Z0-9_]`.
Thanks, hard-working programmers!

```
import re

name = input("What's your name? ").strip()
matches = re.search(r"^(.+), (.+)$", name)
if matches:
    last = matches.group(1)
    first = matches.group(2)
    name = f"{first} {last}"
print(f"hello, {name}")
```

```
import re

name = input("What's your name? ").strip()
if matches := re.search(r"^(.+), *(.+)$", name):
    name = matches.group(2) + " " + matches.group(1)
print(f"hello, {name}")
```

Notice how we combine two lines of our code. The
walrus `:=` operator assigns a value from right to left and
allows us to ask a boolean question at the same time. Turn your head
sideways and you’ll see why this is called a walrus operator.

```
class Student:
    def __init__(self, name, house):
        if not name:
            raise ValueError("Invalid name")
        if house not in ["Gryffindor", "Hufflepuff", "Ravenclaw", "Slytherin"]:
            raise ValueError("Invalid house")
        self.name = name
        self.house = house

    def __str__(self):
        return f"{self.name} from {self.house}"

def main():
    student = get_student()
    student.house = "Number Four, Privet Drive"
    print(student)

def get_student():
    name = input("Name: ")
    house = input("House: ")
    return Student(name, house)

if __name__ == "__main__":
    main()
```

---

# decorator

def a_new_decorator(a_func): ```

def wrapTheFunction(): print(“I am doing some boring work before
executing a_func()”)

```
a_func()

print("I am doing some boring work after executing a_func()")
```

return wrapTheFunction

def a_function_requiring_decoration(): print(“I am the function which
needs some decoration to remove my foul smell”)

a_function_requiring_decoration() ((outputs: “I am the function which
needs some decoration to remove my foul smell”

a_function_requiring_decoration =
a_new_decorator(a_function_requiring_decoration) ((now
a_function_requiring_decoration is wrapped by wrapTheFunction()

a_function_requiring_decoration() ((outputs: I am doing some boring
work before executing a_func() I am the function which needs some
decoration to remove my foul smell I am doing some boring work after
executing a_func()

```
你看明白了吗？我们刚刚应用了之前学习到的原理。这正是 python 中装饰器做的事情！它们封装一个函数，并且用这样或者那样的方式来修改它的行为。现在你也许疑惑，我们在代码里并没有使用 @ 符号？那只是一个简短的方式来生成一个被装饰的函数。这里是我们如何使用 @ 来运行之前的代码：

```

@a_new_decorator def
a_function_requiring_decoration(): “““Hey you! Decorate me!”“” print(“I
am the function which needs some decoration to” “remove my foul
smell”)

a_function_requiring_decoration() #outputs: I am doing some boring
work before executing a_func() # I am the function which needs some
decoration to remove my foul smell # I am doing some boring work after
executing a_func()

#the @a_new_decorator is just a short way
of saying: a_function_requiring_decoration =
a_new_decorator(a_function_requiring_decoration)

```
***

```

@a_new_decorator
a_fuction_requiring_decoration 即为 a_function_requiring_decoration =
a_new_decorator(a_function_requiring_decoration)

```
**装饰器是@后的函数是装饰器，它接受一个函数进行一定操作后再利用这个函数，其后跟的是它接受到的函数**
***
```

def decorated(*args, **kwargs): auth = request.authorization if not
auth or not check_auth(auth.username, auth.password): authenticate()
return f(*args, **kwargs)

```
- `*args`：是一个元组（tuple），用于接收任意数量的位置参数。当你在调用装饰后的函数时，传递的所有位置参数都会被收集到 `args` 中。在调用原始函数时，可以使用 `f(*args)` 来将这些位置参数传递给原始函数。

- `**kwargs`：是一个字典（dictionary），用于接收任意数量的关键字参数。当你在调用装饰后的函数时，传递的所有关键字参数都会被收集到 `kwargs` 中。在调用原始函数时，可以使用 `f(**kwargs)` 来将这些关键字参数传递给原始函数。

## 使用类装饰器
```

class email_logit(logit): ’’’
一个logit的实现版本，可以在函数调用时发送email给管理员 ’’’ def
**init**(self, email=‘admin@myproject.com’, *args,
**kwargs): self.email = email super(email_logit, self).__init__(*args,
**kwargs)

```
def notify(self):
    # 发送一封email到self.email
    # 这里就不做实现了
    pass
```

```

```

class MultiDecorator: def **init**(self, func):
self.func = func

```
def first_decorator(self):
    print("First decorator: Something before the function is called.")
    self.func()
    print("First decorator: Something after the function is called.")

def second_decorator(self):
    print("Second decorator: Something before the function is called.")
    self.func()
    print("Second decorator: Something after the function is called.")
```

@MultiDecorator def my_function():
print(“Inside my_function”)

my_function.first_decorator() print(“——”)
my_function.second_decorator()

```

***
## [Class Methods](https://cs50.harvard.edu/python/2022/notes/8/#class-methods)

- Sometimes, we want to add functionality to a class itself, not to instances of that class.
- `@classmethod` is a function that we can use to add functionality to a class as a whole.
- Here’s an example of _not_ using a class method. In your terminal window, type `code hat.py` and code as follows:

```
    import random
    
    class Hat:
        def __init__(self):
            self.houses = ["Gryffindor", "Hufflepuff", "Ravenclaw", "Slytherin"]
    
        def sort(self, name):
            print(name, "is in", random.choice(self.houses))
    
    hat = Hat()
    hat.sort("Harry")
    ```
    
    Notice how when we pass the name of the student to the sorting hat, it will tell us what house is assigned to the student. Notice that `hat = Hat()` instantiates a `hat`. The `sort` functionality is always handled by the _instance_ of the class `Hat`. By executing `hat.sort("Harry")`, we pass the name of the student to the `sort` method of the particular instance of `Hat`, which we’ve called `hat`.

- We may want, though, to run the `sort` function without creating a particular instance of the sorting hat (there’s only one, after all!). We can modify our code as follows:

    ```
    import random

    class Hat:

        houses = ["Gryffindor", "Hufflepuff", "Ravenclaw", "Slytherin"]

        @classmethod
        def sort(cls, name):
            print(name, "is in", random.choice(cls.houses))

    Hat.sort("Harry")
    ```

    Notice how the `__init__` method is removed because we don’t need to instantiate a hat anywhere in our code. `self`, therefore, is no longer relevant and is removed. We specify this `sort` as a `@classmethod`, replacing `self` with `cls`. Finally, notice how `Hat` is capitalized by convention near the end of this code, because this is the name of our class.

- Returning back to `students.py` we can modify our code as follows, addressing some missed opportunities related to `@classmethod`s:

    ```
    class Student:
        def __init__(self, name, house):
            self.name = name
            self.house = house
    
        def __str__(self):
            return f"{self.name} from {self.house}"
    
        @classmethod
        def get(cls):
            name = input("Name: ")
            house = input("House: ")
            return cls(name, house)
    
    def main():
        student = Student.get()
        print(student)
    
    if __name__ == "__main__":
        main()
    ```

    Notice that `get_student` is removed and a `@classmethod` called `get` is created. This method can now be called without having to create a student first.

***
函数内部需要声明global a才能操作a

***
# argparse
- Luckily, `argparse` is a library that handles all the parsing of complicated strings of command-line arguments. In the text editor window, code as follows:

    ```
    import argparse

    parser = argparse.ArgumentParser()
    parser.add_argument("-n")
    args = parser.parse_args()

    for _ in range(int(args.n)):
        print("meow")
    ```

    Notice how `argparse` is imported instead of `sys`. An object called `parser` is created from an `ArgumentParser` class. That class’s `add_argument` method is used to tell `argparse` what arguments we should expect from the user when they run our program. Finally, running the parser’s `parse_args` method ensures that all of the arguments have been included properly by the user.

- We can also program more cleanly, such that our user can get some information about the proper usage of our code when they fail to use the program correctly. In the text editor window, code as follows:

    ```
    import argparse

    parser = argparse.ArgumentParser(description="Meow like a cat")
    parser.add_argument("-n", help="number of times to meow")
    args = parser.parse_args()

    for _ in range(int(args.n)):
        print("meow")
    ```

    Notice how the user is provided some documentation. Specifically, a `help` argument is provided. Now, if the user executes `python meows.py --help` or `-h`, the user will be presented with some clues about how to use this program.

- We can further improve this program. In the text editor window, code as follows:

    ```
    import argparse

    parser = argparse.ArgumentParser(description="Meow like a cat")
    parser.add_argument("-n", default=1, help="number of times to meow", type=int)
    args = parser.parse_args()

    for _ in range(args.n):
        print("meow")
    ```

    Notice how not only is help documentation included, but you can provide a `default` value when no arguments are provided by the user.

- You can learn more in Python’s documentation of [`argparse`](https://docs.python.org/3/library/argparse.html).
***
# unpacking
To enable the possibility of passing the entire list, we can use unpacking. In the text editor window, code as follows:

```

def total(galleons, sickles, knuts): return (galleons * 17 + sickles)
* 29 + knuts

coins = [100, 50, 25]

print(total(*coins), “Knuts”)

```

Notice how a `*` unpacks the sequence of the list of coins and passes in each of its individual elements to `total`.

-
    Since the `total` function expects three arguments, we cannot pass in a dictionary. We can use unpacking to help with this. In the text editor window, code as follows:

    ```
    def total(galleons, sickles, knuts):
        return (galleons * 17 + sickles) * 29 + knuts
    
    coins = {"galleons": 100, "sickles": 50, "knuts": 25}
    
    print(total(**coins), "Knuts")
    ```
    
    Notice how `**` allows you to unpack a dictionary. When unpacking a dictionary, it provides both the keys and values.

***
# Args and kwargs
- args是位置参数，就像我们提供的print("Hello"， "World")那样。
- Kwargs是命名参数，或“关键字参数”，例如我们提供的打印参数，如print (end="")

```

def yell(words): uppercased = [] for word in words:
uppercased.append(word.upper()) print(*uppercased)

```
- The uppercase list is printed utilizing the `*` to unpack it.
***
# Map
`map` allows you to map a function to a sequence of values. In practice, we can code as follows:

```

def main(): yell(“This”, “is”, “CS50”)

def yell(*words): uppercased = map(str.upper, words)
print(*uppercased)

if **name** == “**main**”: main()

```

Notice how `map` takes two arguments. First, it takes a function we want applied to every element of a list. Second, it takes that list itself, to which we’ll apply the aforementioned function. Hence, all words in `words` will be handed to the `str.upper` function and returned to `uppercased`.

***
# list comprehension
```

uppercased = [arg.upper() for arg in words]

```

```

gryffindors = [ student[“name”] for student in students if
student[“house”] == “Gryffindor”]

```
## dictionary comprehension
```

students = [“Hermione”, “Harry”, “Ron”]

gryffindors = [{“name”: student, “house”: “Gryffindor”} for student
in students] print(gryffindors)

```
- We can even simplify further as follows:
```

students = [“Hermione”, “Harry”, “Ron”]

gryffindors = {student: “Gryffindor” for student in students}

print(gryffindors)

```
***
## [`filter`](https://cs50.harvard.edu/python/2022/notes/9/#filter)

- Using Python’s `filter` function allows us to return a subset of a sequence for which a certain condition is true.
- In the text editor window, code as follows:

    ```
    students = [
        {"name": "Hermione", "house": "Gryffindor"},
        {"name": "Harry", "house": "Gryffindor"},
        {"name": "Ron", "house": "Gryffindor"},
        {"name": "Draco", "house": "Slytherin"},
    ]
    
    def is_gryffindor(s):
        return s["house"] == "Gryffindor"
    
    gryffindors = filter(is_gryffindor, students)
    
    for gryffindor in sorted(gryffindors, key=lambda s: s["name"]):
        print(gryffindor["name"])
    ```

    Notice how a function called `is_gryffindor` is created. This is our filtering function that will take a student `s`, and return `True` or `False` depending on whether the student’s house is Gryffindor. You can see the new `filter` function takes two arguments. First, it takes the function that will be applied to each element in a sequence—in this case, `is_gryffindor`. Second, it takes the sequence to which it will apply the filtering function—in this case, `students`. In `gryffindors`, we should see only those students who are in Gryffindor.
- filter生成一个类似词典的东西让你查找是否符合条件
- `filter` can also use lambda functions as follows:

    ```
    students = [
        {"name": "Hermione", "house": "Gryffindor"},
        {"name": "Harry", "house": "Gryffindor"},
        {"name": "Ron", "house": "Gryffindor"},
        {"name": "Draco", "house": "Slytherin"},
    ]
    
    gryffindors = filter(lambda s: s["house"] == "Gryffindor", students)
    
    for gryffindor in sorted(gryffindors, key=lambda s: s["name"]):
        print(gryffindor["name"])
    ```
***
# enumeration
Utilizing enumeration, we can do

```

students = [“Hermione”, “Harry”, “Ron”]

for i, student in enumerate(students): print(i + 1, student) ```

---

1. a-zA-Z0-9_[↩︎](about:blank#fnref1)