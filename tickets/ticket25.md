## �������� � ������  

�������������� ������, ������ ���������� ������� - ��� ������, � ���� ��������� ��������� ������, ��� � �������.

���������� ����� ����:

* �������
* ��������� �� ������� 
* ������� � ������������� `operator()`

### `operator()`

```c++

struct Greater {
	bool operator()(int a, int b) const {
		return a > b;
	}
	bool operator()(int a) const {                    // overloads are possible
	    return a > 0;
	}
	bool operator()() const {
	    return true;
	}
};

int main() {
	const Greater comparator;

	std::cout << comparator.operator()();              // true
	std::cout << comparator.operator()(-8);            // false
	std::cout << comparator.operator()(100, 99);       // true

	std::cout << comparator();                         // true
	std::cout << comparator(-8);                       // false
	std::cout << comparator(100, 99);                  // true

	std::cout << Greater().operator()();               // true
	std::cout << Greater()(-8);                        // false
	
	std::vector<int> vec{1, 2, 3, 4, 5, 6, 7, 8}; 
	std::sort(vec.begin(), vec.end(), comparator);     // will reverse vector
}

```

`operator()` ������� �������� `const`, �.�. ������ ��������� �� ����� ���������� ���������


### ������������� ���������

� ������� ���� ������� ��������� � `std::sort` � �������� �����������. ��� �� ��� ������ �������� � `std::set`. �.�.
set ����� ����������� ���������� � ���� (��� � ����� �� � STL) � �������, 
��� ���������� �� ����� ������ ��������� ��� ������, ��� ���`operator()` ������� �������� `const`. 

```c++
int levenshtein_distance(std::string const& a, std::string const& b);


struct Closer {
    std::string template;

    bool operator()(std::string const& a, std::string const& b) const {  // 'const' is important
        return levenshtein_ditance(a, template) < levenshtein_ditance(b, template);
    }
};

int main() {
    using namespace std::literals;
    std::set<std::string, Closer> v1({"meow"s, "woof"s, "oink"s, "quack"s}, Closer{"meow"s});
    std::set<std::string, Closer> v2({"meow"s, "woof"s, "oink"s, "quack"s}, Closer{"woof"s});
}

```

��� ���������� `std::set` ����������� ���� ���������. 

������ ��������� � `operator()` �������� �� ���������� ������ - ����� �������, ���� ���������� ����� �������� ���� ���������, 
� ����������� �������� �����.  

�������, ��� ��-�� ����, ��� ����������� ���������� STL ��������� �������� � ���������� �����������, ��� �������� 
��������, ��� ��������� � �������� `std::all_of`, `std::any_of`, `std::for_each` � �.�. ��������� �������� ����� �� ����������.
`std::for_each` ���������� ������� ����� ���������� ��� � ����������, ��� ��� � ��� ��������� ��-���� ����� ���-�� �������.

```c++
struct Functor {
	int val = 0;
	void operator()(int) {
		val++;
	}
};

int main() {
	Functor c;
	std::vector<int> v(100, 0);
	auto used = std::for_each(v.begin(), v.end(), c); // ���� operator() ����� ��������� c, �� ���� ����� ��������� �� c
	std::cout << c.val << std::endl;                  // 0
	std::cout << used.val << std::endl;               // 100
}

```

����������� ����� ���������� �  `std::function`, ��� ���� �� ���������� � ������ � dynamic storage duration
(�� ���� ��� ��� small object optimization)

��� �������� ������������ ���� ��� `std::reference_wrapper` �� ���������� `functional`, 
������� ��������� ��������� ������ �� �������� � `std::vector` ���
���-������ ��� � ����������� ������ `operator()`, �������� ����������� �������� �������, ������� �� ������.

������������ ������ �������� `std::reference_wrapper` -  `auto func = std::ref(functor);`

� ����� ���� ���� ����� `get`, ������������ ������� ������ �� ������, �� ������� ��������� `std::reference_wrapper`.

������ �������� ����� ���������� � ��������� stl


```c++
struct Functor {
	int val = 0;
	void operator()(int) {
		val++;
	}
};

int main() {
	Functor c;
	std::vector<int> v(100, 0);
	std::for_each(v.begin(), v.end(), std::ref(c)); // ���� operator() ����� ��������� c, �� ���� ����� ��������� �� c
	std::cout << c.val << std::endl;                // 100
}
```
`std::reference_wrapper` �� �������, ��� �� ��� ��������� � ����� ���� �������.

###������

������-��������� - �������������� ����� ��� �������� ���������. ��� ���������� �������� ���������� �������,
������� ��������� ������� ����� ���� ����� ������ ����� `auto`. ��������� ������-��������� `[<capture>](<arguments>){<body>}`.
��� ������������� ��������� �������� ��������� ��� (�� ����� ���� ��� ����� ������ ��������� ��������� `-> Type`).

���� ������������� ������� �������� ����������� � ���������:

* `[=]` - ��� ������������ � ���� ��������� ����������� �� �������� scope ���������� ������ ��������.
* `[&]` - ��� ������������ � ���� ��������� ����������� �������� � �������� �� ������.
* `[this]` - ������� `this` ������������� �� ������
* `[\*this]` - ������� `this` ������������� �� ��������
* `[name = expression]` - ������ � �������� ����� ���� name, ����������� ���� `expression` (��� ���� ��������� ���)
* `[name]` - �������� � ������� ���������� `name`
* `[&name]` - ������ � �������� ������ �� ���������� `name`

�� ��������� `operator()` � ������������� �������� �������, ��� `const`. �������� ����� �����, ������� ����� `mutable` ����� ����� ������.

�������

```C++
int main() {
    auto lambda = [val = 0](int) mutable { std::cout << val++ << '\n'; };
    std::vector<int> v(100, 0);
    std::for_each(v.begin(), v.end(), lambda); // numbers from 0 to 99
}
```

[������������ ���](https://cppinsights.io/lnk?code=I2luY2x1ZGUgPGlvc3RyZWFtPgojaW5jbHVkZSA8dmVjdG9yPgojaW5jbHVkZSA8ZnVuY3Rpb25hbD4KCmludCBtYWluKCkgewogICAgYXV0byBsYW1iZGEgPSBbdmFsID0gMF0oaW50KSBtdXRhYmxlIHsgc3RkOjpjb3V0IDw8IHZhbCsrIDw8ICdcbic7IH07CiAgICBzdGQ6OnZlY3RvcjxpbnQ+IHYoMTAwLCAwKTsKICAgIHN0ZDo6Zm9yX2VhY2godi5iZWdpbigpLCB2LmVuZCgpLCBsYW1iZGEpOyAvLyBudW1iZXJzIGZyb20gMCB0byA5OQp9Cg==&insightsOptions=cpp17&std=cpp17&rev=1.0)

���� ��������� ������ ��������� �� �������� �� ����� ������� - ���������� ����������� `[this]` ��� `[*this]`.

```c++

struct Foo {
  int a;
  auto copy_capture() {
  	return [*this](){
      std::cout << a << std::endl;
    };
  }
};

int main()
{
  auto lambda1 = Foo{1}.copy_capture();
  lambda1(); // no dangling reference
}

```

[������������ ���](https://cppinsights.io/lnk?code=I2luY2x1ZGUgPGlvc3RyZWFtPgoKc3RydWN0IEZvbyB7CiAgaW50IGE7CiAgYXV0byBjb3B5X2NhcHR1cmUoKSB7CiAgCXJldHVybiBbKnRoaXNdKCl7CiAgICAgIHN0ZDo6Y291dCA8PCBhIDw8IHN0ZDo6ZW5kbDsKICAgIH07CiAgfQp9OwoKaW50IG1haW4oKQp7CiAgYXV0byBsYW1iZGExID0gRm9vezF9LmNvcHlfY2FwdHVyZSgpOwogIGxhbWJkYTEoKTsgLy8gbm8gZGFuZ2xpbmcgcmVmZXJlbmNlCn0=&insightsOptions=cpp17&std=cpp17&rev=1.0)


�������� ��������� ������ �������� � ������ `[field = std::move(value)]` - 	���, ��������, ������������ ������ ��������
� ������ `std::unique_ptr`. ������������� `move` ��� ���, ������� ������ ����� ���� ������� ���������� ������.

**������ �����** _������_ ~~������~~ ==������?== `inline code block` [������ �� ����](https://www.markdownguide.org/cheat-sheet/)
```c++
int main() {
    return 0;
}
```

### ������������

LMAO
Not bottom text, lol
