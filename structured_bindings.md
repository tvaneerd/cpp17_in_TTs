Structured Bindings 
-------------------

<table>
<tr>
<th>
C++11
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
   auto i = get&lt;0&gt;(__tmp);
   auto s = get&lt;1&gt;(__tmp);

   use(s, ++i);
</pre>
</td>
</tr>
</table>

Get reference for the item in the tuple

<table>
<tr>
<th>
C++11
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
   int &amp; i = get&lt;0&gt;(tup);
   string &amp; s = get&lt;1&gt;(tup);
  
   use(s, ++i);
</pre>
</td>
<td  valign="top">
<pre lang="cpp">
   tuple&lt;int, string&gt; stuff();
   
   //syntax error - reference without initialising
   int &amp; i;
   string s;
   std::tie(i,s) = stuff();

   use(s, ++i);
</pre>
</td>
<td valign="top">
<pre lang="cpp">
   tuple&lt;int, string&gt; stuff();
   
   
   auto &amp; [ i, s ] = stuff();


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
   
   
   auto &amp; [ i, s ] = stuff();


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
   
   
   auto const &amp; [ i, s ] = stuff();


   use(s, i);
</pre>
</td>
<td valign="top">
<pre lang="cpp">
   pair&lt;int, string&gt; stuff();
   
   auto const &amp; __tmp = stuff();
   auto &amp; i = get&lt;0&gt;(__tmp); // automatically const
   auto &amp; s = get&lt;1&gt;(__tmp); // automatically const

   use(s, i);
</pre>
</td>
</tr>
</table>


Wait, pair and tuple are not magic(?), can *my* types work with this?

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
