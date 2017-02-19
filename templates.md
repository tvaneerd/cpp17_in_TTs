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
template <typename T, T v>
struct integral_constant {
  static constexpr T value = v;
};
intergral_constant<int, 2048>::value
integral_constant<char, 'a'>::value
</pre>
</td>
<td  valign="top">
<pre lang="cpp">
template <typename T, T v>
struct integral_constant {
static constexpr T value = v;
};
intergral_constant<2048>::value
integral_constant<'a'>::value
</pre>
</td>
</tr>
</table>
