std::optional<T>
---

So, we have

`Foo parseFoo(std::string_view input);`

What if the parse fails?  And you can't parse out a Foo?

1. return default Foo. ie `Foo()` (if Foo is default constructible)
2. throw an exception
3. `bool parseFoo(std::string_view input, Foo & output);`  // also basically requires `Foo()`
4. `Foo * parseFoo(std::string_view input);`  // allocation!? :-(



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


Note, optional is **not just for errors**.
See also boost::optional, Haskell's Maybe, etc.

