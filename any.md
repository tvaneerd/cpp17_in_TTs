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

