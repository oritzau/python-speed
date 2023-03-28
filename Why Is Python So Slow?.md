
### If you've used Python for anything more than a simple project, you've probably asked this exact question.

**Isn't this written in C? I thought C was fast...**

What happens when we instantiate x in this Python snippet? 

```python
# Bear with me, there's some strange stuff going on here behind the curtain
x = 12
```

Let's look at C, the language Python is entirely written in:

```c
int main() {
	int x = 12;
	// Using a reference, we can see the exact memory address of x
	// Don't get bogged down in the syntax, it's not important for this example
	int* x_ptr = &x;
	// x_ptr now has a value that we recognize as an address, like 0x7ffdc00a948c
	return 0;
}
```

#### C
address | value
--- | ---
0x7ffdc00a948c | 12

### Python

Our variable x does not exist as a named space in memory, instead x points to an Object with values that look something like this:
address | type | value | refcount
--- | --- | --- | ---
0x7ffdc00a948c | integer | 12 | 1

#### So how does that make things tens of times slower?

##### In shortest terms, it doesn't. Python's insistence on having everything be an object gives us superpowers, such as:
- Dynamic typing / type inference
- Shared mutability
- Readable and lightweight syntax
- Garbage collection

Each of these features comes with a cost, especially speed.

## Dynamic Typing / Type Inference

Unlike its parent language C, Python is [interpreted](https://www.freecodecamp.org/news/compiled-versus-interpreted-languages/). This means that Python is compiled down to non platform-dependent bytecode which is then executed by the Python interpreter. This gives us the ability to write and debug line by line and deploy code across multiple systems much faster, but significantly raises runtime due to the intermittent code (also called bytecode) executing significantly slower. 

## Shared Mutability

In Python, multiple functions, methods, or even assignment statements can share a reference to a single variable. This is achieved by instantiating every variable as an object with a mutable type parameter and mutable value, while also maintaining a reference count. This added complexity not only increase memory requirements, but also runtime as extra work (determining type, value, and reference count) need to be done.

## Readable and Lightweight Syntax

Python is famous for readability, especially when compared to more boilerplate heavy languages such as Java.

```java
class VeryVerbose {
	public static Boolean comparer(int num1, int num2) {
		return num1 > num2;
	}
	public static void sayHello(String s) {
		System.out.println("Hello, " + s);
	}
}
```

Compare this with a similar class in Python:

```python
class short_and_sweet:
	def compare(num1, num2):
		return num1 > num2

	def say_hello(s):
		print(s)
```

Most notably, Python removes the necessity for static typing, which allows for more readable and human language like code. Once again, these things must be calculated at runtime, costing Python once more in the speed department.

## Garbage Collection

If this is your first foray into how Python's behind the scenes magic, I would highly recommend checking out [Stackify's excellent article](https://stackify.com/python-garbage-collection/) on reference counting, the method Python uses for garbage collection.

Let's explore a simple example:

```python
def some_func():
	a = "Hey" # a is instantiated, with a refcount of 1
	my_dict = {}
	my_dict[a] = "something" # another object refers to a, the refcount goes up
	some_other_func(a) # a function (treated as a first class object in Python) takes a reference to a as well, now refcount is at 3
	return 0
```

Outside of this function, 'a' no longer has any meaning, the refcount is dropped to zero as a and my_dict go out of scope and some_other_func resolves. Now that the refcount is at zero, the garbage collector will free the memory given to 'a'.

Garbage collection can speed up development time and makes programming significantly more accessible, but reduces the ability of the developer to finely tune their program and manage it's exact behavior.

## Summary:

##### It's easy to look at Python's speed (or lack thereof) and dismiss it as a disfunctional or beginner language, but there is power in ease of development and accessibility.

Python is the number one lanuage for machine learning, data science, and other fields involving statistics. It's a powerful tool with incredible features, features which come at a hefty price in speed, efficiency, and control.

#### Quick note for those curious:

"Type annotations", supported in Python3.10 and onward, are literally nothing but fancy comments.

This function:
```python
def open_schrodingers_box(box: Box) -> Optional[Cat]:
	if box.cat == None:
		return None
	return box.cat
```

Will run exactly the same as this one:
```python
def open_schrodingers_box(box):
	if box.cat == None:
		return None
	return box.cat
```

Type annotations do not create a static type environment, nor will they do work for the interpreter ahead of time.