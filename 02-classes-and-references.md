# Python Classes & References

## 1. Classes and objects
A **class** is a blueprint for creating objects. It defines attributes (data) and methods (behavior). An **object** is an instance of a class.
```python
class Cookie:
    pass

cookie = Cookie()
```

## 2. Attributes and methods
An **attribute** stores data belonging to an object. A **method** is a function defined inside a class.
```python
class Cookie:
    def __init__(self, color):
        self.color = color

    def eat(self):
        print("Eating cookie")

cookie = Cookie("green")
print(cookie.color)  # green
cookie.eat()
```

## 3. `__init__` and `self`
- `__init__` runs automatically when an object is created and initializes its attributes.
- `self` refers to the current object, allowing each object to hold its own state.

```python
class Cookie:
    def __init__(self, color):
        self.color = color

green_cookie = Cookie("green")
red_cookie = Cookie("red")
```

Conceptually:
```text
green_cookie ──→ Cookie object {color: "green"}
red_cookie   ──→ Cookie object {color: "red"}
```

## 4. Getters and setters
A **getter** reads a value; a **setter** changes it. Python's `@property` lets an attribute-like interface run getter/setter methods.
```python
class Cookie:
    def __init__(self, color):
        self._color = color

    @property
    def color(self):
        return self._color

    @color.setter
    def color(self, value):
        self._color = value

cookie = Cookie("green")
print(cookie.color)  # green (getter)
cookie.color = "red"  # setter
print(cookie.color)  # red
```
`_color` is a naming convention for an internal attribute; the underscore does not make it truly private.

```text
cookie.color
     ↓
   getter ──→ returns _color

cookie.color = "red"
     ↓
   setter ──→ updates _color
```

## 5. References in Python
Python variables hold **references to objects**. They are not manually manipulated raw pointers like in C or C++.
```python
a = 10
b = a
```
Conceptually, both names refer to the integer object `10`.
```text
a ──┐
    ├──→ 10
b ──┘
```

## 6. Immutable objects
An immutable object cannot be modified after creation. Common immutable types include `int`, `float`, `str`, and `tuple`.
```python
a = 10
b = a
a = 20
```
Reassigning `a` makes it refer to `20`; it does not change the integer `10` referenced by `b`.
```text
a ──→ 20
b ──→ 10
```

## 7. Mutable objects
A mutable object can be modified after creation. Common mutable types include `list`, `dict`, and `set`.
```python
a = {"color": "red"}
b = a
a["color"] = "blue"

print(a)  # {'color': 'blue'}
print(b)  # {'color': 'blue'}
```
Both variables refer to the same dictionary, so the mutation is visible through either reference.
```text
a ──┐
    ├──→ {"color": "blue"}
b ──┘
```

## 8. Reassignment vs. mutation
- **Reassignment** changes what a variable refers to.
- **Mutation** changes the object that is already being referenced.

```python
# Reassignment
a = 10
b = a
a = 20  # b still refers to 10

# Mutation
x = {"score": 1}
y = x
x["score"] = 2  # y sees the changed dictionary too
```

## 9. Why references matter in DSA
References allow one object to link to another object. This is the foundation of linked lists, trees, and many other data structures.
```text
[10 | next] ──→ [20 | next] ──→ [30 | None]
```
Each node's `next` attribute refers to the next node. The final node refers to `None`, meaning there is no next node.

## Quick recap
- A **class** is a blueprint; an **object** is an instance.
- **Attributes** store data; **methods** define behavior.
- `__init__` initializes an object; `self` refers to the current instance.
- Getters read values; setters update values.
- Python variables hold object references.
- Reassignment changes a reference; mutation changes the shared object.
- Understanding references is essential for linked lists, trees, and other DSA structures.