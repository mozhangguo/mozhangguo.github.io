---
layout: post
title: Dependency Injection
author: Mozhang Guo
categories: Note
tags: [Design Pattern]
---

## Basics
So, [Dependency Injection](https://en.wikipedia.org/wiki/Dependency_injection) is a design pattern that is frequently used to support modularity and testability of the code base. 

Overall, there are 4 roles in *dependency injection*
* The **service** to use
* the **client** that uses the service
* the **interface** that used by the client and implemented by the service
* the **injector** that creates a service instance and injects it into the client

Another concept we mentioned here is [Inversion of Control](https://en.wikipedia.org/wiki/Inversion_of_control). Basically *inversion of control* means that the class object should configure its own dependencis from the outside.


## Example
Consider we are implementating the following features.

```C++
// public visiable header 
class Service {
public:
    Service() = default;
    ~Service() = default;

    void doSomething();
}
```

Wherase in the client that uses the service above, we declare as the following way to use the Service
```C++
//public visiable header
class Client {
public:
    Client() = default;
    ~Client() = default;

    void useService() {
        mService.doSomething();
    }
private:
    Service mService;
}
```
In the main function or wherever the client is used. We can do the following to invoke the call of doSomething(). 
```C++
int main() {
    Client client = new Client();
    client.useService();
}
```
In the example able, the Client is tights up to Service, which means that we cannot use call a client without a concete Service implementation. Therefore, we cannot easily swap service.

### What happens with dependency injection?
First we need to define a header abstract all the services
*Public Header <IService.hpp>*
```C++
class IService {
    public:
    IService() = default;
    virtual ~IService() = default;
    virtual doSomething() = 0;
}

std::unique_ptr<IService> createIService();
```

*Interal Header <Service.hpp>*
```C++
#include <IService.hpp>

class Service : public IService {
    public:
    IService() = default;
    virtual ~IService() = default;
    void doSomething();
}
```

*Source file <Service.cpp>*
```C++
std::unique_ptr<IService> makeService() {
    return std::make_unique<Service>();
}
```

*public header <Client.hpp>*
```C++
class Client {
    public:
        Car(std::unique_ptr<IService>&& service) :mService(std::move(service)) {}
        void useService();
    private:
        std::unique_ptr<IService> mService;
}
```

### Some other notes
Be careful on what should be public header and what should be internal header. Try to keep everything inside the cpp file. If that is not enough, an internal header might do.

**virtual destructor** is always a good idea. if the derivied type has a destructor, it won't get called if we store an upcast pointer to the interface unless the interface declares a virtual destructor.

**Covention on factory functions**
MakeService returns a unique pointer passing the ownership to the caller. The unique_ptr can be moved to the object which ends up owning it.

The UseService implies there already exists an IService object which is owned by someone else, with the guarantee that it will outlive the caller. In such case, there is no need for pointers and we can simply return a reference to the object.

The `GetService()` function implies shared ownership. We get an object taht other objects might hold a reference to.
```C++
std::unique_ptr<IService> MakeService()
IService& UseService() {
    static auto instance = std::make_unique<Service>();
    return *instance;
}
std::shared_ptr<IService> GetService();
```
### More of it maybe?
Here are some more design that I could refer to. Dependency Injection with Templates

## In Summary

## References
1. https://vladris.com/blog/2016/07/06/dependency-injection-in-c.html
