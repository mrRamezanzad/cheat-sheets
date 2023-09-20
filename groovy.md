# Groovy is an object oriented language which is based on Java platform

## Features of Groovy
- Support for both static and dynamic typing.
- Support for operator overloading.
- Native syntax for lists and associative arrays.
- Native support for regular expressions.
- Native support for various markup languages such as XML and HTML.
- Groovy is simple for Java developers since the syntax for Java and Groovy are very similar.
- You can use existing Java libraries.
- Groovy extends the java.lang.Object.

> There is no need for semicolons
> There is no need for parenthesis

```java
  public class Demo {
    public static void main(String[] args) {
        System.out.println("Hello World");
    }
  }
```

```groovy
  println "Hello World."
```

-  Groovy supports Dynamic Typing

```groovy
    def x = 104
    println x.getClass() // getClass return class of it like output : class java.lang.Integer
    x = "Guru99"
    println x.getClass() // output: class java.lang.String
```

> You can still variable types like byte, short, int, long, etc with Groovy. But you cannot dynamically change the variable type as you have explicitly declared it.
```groovy
    int x = 104
    println x
    x = "Guru99" // returns error
```


- multi line string 
```groovy
    def x = """Groovy
    at
    Guru99"""
    println x
```

- loops (for loop)
```groovy
    0.upto(4) {println "$it"} // $it is a closure that gives the value of the current loop
    // output: 2 3 4
```

> You can also use the “times” keyword to get the same output
```groovy
    5.times{println "$it"} // output: 2 3 4
```

```groovy
    0.step(7,2){println "$it"} // output 0 2 4
    // 0 is start point, 7 is max number of loop and 2 is steps of iteration
```

## Groovy List
List structure allows you to store a collection of data Items. In a Groovy programming language, the List holds a sequence of object references. It also shows a position in the sequence.

- A list of Strings- [‘Angular’, ‘Nodejs,]
- A list of object references – [‘Groovy’, 2,4 2.6]
- A list of integer values – [16, 17, 18, 19]
- An empty list- [ ]

### list methods
add()      // Allows you to append the new value to the end of this List.
contains() //	Returns true if this List contains a certain value.
get()      // Returns the element at the definite position
isEmpty()  // Returns true value if List contains no elements
minus()    // This command allows you to create a new List composed of the elements of the original excluding those which are specified in the collection.
plus()     //	Allows you to create a new List composed of the elements of the original together along with mentioned in the collection.
pop()      // Removes the last item from the List
remove()   // Removes the element at the specific position
reverse()  // Create a new List which reverses the elements of the original List
size()     //	Returns the number of elements in this List
sort()     //	Returns a sorted copy

```groovy 
    def y = ["Guru99", "is", "Best", "for", "Groovy"]
    println y // output: [Guru99, is, Best, for, Groovy]
    y.add("Learning")
    println(y.contains("is")) // output: true
    println(y.get(2)) // output: Best
    println(y.pop()) // output: Learning
```

## Groovy Maps
A Map Groovy is a collection of Key Value Pairs

- [Tutorial: ‘Java, Tutorial: ‘Groovy] – Collection of key-value pairs which has Tutorial as the key and their respective values
- [ : ] Represent an Empty map

### map methods 
containsKey()   // Check that map contains this key or not?
get()	        // This command looks up the key in this Map and returns the corresponding value. If you don’t find any entry in this Map, then it will return null.
keySet()	    // Allows to find a set of the keys in this Map
put()	        // Associates the specified value with the given key in this Map. If the Map earlier contained a mapping for this key. Then the old value will be replaced by the specified value.
size()	        // Returns the number of key-value mappings.
values()	    // This command returns a collection view of the values.

```groovy
    def y = [fName:'Jen', lName:'Cruise', sex:'F']
    print y.get("fName") // output: Jen
```

## Groovy- Closures
groovy closure is a piece of code wrapped as an object. It acts as a method or a function.

```groovy
    def myClosure = {
        println "My First Closure"	
    }
    myClosure() // output: My First Closure
```

A closure can accept parameters. The list of identifies is comma separated with
an arrow (->) marking the end of the parameter list.
(A closure can return a value.)

```groovy 
    def myClosure = {
        a,b,c->
        y = a+b+c
        println y
        return y
    }
    myClosure(1,2,3)  // output: 6
```

> There are many built-in closures like “It”, “identity”, etc. Closures can take other closure as parameters.

## Groovy Vs. Java

- In Groovy, default access specifier is public. It means a method without any specified access modifier is public and accessible outside of class and package boundaries.
- Getters and setters are automatically generated for class members.
- Groovy allows variable substitution using double quotation marks with strings.
- Typing information is optional.
- Groovy it’s not required to end with a semicolon.
- Groovy is automatically a wrapping class called Script for every program

## Cons of using Groovy
- JVM and Groovy script start time is slow which limits OS-level scripting
- Groovy is not entirely accepted in other communities.
- It is not convenient to use Groovy without using IDE
- Groovy can be slower which increased the development time
- Groovy may need lots of memory
- Knowledge of Java is imperative.

## Groovy Tools
1. groovysh: Executes code interactively.
2. groovyConsole: GUI for interactive code execution
3. groovy: Executes groovy scripts. You can use it like Perl, Python, etc.