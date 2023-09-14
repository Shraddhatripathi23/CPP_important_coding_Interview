# Commonly Asked question 

# Table of Contents
- [Features of OOP](#features-of-oop)
- [Smart Pointers](#smart-pointers)
- [Name Mangling and Externs](#name-mangling-and-externs)
- [Virtual Functions](#virtual-functions)
  * [Pure Virtual Functions](#pure-virtual-functions)
  * [Virtual Destructors](#virtual-destructors)
  * [Virtual Table](#virtual-table)
- [Virtual Base Class](#virtual-base-class)
- [Friend Functions](#friend-functions)
  * [Friends as Bridges](#friends-as-bridges)
- [Friend Classes](#friend-classes)
  * [Private constructors using Friend Classes](#private-constructors-using-friend-classes)
- [Static Functions](#static-functions)
- [Copy Constructors](#copy-constructors)
- [Shallow Copy and Deep Copy](#shallow-copy-and-deep-copy)
- [Operator Overloading](#operator-overloading)
- [Namespace](#namespace)
- [Templates](#templates)
  * [Function Templates](#function-templates)
  * [Class Templates](#class-templates)
- [Exceptions](#exceptions)
- [free vs delete()](#free-vs-delete--)
- [C Storage Classes](#c-storage-classes)
- [C Storage Layout](#c-storage-layout)
- [Credits](#credits)



# Features of OOP


- **Abstraction**
We try to obtain abstract view, model or structure of real life problem, and reduce its unnecessary details. With definition of properties of problems, including the data which are affected and the operations which are identified, the model abstracted from problems can be a standard solution to this type of problems. It is an efficient way since there are nebulous real-life problems that have similar properties.

- **Encapsulation**
Encapsulation is the process of combining data and functions into a single unit called class. In Encapsulation, the data is not accessed directly; it is accessed through the functions present inside the class. In simpler words, attributes of the class are kept private and public getter and setter methods are provided to manipulate these attributes. Thus, encapsulation makes the concept of data hiding possible. (Data hiding: a language feature to restrict access to members of an object, reducing the negative effect due to dependencies. e.g. "protected", "private" feature in C++).

- **Inheritance**
The idea of inheritance is simple, a class is based on another class and uses data and implementation of the other class. And the purpose of inheritance is Code Reuse.

- **Polymorphism**
Polymorphism is the ability to present the same interface for differing underlying forms (data types). With polymorphism, each of these classes will have different underlying data. A point shape needs only two coordinates (assuming it's in a two-dimensional space of course). A circle needs a center and radius. A square or rectangle needs two coordinates for the top left and bottom right corners and (possibly) a rotation. An irregular polygon needs a series of lines.

# Smart Pointers

Pointers with * and -> overloaded. Using [smart pointers](http://en.wikipedia.org/wiki/Smart_pointer), we can make pointers to work in way that we don’t need to explicitly call delete.[ Smart pointer](http://en.wikipedia.org/wiki/Smart_pointer) is a wrapper class over a pointer with operator like * and -> overloaded. The objects of smart pointer class look like pointer, but can do many things that a normal pointer can’t like automatic destruction (yes, we don’t have to explicitly use delete), reference counting and more.

The idea is to make a class with a pointer, destructor and overloaded operators like * and ->. Since destructor is automatically called when an object goes out of scope, the dynamically allocated memory would automatically deleted (or reference count can be decremented). Consider the following simple smartPtr class.


```cpp
#include<iostream>
using namespace std;

class SmartPtr {
    int *ptr; // Actual pointer
public:
    explicit SmartPtr(int *p = NULL) { ptr = p; }

    ~SmartPtr() {
   	    delete(ptr);
    }

	  // Overloading dereferencing operator
    int &operator *() {
  	    return *ptr;
	  }
};

int main() {
    SmartPtr ptr(new int());
    *ptr = 20;
    cout << *ptr;
    return 0;
}
```


Examples: unique_ptr, shared_ptr

# Name Mangling and Externs

[https://www.geeksforgeeks.org/extern-c-in-c/](https://www.geeksforgeeks.org/extern-c-in-c/) 

In C++, C function names may not be mangled, as C does not have function overloading feature.


```cpp
int printf(char *format, ...);

int main() {
	printf("Hello, world!");
	return 0;
}
```


Gives - 


```cpp
undefined reference to `printf(char const*, ...)'
    	ld returned 1 exit status
```


Use extern C for C codes.

```cpp
extern "C" {
      int printf(char *format, ...);
}
int main() {
    printf("Hello, world!");
    return 0;
}
```

# Virtual Functions

_Constructor can never be virtual function._

[https://ideone.com/cmt0E3](https://ideone.com/cmt0E3) 


```cpp
#include <iostream>
using namespace std;

class Base {
public:
    void show() {
   	 cout << "Base\n";
    }
};

class Derv1: public Base {
public:
    void show() {
   	 cout << "Derv1\n";
    }
};


class Derv2: public Base {
public:
    void show() {
   	 cout << "Derv2\n";
    }
};

int main() {
    Derv1 derv1;
    Derv2 derv2;
    Base *ptr;
    ptr = &derv1;
    ptr -> show();
    ptr = &derv2;
    ptr -> show();
    return 0;
}
```


Output:

```
Base
Base
```

This is known as **Static/Early Binding**. Depends on the _type_ of ptr, not _content_.

Compile-time/Static polymorphism => Function overloading (this one), Operator overloading

[https://ideone.com/8eDA0X](https://ideone.com/8eDA0X)


```cpp
#include <iostream>
using namespace std;

class Base {
public:
    virtual void show() {
   	 cout << "Base\n";
    }
};

class Derv1: public Base {
public:
    void show() {
   	 cout << "Derv1\n";
    }
};


class Derv2: public Base {
public:
    void show() {
   	 cout << "Derv2\n";
    }
};

int main() {
    Derv1 derv1;
    Derv2 derv2;
    Base *ptr;
    ptr = &derv1;
    ptr -> show();
    ptr = &derv2;
    ptr -> show();
    return 0;
}
```


Output:

```
Derv1
Derv2
```

Prepend `virtual` keyword to the function definition => virtual function.

The rule is that the compiler selects the function based on the contents of `ptr`, not type. Type is used in non-virtual case.

Here the compiler does not know what class the contents of `ptr` may contain. It may content address of an object of the `Derv1` class or of the `Derv2` class. At runtime, this is decided on the basis of content. When it is known what class is pointed to by `ptr`, the appropriate version is called. This is known as **Late Binding**.

## Pure Virtual Functions

Another usage for virtual functions is when we cannot or we don’t want to implement a method for parent class.


```cpp
#include <iostream>
using namespace std;

class Base {
public:
    virtual void show() = 0; // pure virtual function
};

class Derv1: public Base {
public:
    void show() {
   	 cout << "Derv1\n";
    }
};


class Derv2: public Base {
public:
    void show() {
   	 cout << "Derv2\n";
    }
};

int main() {
    Derv1 derv1;
    Derv2 derv2;
    Base *ptr;
    ptr = &derv1;
    ptr -> show();
    ptr = &derv2;
    ptr -> show();
    return 0;
}
```


Output:

```
Derv1
Derv2
```

The class Base is now an abstract class and cannot be instantiated. Any class with a pure virtual function is an abstract class. Once you’ve placed a pure virtual function in the base class, you must override it in all the derived classes from which you want to instantiate objects. **If a class doesn’t override the pure virtual function, it becomes an abstract class itself**, and you can’t instantiate objects from it(although you might from classes derived from it). For consistency, you may want to make all the virtual functions in the base class pure.

**Abstract classes** are also called **interfaces** in C++.

A Good Example of Late Binding:

```cpp
#include <iostream>
using namespace std;

class Department {
public:
    void show() {
        cout << "You are in CS dept\n";
    }
    virtual void getData() = 0;
};

class Professor: public Department {
public:
    void getData() {
        Department::show();
        cout << "You are in Professor section\n";
    }
};

class Student: public Department {
public:
    void getData() {
        Department::show();
        cout << "You are in Students section\n";
    }
};


int main() {
    string s;
    cin >> s;
    Department *dept;
    Student student;
    if (s == "S") {
        // same as: Department *dept = new Student();
        // dept = &student;
        dept = new Student();
    } else {
        dept = new Professor();
    }
    dept -> getData();
    return 0;
}
```

## Virtual Destructors

```cpp
#include <iostream>
using namespace std;

class Base {
public:
	void show() {
    	cout << "Base\n";
	}
	virtual ~Base() {
   	 cout << "Base destroyed\n";
	}
};

class Derv: public Base {
public:
	void show() {
    	cout << "Derv\n";
	}
	~Derv() {
   	 cout << "Derv destroyed\n";
	}
};

int main() {
    Base *pBase = new Derv();
    delete pBase;
}
```


Deletes both Base and Derv. If not virtual, only “Base” will be deleted.

## Virtual Table

Source: https://www.learncpp.com/cpp-tutorial/125-the-virtual-table/ 

To implement virtual functions, C++ uses a special form of late binding known as the virtual table.
The virtual table is a lookup table of functions used to resolve function calls in a dynamic/late binding manner.
The virtual table sometimes goes by other names, such as “vtable”, “virtual function table”, “virtual method table”,
or “dispatch table”.

First, every class that uses virtual functions (or is derived from a class that uses virtual functions) is given its
own virtual table. This table is simply a static array that the compiler sets up at compile time. A virtual table
contains one entry for each virtual function that can be called by objects of the class. Each entry in this table
is simply a function pointer that points to the most-derived function accessible by that class.

Second, the compiler also adds a hidden pointer to the base class, which we will call *__vptr.
*__vptr is set (automatically) when a class instance is created so that it points to the virtual
table for that class. Unlike the *this pointer, which is actually a function parameter used by the
compiler to resolve self-references, *__vptr is a real pointer. Consequently, it makes each class
object allocated bigger by the size of one pointer. It also means that *__vptr is inherited by derived
classes, which is important.

```cpp
class Base {
public:
    virtual void function1() {};
    virtual void function2() {};
};
 
class D1: public Base {
public:
    virtual void function1() {};
};
 
class D2: public Base {
public:
    virtual void function2() {};
};
```

Because there are 3 classes here, the compiler will set up 3 virtual tables: one for Base, one for D1, and one for D2.

The compiler also adds a hidden pointer to the most base class that uses virtual functions. Compiler does this automatically.

```cpp
class Base {
public:
    FunctionPointer *__vptr;
    virtual void function1() {};
    virtual void function2() {};
};
 
class D1: public Base {
public:
    virtual void function1() {};
};
 
class D2: public Base {
public:
    virtual void function2() {};
};
```

When a class object is created, *__vptr is set to point to the virtual table for that class.
For example, when a object of type Base is created, *__vptr is set to point to the virtual table
for Base. When objects of type D1 or D2 are constructed, *__vptr is set to point to the virtual
table for D1 or D2 respectively.

Now, let’s talk about how these virtual tables are filled out. Because there are only two virtual
functions here, each virtual table will have two entries (one for function1(), and one for function2()).
Remember that when these virtual tables are filled out, each entry is filled out with the most-derived
function an object of that class type can call.

The virtual table for Base objects is simple. An object of type Base can only access the members of Base.
Base has no access to D1 or D2 functions. Consequently, the entry for function1 points to Base::function1(),
and the entry for function2 points to Base::function2().

The virtual table for D1 is slightly more complex. An object of type D1 can access members of both D1 and Base.
However, D1 has overridden function1(), making D1::function1() more derived than Base::function1().
Consequently, the entry for function1 points to D1::function1(). D1 hasn’t overridden function2(),
so the entry for function2 will point to Base::function2().

The virtual table for D2 is similar to D1, except the entry for function1 points to Base::function1(),
and the entry for function2 points to D2::function2().


![virtual-table](https://www.learncpp.com/images/CppTutorial/Section12/VTable.gif)

The *__vptr in each class points to the virtual table for that class. The entries in the virtual table point
to the most-derived version of the function objects of that class are allowed to call.



# Virtual Base Class


```
    Parent
   /       \
Child1		Child2
  \        /
 Grandchild
```

_The Diamond problem_

If an inherited method from Parent is called from Grandchild, will throw ambiguous error.

```cpp
# include <iostream>
using namespace std;

class Parent {
protected:
    static int basedata;
};

int Parent::basedata = 5;

class Child1 : virtual public Parent { // shares copy of Parent
};

class Child2 : virtual public Parent { // shares copy of Parent
};

class Grandchild : public Child1, public Child2 {
public:
    int getdata() {
        return basedata; // no longer ambiguous
    }
};


int main() {
    Grandchild *gc = new Grandchild();
    cout << gc -> getdata() << "\n";
    return 0;
}
```


The use of the keyword virtual in these two classes causes them to share a single common subobject of their base class Parent. Since there is only one copy of basedata,there is no ambiguity when it is referred to in Grandchild.The need for virtual base classes may indicate a conceptual problem with your use of multiple inheritance, so they should be used with caution.

# Friend Functions

Accessing private or protected data.

## Friends as Bridges

Acts as bridges between two classes.


```cpp
# include <iostream>
using namespace std;

class Beta;

class Alpha {
private:
	int data;
public:
	Alpha(): data(3) { };
	friend int friendFunction(Alpha, Beta);
};

class Beta {
private:
	int data;
public:
	Beta(): data(7) { };
	friend int friendFunction(Alpha, Beta);
};


int friendFunction(Alpha alpha, Beta beta) {
	return alpha.data + beta.data;
}

int main() {
	Alpha alpha;
	Beta beta;
	cout << friendFunction(alpha, beta) << "\n"; // outputs 10
	return 0;
}
```


Note the declaration, they can go anywhere (private, protected) not necessarily in public.

# Friend Classes


```cpp
# include <iostream>
using namespace std;


class Alpha {
private:
	int data;
public:
	Alpha(): data(3) { };
	friend class Beta;
};

class Beta { // can access all private of Alpha
public:
	void func1(Alpha alpha) {
    	cout << alpha.data << "\n";
	}
};


int main() {
	Alpha alpha;
	Beta beta;
	beta.func1(alpha);
	return 0;
}
```


## Private constructors using Friend Classes


```cpp
#include <iostream>
using namespace std;

class A {
private:
    A() {
    	cout << "constructor of A\n";
    }
    friend class B;
};

// class B, friend of class A
class B{
public:
    B(){
   	 A a1;
   	 cout << "constructor of B\n";
    }
};

int main(){
    B b1;
    return 0;
}
```


Outputs:

```
constructor of A
constructor of B
```

However, if we try to instantiate A, we will get error: `_main.cpp:25:7: error: ‘A::A()’ is private within this context`.

# Static Functions

```cpp
# include <iostream>
using namespace std;


class Gamma {
private:
    static int total;
    int id;
public:
    Gamma() {
            ++ total;
            id = total;
    }
    ~ Gamma() {
            -- total;
            cout << "Destroying id number " << id << "\n";
    }
    static void showTotal() {
            cout << "Total is " << total << "\n";
    }
    void showid() {
            cout << "ID is " << id << "\n";
    }
};


int Gamma::total = 0;

int main() {
    Gamma g1, g2;
    Gamma::showTotal(); // without static won't work; i.e., static void showTotal() will work
                        // but void showTotal() won't
    Gamma g3;
    Gamma::showTotal();
    g1.showid();
    g2.showid();
    g3.showid();
    cout << "End of Program\n";
    return 0;
}
```

Outputs:

```
Total is 2
Total is 3
ID is 1
ID is 2
ID is 3
End of Program
Destroying id number 3
Destroying id number 2
Destroying is number 1
```


Note the order of destructions. Clearly shows the LIFO stack underneath.

# Copy Constructors

```cpp
#include<iostream>
using namespace std;

class Point {
private:
    int x, y;
public:
    Point(int x1, int y1) {
    	x = x1; y = y1;
    }

    // Copy constructor
    Point(const Point &p2) {
    	x = p2.x;
    	y = p2.y;
    }

    int getX() {
    	return x;
    }
    int getY() {
    	return y;
    }
};

int main() {
    Point p1(10, 15); // Normal constructor is called here
    Point p2 = p1; // Copy constructor is called here
    Point p3(p1); // Copy constructor, different syntax

    // Let us access values assigned by constructors
    cout << "p1.x = " << p1.getX() << ", p1.y = " << p1.getY();
    cout << "\np2.x = " << p2.getX() << ", p2.y = " << p2.getY();
    cout << "\np3.x = " << p3.getX() << ", p3.y = " << p3.getY();

    return 0;
}
```


C++ provides default copy constructors to all classes.

# Shallow Copy and Deep Copy

Default copy constructor only does shallow copy, i.e., a member by member copy.

![deep-copy-shallow-copy](https://i.stack.imgur.com/AWKJa.jpg)



# Operator Overloading


```cpp
# include <iostream>
using namespace std;

class Counter {
private:
	unsigned int count;
public:
	Counter(): count(0) {}
	unsigned int getCount() {
    	return count;
	}
	void operator ++ () {
    	++count;
	}
};

int main() {
	Counter c1, c2, c3;
	++ c1;
	cout << c1.getCount() << "\n"; // 1
	++ c2;
	cout << c2.getCount() << "\n"; // 1
	cout << c3.getCount() << "\n"; // 0
}
```


Note the return value of the operator method. If we try c4 = ++ c1, compiler will complain since the operator is not returning anything. In order to do that:


```cpp
Counter operator ++ () { //increment count
    ++count; //increment count
    Counter temp; //make a temporary Counter
    temp.count = count;   //give it same value as this obj
    return temp;          //return the copy
}
```


Another elegant way to do this is using nameless temporary object.


```cpp
Counter operator ++ (int) {
    return Counter(count++);
}
```

Arithmetic Operators


```cpp
Distance Distance::operator + (Distance d2) const {
    int f = feet + d2.feet;
    float i = inches + d2.inches;
    if (i >= 12.0) {
        i -= 12.0;
        f++;
    }
    return Distance(f, i);
}

```


# Namespace


```cpp
# include <iostream>
using namespace std;

int value = 10;

namespace first {
	int value = 40;
	namespace second {
    	int value = 20;
	}
}

int main() {
	int value = 30;
	cout << value << "\n"; // 30
	cout << first::value << "\n"; // 40
	cout << first::second::value << "\n"; // 20
	cout << ::value << "\n"; // 10
	return 0;
}
```


One can do namespace aliasing by: `namespace alias = first::second::`.

Can also use `using` keyword: using namespace first.

Unnamed Namespace:


```cpp
# include <iostream>
using namespace std;

int value = 10;

namespace {
	int val = 5;    
}

// namespace {
// 	int val = 6;
// } if included, will throw compilation error

int main() {
	cout << val << "\n"; // prints 5
	return 0;
}
```


The namespace name is uniquely generated by the compiler.

# Templates

Templates make it possible to handle many different data types by a single function or class.

## Function Templates


```cpp
#include <iostream>
using namespace std;

template <class T>
T abs (T n) {
	return n < 0 ? -n : n;
}

int main() {
	cout << abs(5) << "\n";
	cout << abs(-5.2) << "\n";
	return 0;
}

```


_More Than One Template Argument_


```cpp
#include <iostream>
#include <vector>
using namespace std;

template <class atype, class btype>
atype find_ (vector<atype> a, btype n, atype k) {
	for (btype i = 0; i < n; ++i) {
    	    if (a[i] == k) {
        	    return i;
    	    }
	}
      return static_cast<atype>(-1);
	// return -1;
}

int main() {
	cout << find_({-1.0, 2.1, 3.0}, 3, 2.1) << "\n";
	return 0;
}
```

## Class Templates


```cpp
#include <iostream>
using namespace std;

template <class Type>
class Stack {
private:
	Type st[100];
	int top;
public:
	Stack() {
    	    top = -1;
	}
	void push(Type var) {
    	    st[++top] = var;
	}
	Type pop() {
    	    return st[top--];
	}
};


int main() {
	Stack <float> s;
	s.push(123.2);
	s.push(236.78);
	s.push(6553.1);
	cout << s.pop() << "\n";
	cout << s.pop() << "\n";
	cout << s.pop() << "\n";
	return 0;
}
```


# Exceptions

Systematic, object-oriented way to handle errors generated by C++ errors.


```cpp
#include <iostream>
using namespace std;

const int MAX = 3;

class Stack {
private:
	int st[MAX];
	int top;
public:
	class Full {
   	 
	};
	class Empty {
   	 
	};
	Stack() {
    	top = -1;
	}
	void push(int var) {
    	    if (top >= MAX - 1) {
        	        throw Full();
    	}
    	st[++top] = var;
	}
	int pop() {
    	    if (top < 0) {
        	        throw Empty();
    	}
    	return st[top--];
	}
};


int main() {
	Stack s;
	try {
    	    s.push(123);
    	    s.push(236);
    	    s.push(6553);
    	    s.push(23);
    	    cout << s.pop() << "\n";
     	    cout << s.pop() << "\n";
    	    cout << s.pop() << "\n";
    	    cout << s.pop() << "\n";
	} catch(Stack::Full) {
    	    cout << "Exception: Stack Full!\n";
	} catch (Stack::Empty) {
    	    cout << "Exception: Stack Empty!\n";
	}
	return 0;
}
```


We can also define exception classes.


```cpp
#include <iostream>
using namespace std;

class Dist {
private:
	int feet;
	float inches;
public:
	class InchesEx {
    	public:
        	string origin;
        	float iValue;
       	 
    	InchesEx(string org, float in) {
        	origin = org;
        	iValue = in; 
    	      }
	};
    
    
	Dist(int ft, float inc) {
    	    if (inc >= 12.0) {
              throw InchesEx("2 arg constructor", inc);
    	  }
    	  feet = ft;
    	  inches = inc;
	}
 
	void getDist() {
    	    cout << "Enter feet:";
    	    cin >> feet;
    	    cout << "Enter inches";
    	    cin >> inches;
    	    if (inches >= 12.0) {
              throw InchesEx("getDist function", inches);
    	    }
	}  
};


int main() {
	try {
    	    Dist d(5, 13);
	} catch (Dist::InchesEx ix) {
    	    cout << "Error!\n" << ix.origin << "\nInches: " << ix.iValue <<"\n";
	}
	return 0;
}
```


Built-in exception classes: `bad_alloc` is one example. There are many others in STL.

# free vs delete()

In C++, delete operator should only be used either for the pointers pointing to the memory allocated using new operator or for a NULL pointer, and free() should only be used either for the pointers pointing to the memory allocated using malloc() or for a NULL pointer.

# C Storage Classes

- **auto**: default, they can only be accessed within the block/function they have been declared and not outside them (which defines their scope).

- **extern**: This storage class tells that the variable is defined elsewhere and not within the same block where it is used.

- **static**: They have the property of preserving their value even after they are out of their scope.

- **register**: Uses CPU register.

|Storage Specifier | Storage | Initial Value | Scope | Life |
|------------------|---------|---------------|-------|------|
| auto | Stack | Garbage | Within block | End of block |
| extern | Data Segment | Zero | global multiple files | Till end of program |
| static | Data Segment | Zero | Within block | Till end of program | 
| register | CPU Register | Garbage | Within block | End of block |

# C Storage Layout



![alt_text](https://media.geeksforgeeks.org/wp-content/uploads/memoryLayoutC.jpg)


**Heap**: dynamic memory allocation

**Stack**: function calls, local variables

**Uninitialized data (bss)**: BSS = **B**lock **S**tarted by **S**ymbol; all uninitialized global and static variable

**Initialized data (ds)**: DS = **D**ata **S**egment; explicitly initialized global and static variables

**Text**: binary of the compiled program, read-only; sharable so that only a single copy needs to be in memory for frequently executed programs such as text editors etc


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


# Credits

Portions of this note is taken from geeksforgeeks, Leetcode, learncpp website and, Cracking the Coding Interview and Object-Oriented Programming in C++, Fourth Edition book.




