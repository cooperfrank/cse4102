# Object-Oriented Programming
- Objects encapsulate state (fields) and actions (methods)
- (Often) classes that say what the state and actions are
  - Objects are instances of classes
- (Often) subclasses that inherit from other classes
- (Often) subclasses can override methods
- Dynamic Dispatch: Methods are called based on what an object is at runtime
- (Sometimes) subclasses can inherit from multiple other classes

# Smalltalk
- Very early object-oriented language (1972)
- First major release: Smalltalk-80
- Adele Goldberg, Dan Ingalls, Alan Kay

## Smalltalk Design Principles
1. Everything is an object
2. Every object is an instance of a class
3. Every class has a superclass
4. Everything happens by message sends
5. Method lookup follows the inheritance chain

### Everything is an Object
- 1 is an object, unlike in Java
- 1 + 2 is the object 1 calling the add method with the argument 2
- new A (in Smalltalk: A new)
  - In java, a class is not an object, but in Smalltalk, classes are also objects. This is just calling the new method on the class A which is an object

#### Classes as Object
- "instance side"
  - The methods and instance variables (fields) available in an instance of a person (like name, age, sayHelloTo)
- "class side"
  - The methods and isntance varaibles you can use directly on the class Person (which is just `new`)
 
### Every class has a superclass
- There is an Object class that every class inherits from
- Object is a subclass of ProtoObject
- ProtoObject is a subclass of ProtoObject (cycle)

### Everything happens by message sends
Three Kinds of Messages:

1. Unary
2. Binary (mostly just arithmetic operators)
3. Keyword

Smalltalk first evaluates all unary messages from left to right, then all binary messages from left to right, then all keyword messages from left to right
