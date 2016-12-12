---
title: Intro to C#
tagline: Going from Java to C# in an evening
description: There are so many .NET jobs out there now..
---

I've been needing to pick up C# for a long time now. I've used it before, working with the Kinect, but never had a reason after that. It should be easy enough considering how close it is to Java, right?

# Basics

## Namespace

~~~csharp
namespace BunSharp {
	class MainClass {
		public static void Main(string[] args) {
			System.Console.WriteLine("Hello");
		}
	}
~~~

Files are namespaces now, with classes inside them. The `main` method is now capitalized. All classes, methods, and properties start with capital letters.


## Classes

~~~csharp
class Person
{
	private string name;
	private int age = 0;

	public string Name {
		get {
			if (name == null) { return "No Name"; }
			else { return name;}
		}
		set { name = value; }
	}

	public int Age {
		set { age = value; }
		get { return age;}
	}
}
~~~

Instead of classes being *protected* by default, they are now *internal*, which is the same thing in that they can only be accessed from the same package.

When importing, you use `using` instead of `import` and can't use the `.*` notation anymore.

Variables are declared in the same way except for:

* **boolean** is now `bool`
* **String** is now `string`
* **final** is now `const`
* **var**  is a form of type inference that lets the compiler substitute the type.

## Properties

Properties are a nice new way of handling variable access. When you have a variable, you create associated properties that have the same encapsulation as methods.

~~~csharp
private string name;
public string Name {
	get {
		if (name == null) { return "No Name"; }
		else { return name;}
	}
	set { name = value; }
}
~~~

Use as such.

~~~csharp
Joe.Name = "Joe";
System.Console.WriteLine(Joe.Name);
~~~


### Auto Properties

Instead of normally writing: 

~~~csharp
private string job;
public string Job {
	get{return job;}
	set{job = value;}
}
~~~

You can instead write: 

~~~csharp
public string Job { get; set; }
~~~

To make it only settable in the class, you could also make the set private:

~~~csharp
public string Job { get; private set; }
~~~

The variables are still private, but with public getters and setters created for you with minimal typing.


## Structs

I've missed structs. Here they're like tiny classes and  initialized with the `new` operator and treated like value types. They're instantiated on the stack rather than the heap and also passed by value.

~~~csharp
public struct Point
{
	public int x, y;

	public Point(int pX, int pY){
		x = pX;
		y = pY;
	}
}
	
var pointA = newPoint(1,2);
var pointB = pointA;	// B is a copy of A
pointB.x = 10			// A is unchanged
~~~


## Inheritance

Instead of *extending* or *implementing* as in Java, just list out the inherited classes after the class name separated by a `:`. Call the superclass's constructor with `base()` in a similar way.

~~~csharp
class Child : Person
{
	public Child() : base() { }
}
~~~


## Overriding

A big departure from Java, C# methods are not virtual and instead methods with the same name signature **hide** the superclass method. You **must** use the `virtual` and `override` keywords to actually override.

**Wrong**

~~~csharp
class Cat
{
	public void speak(){
		Console.WriteLine("meow");
	}
}

class Lion : Cat
{
	public void speak(){
		Console.WriteLine("roar");
	}
}

var lion = new Lion();
lion.speak(); // result: meow
~~~

**Right**

~~~csharp
class Cat
{
	public virtual void speak(){
		Console.WriteLine("meow");
	}
}

class Lion : Cat
{
	public override void speak(){
		Console.WriteLine("roar");
	}
}

var lion = new Lion();
lion.speak(); // result: roar
~~~


## Instantiating Classes

We don't need to overload constructors now! Just use any combination of properties, in any order, and let the object initializers do the work.

~~~csharp
// Good
var Joey = new Person();	// Implied constructor
var Joe = new Person { };	// Same as above, no variables passed in
var Sam = new Child { Age = 2, Name = "Sam" };	// Passing in variables, which use the appropriate properties
~~~

Now what if we had another subclass that does have a constructor? Then we'd need to use it.

~~~csharp
class Adult : Person
{
	public string Job { get; set; }

	public Adult(string job) : base()
	{
		this.Job = job;
	}
}

// Valid
var Adam = new Adult("Apple") { Name = "Adam",Age = 33};
var Tim = new Adult("NOPE");
// Invalid
var Tim = new Adult { Age = 22, Name = "Tim"};
~~~

# Other Bits

## LINQ

Lets us query and transform databases, XML, object graphs, and collections without embedding languages. 

Looks a lot like lambdas or list comprehensions in Python.

~~~csharp
int[] nums = {5, 4, 3, 9, 8, 7, 6};
var lownums = nums.Where(n =>( n > 5 && n < 8));
~~~


## Passing by Reference

* **out** - Get uninitialized variable and give a value.
* **ref** - Use an initialized variable.

~~~csharp
public void Assign(out string s) {
	s = new string("Hello");
}

public void Swap(ref string s, ref string r) {
	string temp = s;
	s = r;
	r = temp;
}
~~~

## Nullable Types

Make a primitive nullable by adding `?`.

~~~csharp
int? x = 5;
if (x.HasValue) {
	Console.WriteLine(x.Value);	// 5
}
x = null;
Console.WriteLine(x.GetValueOrDefault());	// 0
~~~

## Iterators

Basically like in Python, use `yield`.

~~~csharp
public static string[] fruit = { "banana", "apple", "orange", "pear", "grape" };
public static string[] veggies = { "lettuce", "peas", "carrots"};

public static IEnumerable FruitAndVeg {
	get{
		foreach (string f in fruit) { yield return f;}
		foreach (string v in veggies) { yield return v;}
	}
}
~~~


## Pre-Processing

Like in C/C++, we can define variables and sections that are evaluated before compilation.

~~~csharp
#define DEBUG

.......

elements(i) = CalculateElement(i);
#if DEBUG
log.info("elements: " + elements(i).ToString());
#endif
~~~


## Aliases

Like in C/C++, we can create aliases with keywords.

~~~csharp
using C = System.Console;

.......

C.WriteLine("output here");
~~~


# Quick Reference

**C# keyword**|**Java keyword**|**C# keyword**|**Java keyword**|**C# keyword**|**Java keyword**|**C#keyword**|**Javakeyword**
:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----
abstract|abstract|extern|native|operator|N/A|throw|throw
as|**N/A**|false|false|out|**N/A**|true|true
base|super|finally|finally|override|**N/A**|try|try
bool|boolean|fixed|**N/A**|params|…|typeof|**N/A**
break|break|float|float|partial|**N/A**|uint|**N/A**
byte|**N/A**|for|for|private|private|ulong|**N/A**
case|case|foreach|for|protected|**N/A**|unchecked|**N/A**
catch|catch|get|**N/A**|public|public|unsafe|**N/A**
char|char|goto|goto|readonly|**N/A**|ushort|**N/A**
checked|**N/A**|if|if|ref|**N/A**|using|import
class|class|implicit|**N/A**|return|return|value|**N/A**
const|const|in|**N/A**|sbyte|byte|virtual|**N/A**
continue|continue|int|int|sealed|final|void|void
decimal|**N/A**|interface|interface|set|**N/A**|volatile|volatile
default|default|internal|protected|short|short|where|extends
delegate|**N/A**|is|instanceof|sizeof|**N/A**|while|while
do|do|lock|synchronized|stackalloc|**N/A**|yield|**N/A**


----------

# References

* [TechUI](http://www.techhui.com/profiles/blogs/a-15-minute-intro-to-c-for)
* [Alex Naraghi](http://alexnaraghi.com/c-for-java-developers/)