<table>
<tr>
<th>
How do you write `sum()` ?
</th>
</tr>
<tr>
<td  valign="top">
<pre lang="cpp">
{
   auto x = sum(5, 8);
   auto y = sum(a, b, 17, 3.14, etc);
}
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
{
   auto sum() { return 0; }
   
   template <typename T>
   auto sum(T&& t) { return t; }
   
   template <typename T, typename... Rest>
   auto sum(T&& t, Rest&&... r) {
      return t + sum(std::forward<Rest>(r)...);
   }
}
</pre>
</td>
<td  valign="top">
<pre lang="cpp">
{





   template <typename... Args>
   auto sum(Args&&... args) {
      return (args + ... + 0);
   }
}
</pre>
</td>
</tr>
</table>
