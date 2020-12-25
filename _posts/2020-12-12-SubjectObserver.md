---
layout: post
title: Subject Observer Pattern
author: Mozhang Guo
categories: Note
tags: [Design Pattern]
---

This is another design pattern that I encountered in my work place. From wiki, it says that the observer pattern (or subject observer pattern) is just an software design pattern, where a subject maintains a list of its dependents named as observers, and the subject notifies them automatically of any state changes, usually by calling one of their methods.

## Usage? 
This design pattern is pretty common in event drive programming, especially with GUI components since it provides a way for subject to react to events happening in other subjects without coupling to their classes.

The pattern can be recognized by subscription methods, that store objects in a list and by calls to the update method issued to object in that list.

## Design Intent
Let user define a subscription mechanism to notify multiple objects about any events that happen to the object  they're observing. Sometimes, the subject is called publisher and the observer is called subscriber.

*Observer Design*
```C++
class IObserver {
    public:
    virtual ~IObserver() = default;
    virtual void Update(const std::string &message_from_subject) = 0;
};
```

*Subject Design*
```C++
class ISubject {
    public:
    virtual ~IObserver() = default;
    virtual void Attach(IObserver *observer) = 0;
    virtual void Detach(IObserver *observer) = 0;
    virtual void Notify() = 0;
};
```
*Below are some template functions that Subject might support*
```C++
class Subject : public ISubject {
    public:
    virtual ~Subject() = default;

    void Attach(IObserver *observer) override {
        mObserverList.push_back(observer);
    }

    void Detach(IObserver *observer) override {
        if (mObserverList.find(observer)) {
            mObserverList.remove(observer);
        }
    }
    
    void Notify() override {
        std::list<IObserver *>::iterator = mObserverList.begin();
        HowManyObserver();
        while (iterator != mObserverList.end()) {
            (*iterator)->update(message_);
            ++iterator;
        }
    }

    void CreateMessage(std::string message = "Empty") {
        this->message_ = message;
        Notify();
    }

    void HowManyObserver() {
        std::cout << "There are" << mObserverList.size() << "observers in the list.";
    }

    void doSomething() {
        this->message_ = "change message";
        Notify();
        std::cout << "do something";
    }

    private:
    std::list<IObserver *> mObserverList;
    std::string message_;
}
```

```C++
class observer : public IObserver {
    public:
    Observer(Subject &subject) : subject_(subject) {
        this->subject_.Attach(this);
        std::cout << "observer" << ++Observer::static_number_;
        this->number_ = Observer::static_number_;
    }

    virtual ~Observer() = default;

    void Update(const std::string &message_from_subject) override {
        message_from_subject_ = message_from_subject;
        PrintInfo();
    }

    void RemoveFromTheList() {
        subject_.Detach(this);
    }

    void PrintInfo() {
        
    }

    private:
    std::string message_from_subject_;
    Subject &subject;
    static int static_number_;
    int number;

 }
```