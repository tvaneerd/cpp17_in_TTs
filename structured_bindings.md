Decomposition Declarations a.k.a Structured Bindings 
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
   
   
   auto &amp; [ i, s ] = stuff();


   use(s, ++i);
</pre>
</td>
<td valign="top">
<pre lang="cpp">
   pair&lt;int, string&gt; stuff();
   
   auto &amp; __tmp = stuff();
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
C++17
</th>
</tr>
<tr>
<td valign="top">
<pre lang="cpp">
   pair&lt;int, string&gt; stuff();
   
   auto const &amp; [ i, s ] = stuff();

   use(s, ++i);
</pre>
</td>
<td valign="top">
<pre lang="cpp">
   pair&lt;int, string&gt; stuff();
   
   auto &amp;&amp; [ i, s ] = stuff();

   use(s, ++i);
</pre>
</td>
</tr>
</table>


Wait, pair and tuple are not magic(?), can *my* types work with this?





<table>
<tr>
<th>
C++17
</th>
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
</tr>
</table>




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
   Foo stuff();
   namespace std {
      template ... tuple_size ...
      template ... tuple_element ...
   }
   // or get outside class
   template&lt;int N&gt; auto get(Foo /*const?*/ &amp; foo) { /*...*/ }
   //...
   
   auto [ i, s ] = stuff();

   use(s, ++i);
</pre>
</td>
</tr>
</table>



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
   auto [ x, y ] = { 1, 2 }; // ??
   
   // now we're talkin'
   for (auto &amp;&amp; [key, value] : my_map)
   {
      //...
   }
</pre>
</td>
</tr>
</table>
