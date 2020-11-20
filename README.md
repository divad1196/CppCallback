# CppCallback

Partially inspired by RxJS.

* Header only
* Simple to use



Work in Progress



## Classes

### Wrapper

a wrapper defining getter and setter

```c++
Wrapper<int> a(0,
	[](const int& value){ return value / 2; },
	[](int& value, const int& new_value){ value = new_value * 4;}
);

std::cout << a.get() << std::endl;  // 0
a.set(10);
// we can also use implicit cast
std::cout << a << std::endl;	// 20
a.set(a.get() * 3);
std::cout << a.get() << std::endl;  // 120
```



### Subject

Call successively all callbacks on value change

```c++
int tmp = 0;

Subject<int> b(0);
b.subscribe([&tmp](const int& value) { tmp = value; });
b.subscribe([](const int& value) { std::cout << "value changed to " << value << std::endl; });

std::cout << tmp << std::endl;
b.set(9);
std::cout << tmp << std::endl;
```



### Interval

Use the callback after a given period of time (ms)

```c++
// Run for a given period of 5000ms
Interval interval(1000);
    
// Convenient way to use fps
Interval interval2 = Interval::FPS(60);



interval.runFor([](){
	std::cout << "hello world" << std::endl;
}, 5000);

// Run while condition is true
bool condition = true;
interval.runWhile([&condition](Interval::Duration dt){
    ...
    if(some_requirments)
        condition = false;
})
```

* runFor: run for a given period of time (ms)
* runWhile: take a boolean sentinel by const reference. The value must be updated from the callback.
* run: simply run forever.

The callback function can eiter take:

* No parameter
* Interval::Duration by copy which is the duration since last callback in ms.
  Nb: Use this value instead of the known value of interval, even if they currently are the same.



### Debounce

Pretty similar to Interval, it return true if it's last use was more than the interval time, else return false.

```c++
int count = 0;
Debounce dbounce(1000);
while (count < 5) {
    dbounce && (count += 1) && (
        std::cout << count << std::endl
    );
}
```

Nb: I do not recommand to write it by chaining &&, but it allows this kind of use.

## Futur

* Implements data stream
* Take advantage of coroutine.
* Add atomic version and support for threading
* add Pipe class