# C++ drafts and examples


## [Classic algorithms](https://github.com/azoyan/CPP-drafts-and-examples/blob/master/README.md#classic-algorithms-1)


### [Circular buffer with tests](https://github.com/azoyan/CPP-drafts-and-examples/blob/master/README.md#circular-buffer-with-tests-1)
### [Quick sort](https://github.com/azoyan/CPP-drafts-and-examples/blob/master/README.md#quick-sort-1)
### [Binary Search Tree](https://github.com/azoyan/CPP-drafts-and-examples/blob/master/README.md#binary-search-tree-1)
### [Forward list and reversing](https://github.com/azoyan/CPP-drafts-and-examples/blob/master/README.md#forward-list-and-reversing-1)  
  
## [Template magic](https://github.com/azoyan/CPP-drafts-and-examples/blob/master/README.md#template-magic-1)


### [SFINAE](https://github.com/azoyan/CPP-drafts-and-examples/blob/master/README.md#sfinae-1)
### [Compile time power and template argument in range checker](https://github.com/azoyan/CPP-drafts-and-examples/blob/master/README.md#compile-time-power-and-template-argument-in-range-checker-1)
### [Extract type of expression](https://github.com/azoyan/CPP-drafts-and-examples/blob/master/README.md#extract-type-of-expression-1)
### [Compile time algorithm to find max value of numbers](https://github.com/azoyan/CPP-drafts-and-examples/blob/master/README.md#compile-time-algorithm-to-find-max-value-of-numbers-1)

## [Other](https://github.com/azoyan/CPP-drafts-and-examples/blob/master/README.md#other-1)


### [Own signals](https://github.com/azoyan/CPP-drafts-and-examples/blob/master/README.md#own-signals-1)
### [Multiply duration and binary shift duration test in threads](https://github.com/azoyan/CPP-drafts-and-examples/blob/master/README.md#multiply-duration-and-binary-shift-duration-test-in-threads-1)
### [string to int conversion with tests](https://github.com/azoyan/CPP-drafts-and-examples/blob/master/README.md#string-to-int-conversion-with-tests-1)
### [gcc __attribute((__packed__)) extension](https://github.com/azoyan/CPP-drafts-and-examples/blob/master/README.md#gcc-__attributepacked-extension-1)
### [std::chrono - elapsed time example](https://github.com/azoyan/CPP-drafts-and-examples/blob/master/README.md#stdchrono---elapsed-time-example-1)
### [std::functional lamdas in constructor as concept](https://github.com/azoyan/CPP-drafts-and-examples/blob/master/README.md#stdfunctional-lamdas-in-constructor-as-concept-1)
### [.bmp (.ppm) image generator](https://github.com/azoyan/CPP-drafts-and-examples/blob/master/README.md#bmp-ppm-image-generator--bad-code-style) (cancer code style!)


## Classic algorithms
### Circular buffer with tests [Run in ideone.com](http://ideone.com/Rg9oJg)

```cpp
#include <iostream>
#include <array>

using namespace std;

template<typename T>
class CircularBuffer {
public:
	CircularBuffer(const size_t size)
	: mSize(size)
	, mLastPos(mSize - 1)
	, mFirstPos(0)
	, mCursorIndex(mFirstPos)
	, mCount(mCursorIndex)
	{
		mArray = new T[mSize];
	}

public:
	void push_back(T t)	{
		if (mCursorIndex < mSize) {
			if (mCount < mSize) ++mCount;
			mArray[mCursorIndex++] = t;
		}
		else {
			mCount = mSize;
			mCursorIndex = mFirstPos;
			mArray[mCursorIndex++] = t;
		}
	}

public:
	T getLast() {
		if (0 < mCursorIndex) return mArray[--mCursorIndex];
		else {
			mCursorIndex = mLastPos;
			return mArray[mCursorIndex];
		}
	}

public:
	T at(size_t index) {
		return mArray[index];
	}

public:
	size_t count() {
		return mCount;
	}

private:
	size_t mSize;
	size_t mLastPos;
	size_t mFirstPos;
	size_t mCursorIndex;
	size_t mCount;
	T* mArray;
};


struct Tests {
	void normal() {
		CircularBuffer<int> buffer(6);
		buffer.push_back(90);
		buffer.push_back(55);
		buffer.push_back(78);
		buffer.push_back(23);
		buffer.push_back(66);
		cout << "Normal test:  ";
		for (size_t i = 0; i < buffer.count(); ++i) {
			cout << i << ":" << buffer.at(i) << ";  ";
		}
		cout << endl;
	}

	void zero() {
		CircularBuffer<int> buffer(0);
		buffer.push_back(90);
		buffer.push_back(55);
		buffer.push_back(78);
		buffer.push_back(23);
		buffer.push_back(66);

		cout << "Zero test: ";
		for (size_t i = 0; i < buffer.count(); ++i) {
			cout << i << ":" << buffer.at(i) << ";  ";
		}
		cout << endl;
	}

	void one() {
		CircularBuffer<int> buffer(1);
		buffer.push_back(90);
		buffer.push_back(55);
		buffer.push_back(78);
		buffer.push_back(23);
		buffer.push_back(66);

		cout << "One test:     ";
		for (size_t i = 0; i < buffer.count(); ++i) {
			cout << i << ":" << buffer.at(i) << ";  ";
		}
		cout << endl;
	}

	void maximum() {
		CircularBuffer<int> buffer(100000);
		buffer.push_back(90);
		buffer.push_back(55);
		buffer.push_back(78);
		buffer.push_back(23);
		buffer.push_back(66);

		cout << "Maximum test: ";
		for (size_t i = 0; i < buffer.count(); ++i) {
			cout << i << ":" << buffer.at(i) << ";  ";
		}
		cout << endl;
	}
};


int main() {
	Tests tests;
	tests.normal();
	tests.maximum();
	tests.one();
	tests.zero();
	return 0;
}
```
### Quick sort [Run in ideone.com](http://ideone.com/DKgvCl)
```cpp
#include <iostream>

void quickSort(int arr[], int left, int right) {
    int i = left, j = right;
    int pivot = arr[(left + right) / 2];

    while (i <= j) {
        while (arr[i] < pivot) i++;
        while (arr[j] > pivot) j--;

        if (i <= j) {
            int tmp = arr[i];
            arr[i] = arr[j];
            arr[j] = tmp;
            i++;
            j--;
        }
    };

    if (left < j)  quickSort(arr, left, j);
    if (i < right) quickSort(arr, i, right);
}

int main() {
	int size = 14;
	int array[size] = {55, 15, 62 ,43,  120 ,325, 77, 267, 23, 66, 91, 32, 51, 100 };

	quickSort(array, 0, size - 1);

	for (int i : array) std::cout << i << " ";

	return 0;
}
```
### Binary Search Tree [Run in ideone.com](http://ideone.com/flnyQD)
```cpp
#include <iostream>

template<typename T>
struct Node {
	Node(int v) : value(v), left(nullptr), right(nullptr) {}
	T value;
	Node* left;
	Node* right;
};

template<typename T>
class BinaryTree {
public:
	BinaryTree() : mRoot(nullptr) {};

	void insert(int value) {
		if (mRoot == nullptr) mRoot = new Node<T>(value);
		else {
			if      (value < mRoot->value) insert(value, mRoot);
			else if (value > mRoot->value) insert(value, mRoot);
		}
	}

	void insert(int value, Node<T>* parent) {
			if (value < parent->value) {
				if (parent->left != nullptr) insert(value, parent->left);
				else parent->left = new Node<T>(value);
			}

			if (value > parent->value) {
				if (parent->right != nullptr) insert(value, parent->right);
				else parent->right = new Node<T>(value);
			}
		}

	void print() {
		if (mRoot != nullptr) print(mRoot);
	}

	void print(Node<T>* node) {
		std::cout << node << " " << node->value << std::endl;
		if (node->left  != nullptr) print(node->left);
		if (node->right != nullptr) print(node->right);
	}

	Node<T>* find(int value) {
		if (mRoot == nullptr) return nullptr;

		if (mRoot->value == value) return mRoot;
		else {
			if      (value < mRoot->value) return find(value, mRoot->left);
			else if (value > mRoot->value) return find(value, mRoot->right);
		}
	}

	Node<T>* find(int value, Node<T>* node) {
		if (node == nullptr) return nullptr;
		if (node->value == value) return node;
		else {
			if      (value < node->value) return find(value, node->left);
			else if (value > node->value) return find(value, node->right);
		}
	}

private:
	Node<T>* mRoot;
};

int main() {
	BinaryTree<int> bt;
	bt.insert(12);
	bt.insert(45);
	bt.insert(5);
	bt.insert(87);
	bt.insert(55);
	bt.insert(11);

	std::cout << bt.find(55) << " " << bt.find(55)->value;
	return 0;
}
```
### Forward list and reversing [Run in ideone.com](http://ideone.com/ReXj0i)
```cpp
#include <iostream>

template<typename T>
struct Node {
	Node<T>() : next(nullptr), value(0) {}
	Node<T>* next;
	int value;
};

template<typename T>
class ForwardList {
public:
	ForwardList() : mFirst(nullptr) {}

	void push_back(int n) {

		Node<T>* node = new Node<T>();
		node->value = n;
		if (mFirst == nullptr) {
			mFirst = node;
		}
		else if (mFirst != nullptr) {
			Node<T>* it = mFirst;
			while (it->next != nullptr) it = it->next;
			it->next = node;
		}
	}

	void reverse() {
		if (mFirst == nullptr || mFirst->next == nullptr) return;

		Node<T>* it = mFirst;
		Node<T>* prevPointer = nullptr;
		Node<T>* nextPointer = nullptr;
		while(it->next != nullptr) {
			nextPointer = it->next;
			it->next = prevPointer;
			prevPointer = it;
			it = nextPointer;
		}
		it->next = prevPointer;
		mFirst = it;

	}

public:
	Node<T>* first() {
		return mFirst;
	}

private:
	Node<T>* mFirst;		
};


int main() {
	ForwardList<int> list;
	list.push_back(15);
	list.push_back(14);
	list.push_back(36);
	list.push_back(42);

	auto first = list.first();

	std::cout << "Show nodes" << std::endl;

	while (first != nullptr) {
		std::cout << first->value << " " << first << "\n";
		first = first->next;
	}

	std::cout << "\nReverse:\n";
	list.reverse();

	first = list.first();

	while (first != nullptr) {
		std::cout << first->value << " " << first << "\n";
		first = first->next;
	}

	return 0;
}
```
## Template magic
### SFINAE [Run in ideone.com](http://ideone.com/ER71cf)
```cpp
#include <iostream>
using namespace std;

class Cat {
public:
	typedef int holder;
};

class Bank {};

class Dog {};

template <typename T>
struct HasHolderTypedef {
	template <typename C> static char (&f(typename C::holder* c))[1];
	template <typename C> static char (&f(...))[2];

	enum { value = sizeof(f<T>(nullptr)) == sizeof(char[1]) };
};

int main() {
	cout << HasHolderTypedef<Cat>::value << endl;
	cout << HasHolderTypedef<Bank>::value << endl;
	cout << HasHolderTypedef<Dog>::value << endl;
	return 0;
}
```
### Compile time power and template argument in range checker [Run in ideone.com](http://ideone.com/n2QzYO)
```cpp
#include <iostream>

template <int n, int p>
struct Pow {
	enum { value = n * Pow<n, p-1>::value };
};

template <int n>
struct Pow<n, 0> {
	enum { value = 1 };
};

template <int n>
struct Select {
};

template <>
struct Select<1> {
	typedef void NOT_IN_RANGE_ERROR;
};

template <uintmax_t n, typename T>
struct is_in_range {
	typedef typename Select<n <= Pow<2, sizeof(T)*8>::value>::NOT_IN_RANGE_ERROR check;		
};

// client
template <int r_arg>
class Color {
public:
	Color(int r = r_arg) {}
private:
    typedef typename is_in_range<r_arg, uint8_t>::check IDONTCARE;
};

int main() {
	Color<25> g; // ok
	//Color<262> b; // compile-time error
	return 0;
}
```
### Extract type of expression [Run in ideone.com](http://ideone.com/vzdGI5)
```cpp
#include <iostream>
#include <typeinfo>

template <typename T, typename... Args>
struct get_type {
	typedef typename get_type<Args...>::value_type value_type;
};

template<typename T>
struct get_type<T> {
	typedef T value_type;
};


template <typename T> T expr(T t) {
	return t;
}

template <typename T, typename... Args>
typename get_type<Args...>::value_type expr(T t, Args... args) {
	return expr(args...);
}

int main() {
	std::cout << expr(1, 2, 3, "43eee");
	return 0;
}
```
### Compile time algorithm to find max value of numbers [Run in ideone.com](http://ideone.com/39F2BT)
```cpp
#include <iostream>

template <typename T>
constexpr T max_val(T t1, T t2) {
	return t1 < t2 ? t2 : t1;
}

template <typename T, typename... Args>
constexpr T max_val(T value, Args... args) {
	return max_val(value, max_val(args...));
}

int main() {
	constexpr int maxVal(max_val(4, 2, 1, 7, 3, 12, 0, 5));
	std::cout << maxVal;

	return 0;
}
```

## Other
### Own signals [Run in ideone.com](http://ideone.com/rEmp8r)
```cpp
#include <iostream>
#include <vector>
#include <list>
#include <functional>
#include <type_traits>

template<typename T> struct DiscardCombine { void push(T) {} };

template<typename F, typename Combine = DiscardCombine<typename std::function<F>::result_type>>
class Signal {
public:
	void connect(std::function<F> func) {
		static_assert(std::is_member_function_pointer<decltype(&Combine::push)>::value,
                  "A::member is not a member function.");
		mBinds.push_back(func);
	}

	template<typename... Args>
	Combine emit(Args... arg) {
		Combine c;
		for (auto func : mBinds) c.push(func(arg...));
		return c;
	}
	std::list<std::function<F>> mBinds;
};

// client
template<typename T>
struct StoreWorkResult {
	void push(T i) {
		result.push_back(i);
	}
	std::vector<T> result;
};

class Boss {
public:
	Signal<int(int), StoreWorkResult<int>> onOrder;

	void makeProject() {
		auto res = onOrder.emit(1).result;
		for(auto r : res) std::cout << r << std::endl;
	}
};

struct Worker {
	void doTask() { std::cout << "task\n"; }
};

int main() {
	Worker w1;
	Worker w2;

	Boss boss;

	boss.onOrder.connect([&w1](int e1) { w1.doTask(); return 195; });
	boss.onOrder.connect([&w2](int e2) { w2.doTask(); return 15;  });

	std::cout << "Emit signals:\n";
	boss.makeProject();
	return 0;
}
```
### Multiply duration and binary shift duration test in threads [Run in ideone.com](http://ideone.com/M7Gexw)
```cpp
#include <iostream>
#include <chrono>
#include <functional>
#include <thread>

using namespace std::chrono;

long long multiply(int number, int multiplier) {
    return number *= multiplier;
}

long long binaryShift(int number, int shiftValue) {
	return number << shiftValue;
}

long long test(std::function<long long()> testable, long count) {
	unsigned long long acc = 0;
	for (int i = 0; i < count; ++i) acc = acc + testable();
	return acc;
}

int main() {
    int multiplier = 2;
    int shiftValue = 1;
    int number     = 3;
    long count = 70'000'000;

	std::function<long long()> f_multiply 	 = std::bind(multiply, 	  number, multiplier);
	std::function<long long()> f_binaryShift = std::bind(binaryShift, number, shiftValue);

	long long    dur1 = 0;
	long long result1 = 0;

	std::thread thread1( [&count, &f_multiply, &dur1, &result1] () {
		auto t1 = high_resolution_clock::now();
    	result1 = test(f_multiply, count);
    	auto t2 = high_resolution_clock::now();
    	dur1 = duration_cast<microseconds>( t2 - t1 ).count();
		});


	long long    dur2 = 0;
	long long result2 = 0;
		std::thread thread2( [&count, &f_binaryShift, &dur2, &result2] () {
		auto t1 = high_resolution_clock::now();
    	result2 = test(f_binaryShift, count);
    	auto t2 = high_resolution_clock::now();
    	dur2 = duration_cast<microseconds>( t2 - t1 ).count();
		});

	thread2.join();
	thread1.join();

    auto t1 = high_resolution_clock::now();

    int n1 = test(f_multiply, count);

    auto t2 = high_resolution_clock::now();

	int n2 = test(f_binaryShift, count);

	auto t3 = high_resolution_clock::now();

    auto duration1 = duration_cast<microseconds>( t2 - t1 ).count();
	auto duration2 = duration_cast<microseconds>( t3 - t2 ).count();

    std::cout << "multiply duration: " << duration1
    		  << "\nbinShift duration: " << duration2 << "\nmultyiply n1: " << n1 << "\nbinary shift n2: " << n2;

	std::cout << "\n\nWith different threads:\n\n";
	std::cout << "multiply duration: " << dur1 << "\nbinShift duration: " << dur2 << "\n"
			  << "multiply n1: " << result1 << "\nbinary shift n2: " << result2;
    return 0;
}
```

### string to int conversion with tests [Run in ideone.com](http://ideone.com/z6r7fh)
```cpp
#include <iostream>
#include <string>
#include <list>

int toDigit(const char c, bool& ok) {
	int digit = 0;
	ok = true;
	switch(c) {
	case '0': digit = 0; break;
	case '1': digit = 1; break;
	case '2': digit = 2; break;
	case '3': digit = 3; break;
	case '4': digit = 4; break;
	case '5': digit = 5; break;
	case '6': digit = 6; break;
	case '7': digit = 7; break;
	case '8': digit = 8; break;
	case '9': digit = 9; break;
	default: ok = false;
	}
	return digit;
}

int toDigit(const char c) {
	bool tmp;
	return toDigit(c, tmp);
}

int toInt(const char* str, const size_t size, bool& ok) {
	int result = 0;
	int factor = 1;
	bool isNegative = str[0] == '-';
	int index = size - 1;
	int lastIndex = isNegative? 1 : 0;
	ok = !(isNegative && size == 1);
	if (ok) {
		while (index >= lastIndex) {
			result += toDigit(str[index], ok) * factor;
			if (!ok) break;
			factor *= 10;
			--index;
		}
	}
	return isNegative? - 1 * result : result;
}

int toInt(std::string str, bool& ok) {
	return toInt(str.c_str(), str.size(), ok);
}

int toInt(std::string str) {
	bool tmp;
	return toInt(str, tmp);
}

int main() {
	std::list<int> tests;
	bool ok = true;
	tests.push_back(toInt("-123009"		,ok) == -123009 && ok);
	tests.push_back(toInt("123009"		,ok) == 123009  && ok);
	tests.push_back( toInt("-0"			,ok) == 0 		&& ok);
	tests.push_back( toInt("0"			,ok) == 0  		&& ok);
	tests.push_back( toInt("1231"		,ok) == 1231	&& ok);
	tests.push_back( toInt("00000"		,ok) == 0		&& ok);
	tests.push_back( toInt("999999"		,ok) == 999999  && ok);
	tests.push_back( toInt("-999999"	,ok) == -999999 && ok);
	tests.push_back(  toInt("0"			,ok) == 0 		&& ok);
	tests.push_back(  toInt("1"			,ok) == 1 		&& ok);
	tests.push_back(  toInt("-1"		,ok) == -1 		&& ok);
	tests.push_back(  toInt("-"			,ok) == -1 		&& ok);
	tests.push_back(  toInt("-----1"	,ok) == -1 		&& ok); // ????
	for (auto test : tests) std::cout << test;
	return 0;
}
```

### gcc __attribute((__packed__)) extension [Run in ideone.com](http://ideone.com/2014Fd)
```cpp
#include <iostream>

struct __attribute__((__packed__)) Node {
	char text;
	bool ok;
	Node* next;
};

struct Node2 {
	char text;
	bool ok;
	Node* next;
};

int main() {
	Node node;
	Node2 node2;

	std::cout << sizeof(node) << std::endl << sizeof(node2);
	return 0;
}
```
### std::chrono - elapsed time example [Run in ideone.com](http://ideone.com/c1LAtS)
```cpp
#include <iostream>
#include <vector>
#include <chrono>

using namespace std;
using  ns = chrono::nanoseconds;
using get_time = chrono::steady_clock ;

int answer_to_everything() {
	return 42;
}

int main() {
	auto start = get_time::now();
	std::vector<int> v = std::vector<int>(4000, 99);
	auto end = get_time::now();
	auto diff = end - start;
	cout << "Elapsed time is :  " << chrono::duration_cast<ns>(diff).count() << " ns " << endl;
}
```
### std::functional lamdas in constructor as concept [Run in ideone.com](http://ideone.com/J5VYN6)
```cpp
#include <iostream>
#include <functional>
using namespace std;

namespace nsA { //.h
  class A {
  public:
  	A();
  	auto getA() -> int;
  	auto getB() -> int;
  	auto getC() -> int;
  private:
  	int mA;
  	int mB;
  	int mC;
  };
}

template <typename F>
F wrapper(F f) {
	f();
	return f;
}

namespace nsA {//.cpp
  A::A() : mA(0), mB(0), mC(0) {

  	auto createA = wrapper([&] () -> void { mA = 15;  });
  	std::function<void()> createB = wrapper([&] () -> void { mB = 111; });
  	std::function<void()> createC = wrapper([&] () -> void { mC = 788; });
  }

  auto A::getA() -> int { return mA; }
  auto A::getB() -> int { return mB; }
  auto A::getC() -> int { return mC; }
}


int main() {
  nsA::A a;
  cout << a.getA() << " " << a.getB() << " " << a.getC();
  return 0;
}

```

### .bmp (.ppm) image generator [Run in ideone.com](http://ideone.com/eyiMgV) ! BAD-CODE STYLE
```cpp
#include <iostream>
#include <ctime>
#include <cstdlib>

using namespace std;

struct PpmImage {
  PpmImage(uint32_t w, uint32_t h) : width(w), height(h), dataSize(w * h * 3), data(new uint8_t[dataSize]) {
    for (size_t _ = 0; _ < dataSize; _++) data[_] = rand();
  }

  ~PpmImage() { delete[] data; }

  operator int() const { return dataSize; }

  uint32_t width;
  uint32_t height;
  size_t   dataSize;
  uint8_t* data;
};

auto save(FILE* out, const PpmImage& img) -> size_t {
  return fprintf(out, "P6\n# \n%d %d\n%d\n", img.width, img.height, 0xFF) + fwrite(img.data, 1, img.dataSize, out);
}

auto toHexTriplet(int r, int g, int b) -> std::string {
	char result[8];
	snprintf(result, 8, "#%02X%02X%02X\n", r, g, b); //thx Aleksey Ponasenko for C-style
	return std::string(result + std::string(" "));
}

auto print(const PpmImage& img) -> void {
	for (int _ = 0; _ <= img; _+=3) cout << ((_ % img.width) ? toHexTriplet(img.data[_], img.data[_+1], img.data[_+2]) : "\n");
}

int main() {
  print(PpmImage([](int $ =0) {return cin >>$,$;}() ,[](int _ =0) {return cin >>_,_;}()));
  cout << "\nDone";
  return 0;
}
```

