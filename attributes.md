
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

On functions:
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

On classes or structs:
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
struct MyError {
  std::string message;
  int code;
};

MyError divide(int a, int b) {
  if (b == 0) {
    return {"Division by zero", -1};
  }

  std::cout << (a / b) << '\n';

  return {};
}

divide(1, 2);
</pre>
</td>
<td  valign="top">
<pre lang="cpp">
struct [[nodiscard]] MyError {
  std::string message;
  int code;
};

MyError divide(int a, int b) {
  if (b == 0) {
    return {"Division by zero", -1};
  }

  std::cout << (a / b) << '\n';

  return {};
}

divide(1, 2);
</pre>
</td>
</tr>
<tr>
<th>Compiler</th>
<th>C++17 Compiler</th>
</tr>
<tr>
<td></td>
<td>warning: ignoring return value of function declared with 'nodiscard' attribute</td>
</tr>
</table>

Advice: use `[[nodiscard]]` **sparingly**. ie only when there really is _no_ reason to ignore the value.

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


