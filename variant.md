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


