SingletonPattern is a type of a creational pattern, the idea is to have a single instance of a particular object only this meaans we are taking the away the constructor. 

We expose a function that lets customers access the instance ---- as you can see getInstance function below.

We also need to understand we need to make the getInstance function thread safe to prevent a race condition acction, in C++ as show in the example this can easily be doe using lock_guard

Some of the other patterns which are important here is
lazy creatio as you may the notice the object is not being created until firt requested using the getInstance function --- the lazy loading is needed in the events the object itself like Inititalize database connections is expensive operation, hence unless someone is asking for it, we do not go ahead and create it

--------------------------------------------------------

## Advantages
The Singleton pattern offers several benefits when controlled access to a single instance is required:

Ensures only one instance exists, preventing inconsistent states.
Saves memory and system resources by avoiding multiple object creation.
Provides a global access point, making it easy to share the same instance across the application.

## Disadvantages
Despite its benefits, Singleton can introduce certain design challenges:

Can create tight coupling since many classes depend on a global instance.
Difficult to unit test due to shared global state.
May cause issues in multithreaded environments if not implemented carefully.

--------------------------------------------------------
#include <iostream>
#include <thread>
#include <mutex>
using namespace std;

class Singleton {
private:
    static Singleton* instance;
    static mutex mtx;

    Singleton() {
        cout << "Constructor called! Instance at: " << this << endl;
    }

public:
    static Singleton* getInstance() {
        lock_guard<mutex> lock(mtx);

        if (instance == nullptr) {
            instance = new Singleton();
        }
        return instance;
    }
};

Singleton* Singleton::instance = nullptr;
mutex Singleton::mtx;

void accessSingleton(int id) {
    Singleton* s = Singleton::getInstance();
    cout << "Thread " << id << " got instance at: " << s << endl;
}

int main() {
    thread t1(accessSingleton, 1);
    thread t2(accessSingleton, 2);

    t1.join();
    t2.join();

    return 0;
}
