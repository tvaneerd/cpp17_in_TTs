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
  int const &amp; refInt() const
  { return myInt; }
  string const &amp; refString() const
  { return myString; }
};

namespace std
{
   template&lt;&gt; class tuple_size&lt;Foo&gt;
       : public integral_constant&lt;int, 2&gt;
   { };
   template&lt;int N> class tuple_element&lt;N, Foo&gt;
   {
   public:
      using type =
      conditional_t&lt;N==0,int const &amp;,string const &amp;>;
   };
}

template&lt;int N&gt; std::tuple_element_t&lt;N,Foo&gt;
get(Foo const &amp;);

// here's some specializations (the real stuff)
template&lt;&gt; std::tuple_element_t&lt;0,Foo&gt;
get&lt;0&gt;(Foo const &amp; foo)
{
  return foo.refInt();
}
template&lt;&gt; std::tuple_element_t&lt;1,Foo&gt;
get&lt;1&gt;(Foo const &amp; foo)
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
  int const &amp; refInt() const
  { return myInt; }
  string const &amp; refString() const
  { return myString; }
};

namespace std
{
   template&lt;&gt; class tuple_size&lt;Foo&gt;
       : public integral_constant&lt;int, 2&gt;
   { };
   template&lt;int N> class tuple_element&lt;N, Foo&gt;
   {
   public:
      using type =
      conditional_t&lt;N==0,int const &amp;,string const &amp;>;
   };
}


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
