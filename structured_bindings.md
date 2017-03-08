Structured Bindings 
-------------------

<table>
<tr>
<th colspan="2">
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
   ~Foo() { std::cout &lt;&lt; str; }
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
   ~Foo() { std::cout &lt;&lt; str; }
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
<tr><td colspan="2" align="center">hello structured bindings</td></tr>
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
