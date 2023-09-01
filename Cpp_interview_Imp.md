C++
Commonly Asked C++ Interview Questions | Set 1
What are the differences between C and C++?
1) C++ is a kind of superset of C, most of C programs except few exceptions (See this and this) work in C++ as well.
2) C is a procedural programming language, but C++ supports both procedural and Object Oriented programming.
3) Since C++ supports object oriented programming, it supports features like function overloading, templates, inheritance, virtual functions, friend functions. These features are absent in C.
4) C++ supports exception handling at language level, in C exception handling is done in traditional if-else style.
5) C++ supports references, C doesn’t.
6) In C, scanf() and printf() are mainly used input/output. C++ mainly uses streams to perform input and output operations. cin is standard input stream and cout is standard output stream.

There are many more differences, above is a list of main differences.

What are the differences between references and pointers?
Both references and pointers can be used to change local variables of one function inside another function. Both of them can also be used to save copying of big objects when passed as arguments to functions or returned from functions, to get efficiency gain.
Despite above similarities, there are following differences between references and pointers.

References are less powerful than pointers

Once a reference is created, it cannot be later made to reference another object; it cannot be reseated. This is often done with pointers.
References cannot be NULL. Pointers are often made NULL to indicate that they are not pointing to any valid thing.
A reference must be initialized when declared. There is no such restriction with pointers
Due to the above limitations, references in C++ cannot be used for implementing data structures like Linked List, Tree, etc. In Java, references don’t have above restrictions, and can be used to implement all data structures. References being more powerful in Java, is the main reason Java doesn’t need pointers.

References are safer and easier to use:

Safer: Since references must be initialized, wild references like wild pointers are unlikely to exist. It is still possible to have references that don’t refer to a valid location (See questions 5 and 6 in the below exercise )
Easier to use: References don’t need dereferencing operator to access the value. They can be used like normal variables. ‘&’ operator is needed only at the time of declaration. Also, members of an object reference can be accessed with dot operator (‘.’), unlike pointers where arrow operator (->) is needed to access members.
What are virtual functions – Write an example?
Virtual functions are used with inheritance, they are called according to the type of object pointed or referred, not according to the type of pointer or reference. In other words, virtual functions are resolved late, at runtime. Virtual keyword is used to make a function virtual.

Following things are necessary to write a C++ program with runtime polymorphism (use of virtual functions)

A base class and a derived class.
A function with same name in base class and derived class.
A pointer or reference of base class type pointing or referring to an object of derived class.
For example, in the following program bp is a pointer of type Base, but a call to bp->show() calls show() function of Derived class, because bp points to an object of Derived class.

#include<iostream>
using namespace std;

class Base {

public:
    virtual void show() { cout<<" In Base \\n"; }

}
  
class Derived: public Base {

public:
    void show() { cout<<"In Derived \\n"; } 

};

int main(void) {   

    Base *bp = new Derived;     
    bp->show();  // RUN-TIME POLYMORPHISM
    return 0;

}
Output:

In Derived

What is this pointer?
The ‘this’ pointer is passed as a hidden argument to all nonstatic member function calls and is available as a local variable within the body of all nonstatic functions. ‘this’ pointer is a constant pointer that holds the memory address of the current object. ‘this’ pointer is not available in static member functions as static member functions can be called without any object (with class name).

Can we do “delete this”?

Ideally delete operator should not be used for this pointer. However, if used, then following points must be considered.

1) delete operator works only for objects allocated using operator new (See this post). If the object is created using new, then we can do delete this, otherwise behavior is undefined.

class A

{

  public:

    void fun()

    {

        delete this;

    }

};

  

int main()

{

  /* Following is Valid */

  A *ptr = new A;

  ptr->fun();

  ptr = NULL; // make ptr NULL to make sure that things are not accessed using ptr. 

  

  

  /* And following is Invalid: Undefined Behavior */

  A a;

  a.fun();

  

  getchar();

  return 0;

}
2) Once delete this is done, any member of the deleted object should not be accessed after deletion.

#include<iostream>

using namespace std;

  

class A {

  int x;

  public:

    A() { x = 0;}

    void fun() {

      delete this;

  

      /* Invalid: Undefined Behavior */

      cout<<x;

    }

};
What are VTABLE and VPTR?
vtable is a table of function pointers. It is maintained per class.
vptr is a pointer to vtable. It is maintained per object
Compiler adds additional code at two places to maintain and use vtable and vptr.

Code in every constructor. This code sets vptr of the object being created. This code sets vptr to point to vtable of the class.
Code with polymorphic function call (e.g. bp->show() in above code). Wherever a polymorphic call is made, compiler inserts code to first look for vptr using base class pointer or reference (In the above example, since pointed or referred object is of derived type, vptr of derived class is accessed). Once vptr is fetched, vtable of derived class can be accessed. Using vtable, address of derived derived class function show() is accessed and called.
#include <iostream.h>

class Base {

public:
  virtual void function1() { cout << "Base :: function1()\n"; };

  virtual void function2() { cout << "Base :: function2()\n"; };

  virtual ~Base(){};
};

class D1 : public Base {

public:
  ~D1(){};
  virtual void function1() { cout << "D1 :: function1()\n"; };
};

class D2 : public Base {

public:
  ~D2(){};
  virtual void function2() { cout << "D2 :: function2\n"; };
};

int main() {

  D1 *d = new D1;
  ;
  Base *b = d;
  b->function1();
  b->function2();
  delete (b);
  return (0);
}
Output:

D1 :: function1() // base virtual member function got overloaded

// If virtual is removed then: Base :: function1()

Base :: function2()



Commonly Asked C++ Interview Questions | Set 2
Q. Major Differences between JAVA and C++ 
There are lot of differences, some of the major differences are:

Java has automatic garbage collection whereas C++ has destructors, which are automatically invoked when the object is destroyed.

Java does not support pointers, templates, unions, operator overloading, structures etc.

C++ has no in built support for threads, whereas in Java there is a Thread class that you inherit to create a new thread

No goto in JAVA

C++ support multiple inheritance, method overloading and operator overloading but JAVA only has method overloading.

Java is interpreted and hence platform independent whereas C++ isn’t. At compilation time, Java Source code converts into JVM byte code. The interpreter executes this bytecode at run time and gives output. C++ run and compile using compiler which converts source code into machine level language.

Q. What are C++ access specifiers ?
Access specifiers are used to define how the members (functions and variables) can be accessed outside the class.

Private: Members declared as private are accessible only within the same class and they cannot be accessed outside the class they are declared. Child classes are also not allowed to access private members of parent.

Public: Members declared as public are accessible from anywhere.

Protected: Only the class and its child classes can access protected members.table5.6

Do you know What happens when more restrictive access is given to a derived class method in C++?

 
Q. Major C++ features
Class: Class is a blueprint of data and functions or methods. Class does not take any space.

Object: Objects are basic run-time entities in an object oriented system, objects are instances of a class these are defined user defined data types.

Encapsulation and Data abstraction: Wrapping up(combining) of data and functions into a single unit is known as encapsulation. The data is not accessible to the outside world and only those functions which are wrapping in the class can access it. This insulation of the data from direct access by the program is called data hiding or information hiding.

Data abstraction – providing only needed information to the outside world and hiding implementation details. For example, consider a class Complex with public functions as getReal() and getImag(). We may implement the class as an array of size 2 or as two variables. The advantage of abstractions is, we can change implementation at any point, users of Complex class wont’t be affected as our method interface remains same. Had our implementation be public, we would not have been able to change it.

Inheritance: Inheritance is the process by which objects of one class acquire the properties of objects of another class. It supports the concept of hierarchical classification. Inheritance provides reusability. This means that we can add additional features to an existing class without modifying it.

Polymorphism: Polymorphism means ability to take more than one form. An operation may exhibit different behaviors in different instances. The behavior depends upon the types of data used in the operation.

Dynamic Binding: In dynamic binding, the code to be executed in response to function call is decided at runtime. C++ has virtual functions to support this.

Message Passing: Objects communicate with one another by sending and receiving information to each other. A message for an object is a request for execution of a procedure and therefore will invoke a function in the receiving object that generates the desired results. Message passing involves specifying the name of the object, the name of the function and the information to be sent.

Q. Structure vs class in C++
In C++, a structure is same as class except the following differences:

Members of a class are private by default and members of struct are public by default.

When deriving a struct from a class/struct, default access-specifier for a base class/struct is public. And when deriving a class, default access specifier is private.

Q. Malloc() vs new / Delete vs Free
Following are the differences between malloc() and operator new.

new is an operator, while malloc() is a function.

new returns exact data type, while malloc() returns void *.

new calls constructors( class instances are initalized and deinitialized automatically), while malloc() does not (classes won’t get initalized or deinitialized automatically)

Syntax:

    1.  int *n = new int(10); // initialization with new()

    2.  str = (char *) malloc(15); //malloc()
free( ) is used on resources allocated by malloc( ), or calloc( ) in C

Delete is used on resources allocated by new in C++

Q. Inline Functions
C++ provides an inline functions to reduce the function call overhead. Inline function is a function that is expanded in line when it is called. When the inline function is called whole code of the inline function gets inserted or substituted at the point of inline function call. This substitution is performed by the C++ compiler at compile time. Inline function may increase efficiency if it is small.

The syntax for defining the function inline is:

inline return-type function-name(parameters) {

// function code

}
Remember, inlining is only a request to the compiler, not a command. Compiler can ignore the request for inlining.

 Q.Friend class and function in C++
A friend class can access private and protected members of other class in which it is declared as friend. It is sometimes useful to allow a particular class to access private members of other class. For example a LinkedList class may be allowed to access private members of Node.

Friend Function Like friend class, a friend function can be given special grant to access private and protected members. A friend function can be:

a) A method of another class

b) A global function

Important points about friend functions and classes:

1) Friends should be used only for limited purpose. too many functions or external classes are declared as friends of a class with protected or private data, it lessens the value of encapsulation of separate classes in object-oriented programming.

2) Friendship is not mutual. If a class A is friend of B, then B doesn’t become friend of A automatically.

3) Friendship is not inherited (See this for more details)

4) The concept of friends is not there in Java.

Q. Function overloading VS Operator Overloading
Function overloading is a feature in C++ where two or more functions can have the same name but different type of parameters and  different number of parameters.

Note: Overloading of functions with different return types are not allowed.

Operating overloading allows us to  make operators to work for user defined classes. For example, we can overload an operator ‘+’ in a class like String so that we can concatenate two strings by just using +.

Other example classes where arithmetic operators may be overloaded are Complex Number, Fractional Number, Big Integer, etc.

class Complex {
private:
int real, imag;

public:
Complex(int r = 0, int i = 0) {
  real = r;
  imag = i;
}

// This is automatically called when '+' is used with
// between two Complex objects
Complex operator+(Complex const &obj) {
  Complex res;
  res.real = real + obj.real;
  res.imag = imag + obj.imag;
  return res;
}
void print() { cout << real << " + i" << imag << endl; }
};

int main() {
Complex c1(10, 5), c2(2, 4);
Complex c3 = c1 + c2; // An example call to "operator+"
c3.print();
}
Q. Copy Constructor
A copy constructor is a member function which initializes an object using another object of the same class. A copy constructor has the following general function prototype:

ClassName(const ClassName &old_obj);

Point(int x1, int y1) {
  x = x1;
  y = y1;
}

// Copy constructor

Point(const Point &p2) {
  x = p2.x;
  y = p2.y;
}
When is copy constructor called?

In C++, a Copy Constructor may be called in following cases:

When an object of the class is returned by value.

When an object of the class is passed (to a function) by value as an argument.

When an object is constructed based on another object of the same class.

When compiler generates a temporary object.

Can we make copy constructor private?

Yes, a copy constructor can be made private

 
Q.What Is Inheritance?
Different kinds of objects often have a certain amount in common with each other. Yet each also defines additional features that make them different. Object-oriented programming allows classes to inherit commonly used state and behavior from other classes

Q. What is Static Member?
Static is a keyword in C++ used to give special characteristics to an element. Static elements are allocated storage only once in a program lifetime in static storage area. And they have a scope till the program lifetime. Static Keyword can be used with following,

Interesting facts about Static Members Functions  in C++

static member functions do not have this pointer.

A static member function cannot be virtual

Member function declarations with the same name and the name parameter-type-list cannot be overloaded if any of them is a static member function declaration.

static member function can not be declared const, volatile, or const volatile.

What is a namespace?

In each scope, a name can only represent one entity. So, there cannot be two variables with the same name in the same scope. Using namespaces, we can create two variables or member functions having the same name.

A namespace definition begins with the keyword namespace followed by the namespace name as follows:

namespace namespace\_name

{

int x, y; // code declarations where

// x and y are declared in

// namespace\_name's scope

}

