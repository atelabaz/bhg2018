---
# Introductory Python Course
## Part 1
Dana Scarinci Zabaleta

---
## In this first part, we will
* Review basic Python (2.7)
 * Reading and writing files; Basic types: integers, floats, booleans; Strings; Lists and dictionaries; **For** loops; **If-else** statements; Functions

* Learn the pythonic way to do certain things
* Ask simple questions from a data set using this basic knowledge

---
### The data

![thedata](Python/data_preview.jpg)

---
### Reading files

[Docs](https://docs.python.org/2/tutorial/inputoutput.html#reading-and-writing-files)

### Reading
```{Python}
FILE = "titanic.tsv"
src = open(FILE, "r") # other modes: "w", "a"
header = src.readline() #read first line
data = src.readlines() # read rest
src.close()  # remeber to close your files!
```

### Writing
```{Python}
OUTPUT = "results.txt"
tgt = open(OUTPUT, "w")
tgt.write("This will be written in results.txt\n")
tgt.close() # don"t forget this!

```

---
### Let's read our file!

Check the **alternative and safer** syntax!

```{Python}
FILE = "titanic.tsv"

with open(FILE, "r") as src:
    header = src.readline() # read first line
    data = src.readlines() # read rest
```

---
### Lists
The **variable** *data* is storing a **list** of strings. Each string is a line of the original file.

Examples of lists

```{Python}
myList = ["a", "b", "c"]
myList2 = [3,5,7,9,12,34,5]


```

---
### Lists...

*  are ORDERED

```{Python}
myList[0] # access the first element
myList2[1:4] # access [5,7,9]
```
*  are MUTABLE

```{Python}
myList[0] = "w" # change the first element
```

*  can contain different types of objects

```{Python}
myList3 = [1, "Lola", 3.45, True]
```

---
### Up to now, we have:

* *header*: a **string** contanining the names of the columns
* *data*: a list of **strings**

But we would like to have a **dictionary** *dataDict*, where the *keys* are the columm names, and the values are *lists* of values.

---
### Why a dictionary?
Dictionaries are collections of *key:value* pairs

```{Python}
myDict = {"english":"en", "spanish:"sp", "french":"fr"}

dataDict = {"name":["passenger1", "passenger2",...],
            "survived":[1,0,0,1,..], "age":[24,35,7,58,...]}
```

---
### Dictionaries:

* are UNORDERED

```{Python}
myDict[1]  # WRONG! 
myDict["spanish"] # access "sp"
```

* are MUTABLE

```{Python}
myDict["spanish"] = "es"
```

* can contain different types of objects

```{Python}
myDict = {3:"three", "ninety eight":98, 5.6:"five point six"}
```

---
### Create an empty dictionary (1)
Let's get the column names from the string header
 
 * [strip()](https://docs.python.org/2/library/string.html#string.strip) is a string method that removes empty space from the ends of a string
 * [split()](https://docs.python.org/2/library/string.html#string.split) is a string method that turns a string into a list of elements

```{Python}
# turn string into list of column names
header = header.strip().split("\t") 
```

Now, how do we turn this into a dictionary?

---

### For loops
```{Python}
for i in [0,1,2,3,4,5,6,7]:
    print(i)

for i in range(7):
    print(i)
    
abc = ["a", "b", "c"]
for letter in abc:
    print(letter)

for i, letter in enumerate(abc):
    print(str(i) + " " + letter)
    
```

---
### Create an empty dictionary (2)
Create the dictionary the classic way

```{Python}
# using regular loops
dataDict = {}
for colName in header:
    dataDict[colName] = []
```

Or using the pythonic way, in one line!

```{Python}
# dictionary comprehensions exist too!
dataDict = {colName:[] for colName in header} 
```

[More on list comprehensions](https://docs.python.org/3/tutorial/datastructures.html#list-comprehensions)

---
### Populate the dictionary (1)
For each line in the *data* list, we want to

1. split it on \t
2. do something to empty cells
3. change numbers to appropriate data types
4. store each value in the appropriate dataDict key list.

---
### Populate the dictionary (2)

```{Python}
row = row.strip("\r\n")
row = row.split("\t") # now row is a list!
# missing data to None, string digits to floats
for i in range(len(row)):
    elem = row[i]
    if elem == "":
        row[i] = None
    elif elem.isdigit(): # another string method
        row[i] = float(elem)
```

---
### If - else

```{Python}
if expression:
    # do something
elif expression2:
    # do something else
else:
    # do something else
```
Only the first if statement is compulsory in an if - else expression.

What form can an expression take? It has be evaluated into a boolean value: True or False.

```{Python}
x == 3
x != 3
x < 3
x > 3

```
---
### Populate the dictionary (3)
Package the previous code into a function

```{Python}
def splitRow(row):
    row = row.strip("\r\n")
    row = row.split("\t") # now row is a list!
    # missing data to None, string digits to floats
    for i in range(len(row)):
        elem = row[i]
        if elem == "":
            row[i] = None
        elif elem.isdigit(): 
            row[i] = float(elem)
    return row
```

---
### Populate the dictionary (4)

```{Python}
for row in data:
    row = splitRow(row)

    for i in range(len(header)):
        dataDict[header[i]].append(row[i])
        
```

* [append()](https://docs.python.org/2/tutorial/datastructures.html#more-on-lists) is a list method that adds an element to the end of a list

---
# Now let's ask some questions

---

### Questions about a column
* What was the male to female ratio?
* How many passengers travelled in each class?

---
### Questions involving two columns
* What percentage of women survived? And of men?
* What percentage of passengers travelling on 3rd class survived? And in other classes?

---
### One column: number of passengers per class

```
classes = set(dataDict['Pclass'])
passengersByClass = {int(cl):0 for cl in classes}

for cl in classes:
    passengersByClass[cl] = dataDict['Pclass'].count(cl)
```

- Sets are **UNORDERED** collections of **unique** objects

```
mySet = {1,2,3}
```

- [count()](https://docs.python.org/2/tutorial/datastructures.html) is a list method that counts instances of an object inside a list.

---
### One column: number of passengers per class
Let's make it into a function

```
def totalByLevel(category):
    levels = set(dataDict[category])
    byCategory = {level:[] for level in levels}
    for level in levels:
        byCategory[level] = dataDict[category].count(level)
    return byCategory

```

---

### Two columns: % of survivors by class

```
def pctSurvival(category):
    # total number of passengers per group in that category
    total = totalByLevel(category)
    
    levels = total.keys()
    survivors = {level:0 for level in levels} # init counter
    
    for i, p in enumerate(dataDict["Survived"]):
        if p == 1:
            survivors[dataDict[category][i]] += 1
        
    print(category)
    print("==========")
    for level in levels:
        print(level)
        print("{}% survived".format(float(survivors[level]) / 
        total[level] * 100))
```

---
### String formatting

```
# The simplest exaple
yourName = "Peter"
"Hello, {}".format(yourName)

# dont's worry about types!
number = 3
"{} times {} equals {}".format(number, number, number*number)
```
[More on string formatting](https://docs.python.org/2/library/string.html#format-string-syntax)

---
### Scope and functions
#### Global scope

```{Python}
# we can ACCESS global variables from functions
PI = 3.14

def circumference(radius):
    print(2*PI*radius)

# we cannot CHANGE their value
def circumference(radius):
    PI = 3.1416
    print(PI)
    print(2*PI*radius)

r = 5.1
circumference(r)
print(PI)
print(PI * 2 * r)

# unless we declare them as global
def circumference(radius):
    global PI
    PI = 3.1416
    print(PI)
    print(2*PI*radius)

r = 5.1
circumference(r)
print(PI)
print(PI * 2 * r)

```

---
### Scope and functions
#### Local scope

```{Python}
# We cannot access variables declared inside functions (or loops)
# outside of them

def changeNum(num):
    step1 = num + 5
    step2 = step1 * 45
    return step2

changeNum(7)

step2 # NameError!


```

[More about scope](https://www.saltycrane.com/blog/2008/01/python-variable-scope-notes/)

---
### Scope and functions

It is general practice to:

- Define global variables in CAPITAL LETTERS
- Avoid the use of global variables

See the file *better.py* to see a better way to do the same thing we just did today!

---
### Tuples

Similar to lists, but IMMUTABLE

```
myTuple = (1,2,3)
```
[Learn more here](https://docs.python.org/3.3/tutorial/datastructures.html#tuples-and-sequences)

---
### Thank you!

```{Python}
for brainHacker in room:
    print("Thank you, {}!".format(brainHacker)
```
