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
	std::cout << comparator(-8);                       // true
	std::cout << comparator(100, 99);                  // true

	std::cout << Greater().operator()();               // true
	std::cout << Greater()(-8);                        // true
	
	std::vector<int> vec{1, 2, 3, 4, 5, 6, 7, 8}; 
	std::sort(vec.begin(), vec.end(), comparator);          // will reverse vector
}

```

`operator()` ������� �������� `const`, �.�. ������ ��������� �� ����� ���������� ���������


### ������������� ���������

� ������� ���� ������� ��������� � `std::sort` � �������� �����������. ��� �� ��� ������ �������� � `std::set`. �.�.
set ����� ����������� ���������� � ���� (��� � ����� �� � STL) � �������, 
��� ���������� �� ����� ������ ��������� ��� ������, ��� ���`operator()` ������� �������� `const`. 

```c++
int levenshtein_ditance(std::string const& a, std::string const& b);


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

����������� ����� ���������� �  `std::function`, ��� ���� �� ���������� �� ���� (�� ���� ��� ��� small object optimization)

��� �������� ������������ ���� ��� `std::reference_wrapper`, ������� ��������� ��������� ������ �� �������� � `std::vector` ���
���-������ ��� � ����������� ������ `operator()`.

������������ ������ �������� `std::reference_wrapper` -  `auto func = std::ref(functor);`

� ����� ���� ���� ����� `get`, ������������ ������� ������ �� ������, �� ������� ��������� `std::reference_wrapper`.


**������ �����** _������_ ~~������~~ ==������?== `inline code block` [������ �� ����](https://www.markdownguide.org/cheat-sheet/)
```c++
int main() {
    return 0;
}
```

### ������������

LMAO
Not bottom text, lol
