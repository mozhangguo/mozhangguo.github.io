---
layout: post
title: Singleton Pattern
author: Mozhang Guo
categories: Note
tags: [Design Pattern, Creational Pattern]
---



# Introduction

Singleton ensure a class only has one instance and provide a global point of access to it.

It is important for some classes to have exactly one instance. Although there can be many printers in a system, there should be only one printer spooler. To ensure that a class has only one instance and that instance is easily accessible. The simplest way is to use global variable. However, global variable does not keep user away from instantiating multiple objects. Therefore, we promote Singleton design pattern.

### Applicability
Use the singleton pattern in the following cases:
* there must be exactly one instance of a class, and it must be accessible to clients from a well-known access point. (Eg. Device)
* When the sole instance should be extensible by subclassing, and clients should be able to uss an extended instance without modifying their code. (Eg. Device variance)

### Structure
*code below are in plantUML*
```plantUML
@startuml

title Singleton Structure

class Singleton {
  -static Singleton* mInstance
  -SingletonData
  +static Singleton* instance()
  +SomeSingletonOperation()
  +GetSingletonData()
  #Singeleton()
}

note right of Singleton::instance
return mInstance
end note
@enduml
```
![Singleton structure UML](/assets/img/posts/Singleton/SingletonStructure.png)

### Participants
Singleton
* defines an instance method and lets clients access its unique instance. Instance is a class method(static).
* may be responsible for creating its own unique instance.

# Implementation
A few things that we need to consider when using this pattern. 
1. we need to ensure a unique instance. The class need to be written such that only one instance can ever be created. A common way is to hid the operation that creates the instance behind a class operation (that is, either a static member function or class method) that guarantees only one instance is created. This opeartion has access to the variable that holds the unique instance, and it ensures the variable is initialized with the unique instance before returning its value.

**Example**
```cpp
class Singleton {
public:
static Singleton* getInstancePtr() {
    if (mInstancePtr == nullptr) {
        mInstancePtr = new Singleton();
    }
    return mInstancePtr;
};

protected:
Singleton() = default;

private:
static Singleton* mInstancePtr;
}

Singleton* Singleton::mInstancePtr = nullptr; 
```

In the example above, Clients access the singleton exclusively through the `getInstancePtr()` member function. The variable `mInstancePtr` is initialized as nullptr and the `getInstancePtr()` returns its value. The `getInstancePtr()` use lazy initialization; the value is not created and stored until it's first accessed.

Notice that the `Singleton()` is protected. A client that tries to instantiate Singleton object would get an error at compile time. It ensure that only one instance is created. Moreover, since the `mInstancePtr` is a pointer to Singleton object, the `getInstancePtr()` method could assign a pointer to a subclass of Singleton to this variable. There is another thing to note about C++ implementation. It is not enough to define the singleton as a global or static object and then rely on automatic initialization.

Because we cannot guarantee that only one instance of a static object will ever be declared. We might not have enough information to instantiate every singleton at static initialization time. C++ does not define the order in which constructors for global objects are called across translation units. This means that no dependencies can exist between singletons.

2. About subclassing the Singleton class. The main issue is not such much defining the subclass but installing its unique instance so that the clients will be able to use it. In essence, the variable that refers to the singleton instance must get initialized of the subclass. The simplest technique is to determine which singleton you want to use in the Singleton's `getInstancePtr()` method. For example, we can store the require info in environment variables or pre preprocessor directives.

*using environment variables*
```cpp
static Singleton* Singleton::getInstancePtr() {
    if (mInstancePtr == nullptr) {
        const char* singletonMode = getenv("singletonMode");
        switch(singletonMode) {
            case "someMode":
                mInstancePtr = new SomeMode();
                break;
            /* there could be many singleton subclass*/
            default:
                mInstancePtr = new Singleton();
        }
    }
    return mInstancePtr;
};
```

*using preprocessor directives*

```cpp
#define SomeMode

static Singleton* Singleton::getInstancePtr() {
    if (mInstancePtr == nullptr) {
#ifdef SomeMode
        mInstancePtr = new SomeMode();
#else
        mInstancePtr = new Singleton();
#endif        
    }
    return mInstancePtr;
};
```

Another way is to take out the implementation of `getInstancePtr()` out of the parent class and put it in the subclass. In the link time (linking object containing different implementation), the singleton could choose different implementation and keeps clients hiden away from that. 

Registry of singleton is another approach. Instead of having `getInstancePtr()` to define a set of possible singleton classes, the singleton classes can register their singleton instance by name in a well-known registry.

The registry maps between string names and singletons. When `getInstancePtr()` needs a singleton, it consults the registry, asking for the singleton by name. The registry looks up the corresponding singleton (if it exists) and returns it. This approach frees `getInstancePtr()` from knowing all possible singleton classes or instances. All it requires is a common interface for all singleton classes that includes operations for the registry.

```cpp
class Singleton {
    public:
    /**
     * @brief:Register the singleton instance under the given name. To keep the registry simple, mRegistry list is used to store the NameSingletonPair objects
     * Each NameSingletonPair maps a name to a singleton.
    */
    static void Register(const char * name, Singleton* regSingleton) {
        
    }
    static Singleton* getInstancePtr() {
        if (mInstancePtr == nullptr) {
            const char * singeletonName = getenv("singleton");

            mInstancePtr = Lookup(singeletonName);
        }
        return mInstancePtr;
    }

    protected:
    Singleton() = default;
    
    /**
     * @brief: The Lookup method find a singleton given its name
    */
    static Singleton* Lookup(const char* name) {

    }

    private:
    static List<NameSingletonPair>* mRegistry;
    static Singleton* mInstancePtr;
}
```
The singleton class register themselves in the constructor. We can defines a static instance of MySingleton to register the service.
```cpp
MySingleton :: MySingleton() {
    Singleton::Register("MySingleton", this);
}
```
In such case, it is no longer the singleton class responsibility to create singleton. Instead, its primary responsibility is to make the singleton object of choice accessible in the system.

The drawback of this approach is that all possible subclasses much be created otherwise they would not be registered.