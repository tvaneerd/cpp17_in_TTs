
Descriptions of C++17 features, presented mostly in "Tony Tables" (hey, the tables were my idea, but their name wasn't :-).

There are actually **over 100 changes in C++17**, only some of them are listed here.

Caveat1: *C++17 is completed, but not signed off yet.* There may still be changes, although _highly_ unlikely (modulo defects).  
Caveat2: *I make mistakes.*  This is more likely :-)

Created with the support of my employer, [Christie Digital Systems](https://www.christiedigital.com/en-us/business/solutions/projection-mapping).

---
if-init
---

<table>
<tr>
<th>
C++
</th>
</tr>
<tr>
<td  valign="top">
<pre lang="cpp">
{
   if (Foo * ptr = get_foo())
      use(*ptr);
   more_code();
}
</pre>
</td>
</tr>
</table>

But what do you do when it isn't (convertible to) boolean?

<table>
<tr>
<th>
C++
</th>

</tr>
<tr>
<td  valign="top">
<pre lang="cpp">
{
   {
      QVariant var = getAnswer();
      if (var.isValid())
         use(var);
   }
   more_code();
}
</pre>
</td>
</tr>
</table>

<table>
<tr>
<th>
C++
</th>
<th>
C++17
</th>
</tr>
<tr>
<td  valign="top">
<pre lang="cpp">
{
   {
      QVariant var = getAnswer();
      if (var.isValid())
         use(var);
   }
   more_code();
}
</pre>
</td>
<td  valign="top">
<pre lang="cpp">
{
   
   
   if (QVariant var = getAnswer(); var.isValid())
      use(var);
      
   more_code();
}
</pre>
</td>
</tr>
</table>

Switch statements too!


<table>
<tr>
<th>
C++17
</th>
</tr>
<tr>
<td  valign="top">
<pre lang="cpp">
{
   switch (Device dev = get_device(); dev.state())
   {
   case sleep: /*...*/ break;
   case ready: /*...*/ break;
   case bad: /*...*/ break;
   }
}
</pre>
</td>
</tr>
</table>

Structured Bindings 
-------------------

<table>
<tr>
<th>
C++14
</th>
<th>
C++14
</th>
<th>
C++17
</th>
</tr>
<tr>
<td  valign="top">
<pre lang="cpp">
   tuple&lt;int, string&gt; stuff();
   
   auto tup = stuff();
   int i = get&lt;0&gt;(tup);
   string s = get&lt;1&gt;(tup);
  
   use(s, ++i);
</pre>
</td>
<td  valign="top">
<pre lang="cpp">
   tuple&lt;int, string&gt; stuff();
   
   int i;
   string s;
   std::tie(i,s) = stuff();

   use(s, ++i);
</pre>
</td>
<td valign="top">
<pre lang="cpp">
   tuple&lt;int, string&gt; stuff();
   
   
   auto [ i, s ] = stuff();


   use(s, ++i);
</pre>
</td>
</tr>
</table>



<table>
<tr>
<th>
C++17
</th>
<th>
compiler
</th>
</tr>
<tr>
<td valign="top">
<pre lang="cpp">
   pair&lt;int, string&gt; stuff();
   
   
   auto [ i, s ] = stuff();


   use(s, ++i);
</pre>
</td>
<td valign="top">
<pre lang="cpp">
   pair&lt;int, string&gt; stuff();
   
   auto __tmp = stuff();
   auto &amp; i = get&lt;0&gt;(__tmp);
   auto &amp; s = get&lt;1&gt;(__tmp);

   use(s, ++i);
</pre>
</td>
</tr>
</table>


Note, in the above, `__tmp` is a copy, but `i` and `s` are references. Or I should say "references" in quotes. Not exactly references, but real compiler synonyms for the members. (They are not real references as things like `decltype` "look through" the references to the actual members.)

So even though `auto [i,s] = stuff();` has no `&` anywhere, there are still references involved.  For example:

<table>
<tr>
<th>
C++17
</th>
<th>
compiler
</th>
</tr>
<tr>
<td valign="top">
<pre lang="cpp">
#include &lt;string&gt;
#include &lt;iostream&gt;

struct Foo
{
   int x = 0;
   std::string str = "world";
   ~Foo() { std::cout &lt;&lt; s; }
};

int main()
{
    auto [ i, s ] = Foo();
    std::cout &lt;&lt; "hello ";
    s = "structured bindings";
}
</pre>
</td>
<td valign="top">
<pre lang="cpp">
#include &lt;string&gt;
#include &lt;iostream&gt;

struct Foo
{
   int x = 0;
   std::string str = "world";
   ~Foo() { std::cout &lt;&lt; s; }
};

int main()
{
    auto __tmp = Foo();
    std::cout &lt;&lt; "hello ";
    __tmp.str = "structured bindings";
}
</pre>
</td>
</tr>
<th colspan="2">Output</th>
<tr><td colspan="2" align="center">hello structued bindings</td></tr>
</table>

Note that the `s = "structured bindings";` is modifying `Foo::str` _inside_ of the temporary (hidden) `Foo`, so that when the temporary `Foo` is destroyed, its destructor prints `structured bindings` instead of `world`.

So what _does_ a `&` do in a structured binding declaration?  
It gets applied to the hidden `__tmp` variable:


<table>
<tr>
<th>
C++17
</th>
<th>
compiler
</th>
</tr>
<tr>
<td valign="top">
<pre lang="cpp">
   struct X { int i = 0; };
   X makeX();
   
   X x;
   
   auto [ b ] = makeX();
   b++;
   auto const [ c ] = makeX();
   c++;
   auto &amp; [ d ] = makeX();
   d++;
   auto &amp; [ e ] = x;
   e++;
   auto const &amp; [ f ] = makeX();
   f++;
</pre>
</td>
<td valign="top">
<pre lang="cpp">
   struct X { int i = 0; };
   X makeX();
   
   X x;
   
   auto __tmp1 = makeX();
   __tmp1.i++;
   auto const __tmp2 = makeX();
   __tmp2.i++; //error: can't modify const
   auto &amp; __tmp3 = makeX(); //error: non-const ref cannot bind to temp
   
   auto &amp; _tmp3 = x;
   x.i++;
   auto const &amp; _tmp4 = makeX();
   __tmp4.i++; //error: can't modify const
</pre>
</td>
</tr>
</table>


Wait, pair and tuple are not magic (just nearly impossible to write to STL level), can *my* types work with this?

**YES**.  The compiler uses `get<N>()` if available, or can work with plain structs directly:

**Structs**

<table>
<tr>
<th>
C++17
</th>
<th>compiler</th>
</tr>
<tr>
<td valign="top">
<pre lang="cpp">
   struct Foo {
      int x;
      string str;
   };
   
   Foo stuff();
     
     
   auto [ i, s ] = stuff();


   use(s, ++i);
</pre>
</td>
<td valign="top">
<pre lang="cpp">
   struct Foo {
      int x;
      string str;
   };
   
   Foo stuff();
   
   Foo __tmp = stuff();
   auto &amp; i = __tmp.x;
   auto &amp; s = __tmp.str;

   use(s, ++i);
</pre>
</td></tr>
</table>


**Implement your own get()**


<table>
<tr>
<th>
C++17
</th>
</tr>
<tr>
<td valign="top">
<pre lang="cpp">
   class Foo {
      // ...
   public:
      template &lt;int N&gt; auto get() /*const?*/ { /*...*/ }
   };
   namespace std {
      template ... tuple_size ...
      template ... tuple_element ...
   }
   // or get outside class
   template&lt;int N&gt; auto get(Foo /*const?*/ &amp; foo) { /*...*/ }
   //...
   
   Foo stuff();

   auto [ i, s ] = stuff();

   use(s, ++i);
</pre>
</td>
</tr>
</table>

**Arrays, std::array, etc, oh my!**

<table>
<tr>
<th>
etc
</th>
</tr>
<tr>
<td valign="top">
<pre lang="cpp">
    
   int arr[4] = { /*...*/ };
   auto [ a, b, c, d ] = arr; 
   auto [ t, u, v ] = std::array&lt;int,3&gt;();
   
   // now we're talkin'
   for (auto &amp;&amp; [key, value] : my_map)
   {
      //...
   }
</pre>
</td>
</tr>
</table>
Constexpr If
------------


Actually, how _would_ someone write a custom `get<>()` function for their class?
(see [Structured Bindings](structured_bindings.md) for why you might want to do that)
Since each `get<0>`, `get<1>`, etc returns a different member, which are possibly different types...
(oh no, template metaprogramming...)

<table>
<tr>
<th>
C++14
</th>
<th>
C++17
</th>
</tr>
<tr>
<td valign="top">
<pre lang="cpp">
class Foo {
  int myInt;
  string myString;
public:
  int const &amp; refInt() { return myInt; }
  string const &amp; refString() { return myString; }
};



// Hey compiler, keep an eye out for this template:
template&lt;int N&gt; void get(Foo const &amp; foo)
{
  static_assert(false, "Foo only has 2 members");
}
// here's some specializations (the real stuff)
template&lt;&gt; int const &amp; get&lt;0&gt;(Foo const &amp; foo)
{
  return foo.refInt();
}
template&lt;&gt; string const &amp; get&lt;1&gt;(Foo const &amp; foo)
{
  return foo.refString();
}
</pre>
</td>
<td valign="top">
<pre lang="cpp">
class Foo {
  int myInt;
  string myString;
public:
  int const &amp; refInt() { return myInt; }
  string const &amp; refString() { return myString; }
};



template&lt;int N&gt; auto &amp; get(Foo const &amp; foo)
{
  static_assert(0 &lt;= N &amp;&amp; N &lt; 2, "Foo only has 2 members");

  if constexpr (N == 0)  // !! LOOK HERE !!
     return foo.refInt();
  else if constexpr (N == 1)    // !! LOOK HERE !!
     return foo.refString();
}
</pre>
</td>
</tr>
</table>


P.S. `if constexpr (expression)` doesn't _check_ if the expression is constexpr.  The expression _must_ be constexpr (else it doesn't compile).  The part that is constexpr is 'doing' the if.  Don't think about this and what syntax might be better.  The committee argued about it long enough.
Deduction Guides
---

Speaking of pair and tuple...

<table>
<tr>
<th>
C++14
</th>
<th>
C++17
</th>
</tr>
<tr>
<td  valign="top">
<pre lang="cpp">
pair&lt;int, string&gt; is1 = pair&lt;int, string&gt;(17, "hello");
auto is2 = std::pair&lt;int, string&gt;(17, "hello");
auto is3 = std::make_pair(17, string("hello"));
auto is4 = std::make_pair(17, "hello"s);
</pre>
</td>
<td  valign="top">
<pre lang="cpp">
pair&lt;int, string&gt; is1 = pair(17, "hello");
auto is2 = pair(17, "hello"); // !! pair&lt;int, char const *&gt;
auto is3 = pair(17, string("hello"));
auto is4 = pair(17, "hello"s);
</pre>
</td>
</tr>
</table>

The magic behind the above is called "deduction guides".  In particular, _implicit_ deduction guides, and _explicit_ deduction guides.

Explicit Deduction Guides
---


<pre lang=cpp>
template&lt;typename T&gt;
struct Thingy
{
  T t;
};

// !! LOOK HERE !!
Thingy(const char *) -> Thingy&lt;std::string&gt;;

Thingy thing{"A String"}; // thing.t is a `std::string`.
</pre>
_(example from Nicol Bolas)_


Implicit Deduction Guides
---

For any `template<typename T, typename U, etc> struct`... (or class!)
if there is a constructor that takes `T` and `U` such that it can figure out all the types,
then that constructor forms an "implicit" deduction guide.  ie just like the explicit one above, but the compiler does it for you.

More importantly, the above should say **for all** templatized types...  ie whether you want it or not.

template\<auto>
---

<table>
<tr>
<th>
C++14
</th>
<th>
C++17
</th>
</tr>
<tr>
<td  valign="top">
<pre lang="cpp">
template &lt;typename T, T v&gt;
struct integral_constant
{
   static constexpr T value = v;
};
integral_constant&lt;int, 2048&gt;::value
integral_constant&lt;char, 'a'&gt;::value
</pre>
</td>
<td  valign="top">
<pre lang="cpp">
template &lt;auto v&gt;
struct integral_constant
{
   static constexpr auto value = v;
};
integral_constant&lt;2048&gt;::value
integral_constant&lt;'a'&gt;::value
</pre>
</td>
</tr>
</table>








Fold Expressions
---


<table>
<tr>
<th>
How do you write `sum()` ?
</th>
</tr>
<tr>
<td  valign="top">
<pre lang="cpp">
auto x = sum(5, 8);
auto y = sum(a, b, 17, 3.14, etc);
</pre>
</td>
</tr>
</table>

<table>
<tr>
<th>
C++14
</th>
<th>
C++17
</th>
</tr>
<tr>
<td  valign="top">
<pre lang="cpp">
auto sum() { return 0; }

template &lt;typename T&gt;
auto sum(T&amp;&amp; t) { return t; }

template &lt;typename T, typename... Rest&gt;
auto sum(T&amp;&amp; t, Rest&amp;&amp;... r) {
   return t + sum(std::forward&lt;Rest&gt;(r)...);
}
</pre>
</td>
<td  valign="top">
<pre lang="cpp">







template &lt;typename... Args&gt;
auto sum(Args&amp;&amp;... args) {
   return (args + ... + 0);
}
</pre>
</td>
</tr>
</table>
Nested Namespaces
---

<table>
<tr>
<th>
C++14
</th>
<th>
C++17
</th>
</tr>
<tr>
<td  valign="top">
<pre lang="cpp">
   namespace A {
      namespace B {
         namespace C {
            struct Foo { };
            //...
         }
      }
   }
</pre>
</td>
<td  valign="top">
<pre lang="cpp">
   namespace A::B::C {
      struct Foo { };
      //...
   }
</pre>
</td>
</tr>
</table>


Single Param static_assert
---


<table>
<tr>
<th>
C++14
</th>
<th>
C++17
</th>
</tr>
<tr>
<td  valign="top">
<pre lang="cpp">
static_assert(sizeof(short) == 2, "sizeof(short) == 2")
</pre>
</td>
<td  valign="top">
<pre lang="cpp">
static_assert(sizeof(short) == 2)
</pre>
</td>
</tr>
<tr>
<th colspan="2">Output</th>
</tr>
<tr>
<td colspan="2" align="center">static assertion failure: sizeof(short) == 2</td>
</tr>
</table>





Inline Variables
---


<table>
<tr>
<th>
C++14
</th>
<th>
C++17
</th>
</tr>
<tr>
<td  valign="top">
<pre lang="cpp">
// foo.h
extern int foo;

// foo.cpp
int foo = 10;
</pre>
</td>
<td  valign="top">
<pre lang="cpp">
// foo.h
inline int foo = 10;
</pre>
</td>
</tr>
</table>



<table>
<tr>
<th>
C++14
</th>
<th>
C++17
</th>
</tr>
<tr>
<td  valign="top">
<pre lang="cpp">
// foo.h
struct Foo {
   static int foo;
};

// foo.cpp
int Foo::foo = 10;
</pre>
</td>
<td  valign="top">
<pre lang="cpp">
// foo.h
struct Foo {
   static inline int foo = 10;
};
</pre>
</td>
</tr>
</table>






Guaranteed Copy Elision
---



<table>
<tr>
<th>
C++17
</th>
</tr>
<tr>
<td  valign="top">
<pre lang="cpp">

// header &lt;mutex&gt;
namespace std
{
   template &lt;typename M&gt;
   struct lock_guard
   {
      explicit lock_guard(M &amp; mutex);
      // not copyable, not movable:
      lock_guard(lock_guard const &amp; ) = delete;
      //...
   }
}

// your code
lock_guard&lt;mutex&gt; grab_lock(mutex &amp; mtx)
{
   return lock_guard&lt;mutex&gt;(mtx);
}

mutex mtx;

void foo()
{
   auto guard = grab_lock(mtx);
   /* do stuff holding lock */
}
</pre>
</td>
</tr>
</table>






some new [[attributes]]
---


**[[fallthrough]]**

<table>
<tr>
<th>
C++14
</th>
<th>
C++17
</th>
</tr>
<tr>
<td  valign="top">
<pre lang="cpp">
switch (device.status())
{
case sleep:
   device.wake();
   // fall thru
case ready:
   device.run();
   break;
case bad:
   handle_error();
   break;
}
</pre>
</td>
<td  valign="top">
<pre lang="cpp">
switch (device.status())
{
case sleep:
   device.wake();
   [[fallthrough]];
case ready:
   device.run();
   break;
case bad:
   handle_error();
   break;
}
</pre>
</td>
</tr>
<tr>
<th>Compiler</th>
<th>Compiler</th>
</tr>
<tr>
<td>warning: case statement without break</td>
<td></td>
</tr>
</table>

**[[nodiscard]]**

On functions:
<table>
<tr>
<th>
C++14
</th>
<th>
C++17
</th>
</tr>
<tr>
<td  valign="top">
<pre lang="cpp">
struct SomeInts
{
   bool empty();
   void push_back(int);
   //etc
};

void random_fill(SomeInts &amp; container,
      int min, int max, int count)
{
   container.empty(); // empty it first
   for (int num : gen_rand(min, max, count))
      container.push_back(num);
}
</pre>
</td>
<td  valign="top">
<pre lang="cpp">
struct SomeInts
{
   [[nodiscard]] bool empty();
   void push_back(int);
   //etc
};

void random_fill(SomeInts &amp; container,
      int min, int max, int count)
{
   container.empty(); // empty it first
   for (int num : gen_rand(min, max, count))
      container.push_back(num);
}
</pre>
</td>
</tr>
<tr>
<th>Compiler</th>
<th>C++17 Compiler</th>
</tr>
<tr>
<td></td>
<td>warning: ignoring return value of 'bool empty()'</td>
</tr>
</table>

On classes or structs:
<table>
<tr>
<th>
C++14
</th>
<th>
C++17
</th>
</tr>
<tr>
<td  valign="top">
<pre lang="cpp">
struct MyError {
  std::string message;
  int code;
};

MyError divide(int a, int b) {
  if (b == 0) {
    return {"Division by zero", -1};
  }

  std::cout << (a / b) << '\n';

  return {};
}

divide(1, 2);
</pre>
</td>
<td  valign="top">
<pre lang="cpp">
struct [[nodiscard]] MyError {
  std::string message;
  int code;
};

MyError divide(int a, int b) {
  if (b == 0) {
    return {"Division by zero", -1};
  }

  std::cout << (a / b) << '\n';

  return {};
}

divide(1, 2);
</pre>
</td>
</tr>
<tr>
<th>Compiler</th>
<th>C++17 Compiler</th>
</tr>
<tr>
<td></td>
<td>warning: ignoring return value of function declared with 'nodiscard' attribute</td>
</tr>
</table>

Advice: use `[[nodiscard]]` **sparingly**. ie only when there really is _no_ reason to ignore the value.

**[[maybe_unused]]**



<table>
<tr>
<th>
C++14
</th>
<th>
C++17
</th>
</tr>
<tr>
<td  valign="top">
<pre lang="cpp">
   bool res = step1();
   assert(res);
   step2();
   etc();
</pre>
</td>
<td  valign="top">
<pre lang="cpp">
   [[maybe_unused]] bool res = step1();
   assert(res);
   step2();
   etc();
</pre>
</td>
</tr>
<tr>
<th>Compiler</th>
<th>C++17 Compiler</th>
</tr>
<tr>
<td>warning: unused variable 'res'</td>
<td></td>
</tr>
</table>






<table>
<tr>
<th>
C++17
</th>
</tr>
<tr>
<td  valign="top">
<pre lang="cpp">
   [[maybe_unused]] void f()
   {
      /*...*/
   }
   int main()
   {
   }
</pre>
</td>
</tr>
</table>


std::string_view
---

Standardization of existing practice.  See boost::string_ref, QStringRef, etc.

---

Let's say I write some kind of parser:

`Foo parseFoo(std::string const & input);`

But then I have some users using `char *` - and creating a `string` just to pass to the parser, so I add (or change to) this interface:

`Foo parseFoo(char const * str);`

But this parser becomes really popular.  Some are embedding Foos into the middle of their own formats - so no null at the end:

`Foo parseFoo(char const * str, int length);`

Oh, and we use a custom string class (or QString,...)

`Foo parseFoo(MyString const & str);`

etc!  How do you maintain this interface?


<table>
<tr>
<th>
C++14
</th>
<th>
C++17
</th>
</tr>
<tr>
<td  valign="top">
<pre lang="cpp">
Foo parseFoo(std::string const &amp; input);
Foo parseFoo(char const * str);

Foo parseFoo(char const * str, int length);




Foo parseFoo(MyString const &amp; str);
</pre>
</td>
<td  valign="top">
<pre lang="cpp">
Foo parseFoo(std::string_view input);

// I would say don't offer this interface, but:
Foo parseFoo(char const * str, int length)
{
   return parseFoo(string_view(str,length));
}

class MyString {
   //...
   operator string_view() const
   {
      return string_view(this->data, this->length);
   }
};
</pre>
</td>
</tr>
</table>

**Example 2**

Think of something like an XML parser, that is constantly returning `string` objects for the XML entities that it finds.
Each of those strings is a potential allocation.
So instead, return `string_view`.

**Caveats**

`string_view` does NOT own the string memory.  It points to memory owned elsewhere, similar to how a reference or pointer or iterator works.
It has _reference semantics_.
std::optional<T>
---

So, we have

`Foo parseFoo(std::string_view input);`

What if the parse fails?  And you can't parse out a Foo?

0. throw an exception
1. return default Foo. ie `Foo()` (if Foo is default constructible)
2. `bool parseFoo(std::string_view input, Foo & output);`  // also basically requires `Foo()`
3. `Foo * parseFoo(std::string_view input);`  // allocation!? :-(



<table>
<tr>
<th>
C++14
</th>
<th>
C++17
</th>
</tr>
<tr>
<td  valign="top">
<pre lang="cpp">
// returns default Foo on error
Foo parseFoo(std::string_view in);

// throws parse_error
Foo parseFoo(std::string_view in);

// returns false on error
bool parseFoo(std::string_view in, Foo &amp; output);

// returns null on error
unique_ptr&lt;Foo&gt; parseFoo(std::string_view in);

</pre>
</td>
<td  valign="top">
<pre lang="cpp">







std::optional&lt;Foo&gt; parseFoo(std::string_view in);
</pre>
</td>
</tr>
</table>





**Usage**

<table>
<tr>
<th>
C++17
</th>
</tr>
<tr>
<td  valign="top">
<pre lang="cpp">
optional ofoo = parseFoo(str);
if (ofoo)
   use(*ofoo);
</pre>
</td>
</tr>
<tr>
<td  valign="top">
<pre lang="cpp">
optional&lt;int&gt; oi = parseInt(str);
std::cout &lt;&lt; oi.value_or(0);
</pre>
</td>
</tr>
</table>


Note, optional is **not just for errors**, and exceptions are still the go-to choice for error handling.  
See also boost::optional, Haskell's Maybe, etc.

std::variant<A,B,C,...>
---

A more perfect union.


<table>
<tr>
<th>
C++14
</th>
<th>
C++17
</th>
</tr>
<tr>
<td  valign="top">
<pre lang="cpp">
struct Stuff
{
   union Data {
      int i;
      double d;
      string s;  // constructor/destructor???
   } data;
   enum Type { INT, DOUBLE, STRING } type;
};
</pre>
</td>
<td  valign="top">
<pre lang="cpp">
struct Stuff
{




   std::variant&lt;int, double, string&gt; data;

};

</pre>
</td>
</tr>
</table>



**Usage**

<table>
<tr>
<th>
C++14
</th>
<th>
C++17
</th>
</tr>
<tr>
<td  valign="top">
<pre lang="cpp">
void handleData(int i);
void handleData(double d);
void handleData(string const &amp; s);

//...

switch (stuff.type)
{
case INT:
   handleData(stuff.data.i);
   break;
case DOUBLE:
   handleData(stuff.data.d);
   break;
case STRING:
   handleData(stuff.data.s);
   break;
}
</pre>
</td>
<td  valign="top">
<pre lang="cpp">
void handleData(int i);
void handleData(double d);
void handleData(string const &amp; s);

//...

std::visit([](auto const &amp; val) { handleData(val); }, stuff.data);

// can also switch(stuff.data.index())

</pre>
</td>
</tr>
</table>





<table>
<tr>
<th>
How the above lambda works
</th>
</tr>
<tr>
<td  valign="top">
<pre lang="cpp">
struct ThatLambda
{
   void operator()(int const &amp; i) { handleData(i); }
   void operator()(double const &amp; d) { handleData(d); }
   void operator()(string const &amp; s) { handleData(s); }
};

ThatLambda thatLambda;
std::visit(thatLambda, stuff.data);

</pre>
</td>
</tr>
</table>


**More Usage**



<table>
<tr>
<th>
C++17
</th>
</tr>
<tr>
<td  valign="top">
<pre lang="cpp">
if (holds_alternative&lt;int&gt;(data))
   int i = get&lt;int&gt;(data);

// throws if not double:
double d = get&lt;double&gt;(data);

</pre>
</td>
</tr>
</table>




<table>
<tr>
<th>
C++17
</th>
</tr>
<tr>
<td  valign="top">
<pre lang="cpp">
std::variant&lt;Foo, Bar&gt; var;  // calls Foo()
// (or doesn't compile if no Foo())

Bar bar = makeBar();
var = bar; // calls ~Foo() and Bar(Bar const &amp;)
// (what if Bar(Bar const &amp; b) throws?)

var = Foo(); // calls ~Bar() and move-ctor Foo(Foo &amp;&amp;)
// (what if Foo(Foo &amp;&amp; b) throws? - even though moves shouldn't throw)

var = someFoo;  // calls Foo::operator=(Foo const &amp;)


std::variant&lt;Foo, std::string&gt; foostr;

foostr = "hello"; // char * isn't Foo or string
// yet foostr holds a std::string
</pre>
</td>
</tr>
</table>


std::any
---

Whereas `std::variant<A,B,C>` can hold an A or B or C,
`std::any` can hold (almost) anything!  

<table>
<tr>
<th>
C++14
</th>
<th>
C++17
</th>
<th>
C++17
</th>
</tr>
<tr>
<td  valign="top">
<pre lang="cpp">
void * v = ...;
if (v != nullptr) {
   // hope and pray it is an int:
   int i = *reinterpret_cast&lt;int*&gt;(v);
}
</pre>
</td>
<td  valign="top">
<pre lang="cpp">
std::any v = ...;
if (v.has_value()) {
   // throws if not int
   int i = any_cast&lt;int&gt;(v);
}
</pre>
</td>
<td  valign="top">
<pre lang="cpp">
std::any v = ...;
if (v.type() == typeid(int)) {
   // definitely an int
   int i = any_cast&lt;int&gt;(v);
}
</pre>
</td>
</tr>
</table>


**Note: std::any is NOT a template. It can hold any types, and change type at runtime.**

<table>
<tr>
<th>
C++14
</th>
<th>
C++17
</th>
</tr>
<tr>
<td  valign="top">
<pre lang="cpp">
// can hold Circles, Squares, Triangles,...
std::vector&lt;Shape *&gt; shapes;
// requires lots of calls to new
</pre>
</td>
<td  valign="top">
<pre lang="cpp">
// can hold Circles, Squares, Triangles, ints, strings,...
std::vector&lt;any&gt; things;
// no calls to new
</pre>
</td>
</tr>
</table>

namespace std::filesystem
---

<table>
<tr>
<th>
C++14 Windows
</th>
<th>
C++17
</th>
</tr>
<tr>
<td  valign="top">
<pre lang="cpp">
#include &lt;windows.h&gt;

void copy_foobar() {
  std::wstring dir = L"\\sandbox";
  std::wstring p = dir + L"\\foobar.txt";
  std::wstring copy = p;
  copy += ".bak";
  CopyFile(p, copy, false);
  
  std::string dir_copy = dir + ".bak";
  SHFILEOPSTRUCT s = { 0 };
  s.hwnd = someHwndFromSomewhere;
  s.wFunc = FO_COPY;
  s.fFlags = FOF_SILENT;
  s.pFrom = dir.c_str();
  s.pTo = dir_copy.c_str();
  SHFileOperation(&amp;s);
}

void display_contents(std::wstring const &amp; p) {
  std::cout &lt;&lt; p &lt;&lt; "\n";

  std::wstring search = p + "\\*";
  WIN32_FIND_DATA ffd;
  HANDLE hFind =
        FindFirstFile(search.c_str(), &amp;ffd);
  if (hFind == INVALID_HANDLE_VALUE)
     return;
  
  do {
    if ( ffd.dwFileAttributes
         &amp; FILE_ATTRIBUTE_DIRECTORY) {
      std::cout &lt;&lt; "  " &lt;&lt; ffd.cFileName &lt;&lt; "\n";
    } else {
      LARGE_INTEGER filesize;
      filesize.LowPart = ffd.nFileSizeLow;
      filesize.HighPart = ffd.nFileSizeHigh;
      std::cout &lt;&lt; "  " &lt;&lt; ffd.cFileName
                &lt;&lt; " [" &lt;&lt; filesize.QuadPart
                &lt;&lt; " bytes]\n";
    }
  } while (FindNextFile(hFind, &amp;ffd) != 0);
}
</pre>
</td>
<td  valign="top" rowspan="3">
<pre lang="cpp">
#include &lt;filesystem&gt;
#include &lt;iostream&gt;
namespace fs = std::filesystem;

void copy_foobar() {
  fs::path dir = "/";
  dir /= "sandbox";
  fs::path p = dir / "foobar.txt";
  fs::path copy = p;
  copy += ".bak";
  fs::copy(p, copy);
  fs::path dir_copy = dir;
  dir_copy += ".bak";
  fs::copy(dir, dir_copy, fs::copy_options::recursive);
}

void display_contents(fs::path const &amp; p) {
  std::cout &lt;&lt; p.filename() &lt;&lt; "\n";

  if (!fs::is_directory(p))
    return;

  for (auto const &amp; e: fs::directory_iterator{p}) {
    if (fs::is_regular_file(e.status())) {
      std::cout &lt;&lt; "  " &lt;&lt; e.path().filename()
                &lt;&lt; " [" &lt;&lt; fs::file_size(e) &lt;&lt; " bytes]\n";
    } else if (fs::is_directory(e.status())) {
      std::cout &lt;&lt; "  " &lt;&lt; e.path().filename() &lt;&lt; "\n";
    }
  }
}
</pre>
</td>
</tr>
<tr>
<th>
C++14 POSIX
</th>
</tr>
<tr>
<td  valign="top">
<pre lang="cpp">
#include &lt;dirent.h&gt;
#include &lt;sys/stat.h&gt;
#include &lt;sys/types.h&gt;

void copy_foobar() {

// [TODO]
// to copy file, use fread / fwrite

// how to copy directory...?
}

void display_contents(std::string const &amp; p) {
  std::cout &lt;&lt; p &lt;&lt; "\n";

  struct dirent *dp;
  DIR *dfd;
  
  if ((dfd = opendir(p.c_str()) == nullptr)
    return;

  while((dp = readdir(dfd)) != nullptr) {
    struct stat st;
    string filename = p + "/" + dp->d_Name;
    if (stat(filename.c_str(), &amp;st) == -1)
      continue;
      
    if ((st.st_mode &amp; S_IFMT) == S_IFDIR)
      std::cout &lt;&lt; "  " &lt;&lt; filename &lt;&lt; "\n";
    } else {
      std::cout &lt;&lt; "  " &lt;&lt; filename
                &lt;&lt; " [" &lt;&lt; st.st_size
                &lt;&lt; " bytes]\n";
    }
  }
}
</pre>
</td>
</tr>

</table>
Parallel STL
---

A bunch of std:: algorithms can now run in parallel, if you request it.

| | | |
|---|---|---|
| adjacent_difference | is_heap_until | replace_copy_if | 
| adjacent_find | is_partitioned | replace_if | 
| all_of | is_sorted | reverse | 
| any_of | is_sorted_until | reverse_copy | 
| copy | lexicographical_compare | rotate | 
| copy_if | max_element | rotate_copy | 
| copy_n | merge | search | 
| count | min_element | search_n | 
| count_if | minmax_element | set_difference | 
| equal | mismatch | set_intersection | 
| fill | move | set_symmetric_difference | 
| fill_n | none_of | set_union | 
| find | nth_element | sort | 
| find_end | partial_sort | stable_partition | 
| find_first_of | partial_sort_copy | stable_sort | 
| find_if | partition | swap_ranges | 
| find_if_not | partition_copy | transform | 
| generate | remove | uninitialized_copy | 
| generate_n | remove_copy | uninitialized_copy_n  | 
| includes | remove_copy_if | uninitialized_fill  | 
| inner_product | remove_if | uninitialized_fill_n | 
| inplace_merge | replace | unique | 
| is_heap | replace_copy | unique_copy | 


**How do you 'request' it?**


<table>
<tr>
<th>
C++14
</th>
<th>
C++17
</th>
</tr>
<tr>
<td  valign="top">
<pre lang="cpp">
std::for_each(first, last,
    [](auto& x){ process(x); }
);
std::copy(first, last, output);
std::sort(first, last);
std::transform(xfirst, xlast, yfirst,
    [=](double xi, double yi){ return a * xi + yi; }
);
</pre>
</td>
<td  valign="top">
<pre lang="cpp">
std::for_each(std::par, first, last,
    [](auto& x){ process(x); }
);
std::copy(std::par, first, last, output);
std::sort(std::par, first, last);
std::transform(std::par_unseq, xfirst, xlast, yfirst,
    [=](double xi, double yi){ return a * xi + yi; }
);</pre>
</td>
</tr>
</table>


Execution Policies
---

| | |
---|---
std::seq | indeterminately sequenced in the **calling thread**
std::par | **multiple threads** - calls are indeterminately sequenced with respect to each other within the same thread
std::par_unseq | **multiple threads and may be vectorized** - calls are unsequenced with respect to each other and possibly interleaved


Technical Specifications (TS)
---

Remember `std::tr1` and `std::tr2`, which had `shared_ptr` etc (lots of Boost, basically), and later became C++11?
Those were _Technical Reports_ (thus the 'tr').  Now we call them _Technical Specifications_.
(The differences are... technical. Basically, they are still _specs_, just not _the_ Standard.
They are specs, not "reports" about something, like a report on C++ performance wrt exceptions, etc (which is [TR18015](www.open-std.org/jtc1/sc22/wg21/docs/TR18015.pdf))

Also, we put them in `std::experimental`. It would be `std::ish` but I wasn't there that day :-(

The committee has a number of TSes on the go.  Although they are not part of C++17, you can use them NOW.

**Concepts TS**
---

_The biggest addition to C++ since sliced bread_.

Available NOW in latest gcc.

In a nutshell,

<table>
<tr>
<th>
C++14
</th>
<th>
C++17
</th>
</tr>
<tr>
<td  valign="top">
<pre lang="cpp">
//
// T must be a Random Access Iterator
// otherwise you will either get the wrong answer,
// or, most likely, terrible compiler errors
//
template &lt;typename T&gt;
auto binary_search(T first, T second)
{
   //...
}
</pre>
</td>
<td  valign="top">
<pre lang="cpp">







template &lt;RandomAccessIterator T&gt;
auto binary_search(T first, T second)
{
   //...
}
</pre>
</td>
</tr>
<tr>
<td  valign="top">
<pre>
error: syntax error '[' unexpected
error: gibberish
error: more compiler gibberish
error: for pages and pages
...
</pre>
</td>
<td  valign="top">
<pre>
error: MyIter does not model RandomAccessIterator
</pre>
</td>
</tr>
</table>

**Modules TS**
---

_Encapsulation at the component level_  
(Precompiled headers on steroids. Don't tell Gaby I said that.)  

Available NOW in Visual Studio and clang.

**Coroutines TS**
---

(a.k.a. Gor-routines.) Similar to `await` et al from C#, python, etc.  But, of course, better.

Available NOW in Visual Studio.


**Ranges TS**
---

_Nothing less than STL 2.0_  
https://github.com/ericniebler/range-v3

**Networking TS**
---

_Boost ASIO._



**Transactional Memory TS**
---

**Parallelism 2 TS**
---

**Concurrency 2 TS**
---

**Library Fundamentals 2 TS**
---

**2D Graphics TS**
---


---

Some sources I used, and/or places for good C++17 info:

https://jfbastien.github.io/what-is-cpp17/#/  
https://skebanga.github.io/structured-bindings/  
http://www.bfilipek.com/2017/01/cpp17features.html  
http://en.cppreference.com  
http://stackoverflow.com  
Bryce Adelstein Lelbach's talk
