
some new [[attributes]]
---


**[[fallthrough]]**

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
switch (device.status())
{
case sleep:
   device.wake();
   // fall thru
case ready:
   device.run();
   break;
case bad:
   handle_error();
   break;
}
</pre>
</td>
<td  valign="top">
<pre lang="cpp">
switch (device.status())
{
case sleep:
   device.wake();
   [[fallthrough]];
case ready:
   device.run();
   break;
case bad:
   handle_error();
   break;
}
</pre>
</td>
</tr>
<tr>
<th>Compiler</th>
<th>Compiler</th>
</tr>
<tr>
<td>warning: case statement without break</td>
<td></td>
</tr>
</table>

**[[nodiscard]]**

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
struct SomeInts
{
   bool empty();
   void push_back(int);
   //etc
};

void random_fill(SomeInts &amp; container,
      int min, int max, int count)
{
   container.empty(); // empty it first
   for (int num : gen_rand(min, max, count))
      container.push_back(num);
}
</pre>
</td>
<td  valign="top">
<pre lang="cpp">
struct SomeInts
{
   [[nodiscard]] bool empty();
   void push_back(int);
   //etc
};

void random_fill(SomeInts &amp; container,
      int min, int max, int count)
{
   container.empty(); // empty it first
   for (int num : gen_rand(min, max, count))
      container.push_back(num);
}
</pre>
</td>
</tr>
<tr>
<th>Compiler</th>
<th>C++17 Compiler</th>
</tr>
<tr>
<td></td>
<td>warning: ignoring return value of 'bool empty()'</td>
</tr>
</table>

**[[maybe_unused]]**



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
   bool res = step1();
   assert(res);
   step2();
   etc();
</pre>
</td>
<td  valign="top">
<pre lang="cpp">
   [[maybe_unused]] bool res = step1();
   assert(res);
   step2();
   etc();
</pre>
</td>
</tr>
<tr>
<th>Compiler</th>
<th>C++17 Compiler</th>
</tr>
<tr>
<td>warning: unused variable 'res'</td>
<td></td>
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
   [[maybe_unused]] void f()
   {
      /*...*/
   }
   int main()
   {
   }
</pre>
</td>
</tr>
</table>


