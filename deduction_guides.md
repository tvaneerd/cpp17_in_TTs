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

