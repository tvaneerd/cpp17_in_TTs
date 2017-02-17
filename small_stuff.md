Nested Namespaces
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
   namespace A {
      namespace B {
         namespace C {
            struct Foo { };
            //...
         }
      }
   }
</pre>
</td>
<td  valign="top">
<pre lang="cpp">
   namespace A::B::C {
      struct Foo { };
      //...
   }
</pre>
</td>
</tr>
</table>


Single Param static_assert
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
static_assert(sizeof(short) == 2, "sizeof(short) == 2")
</pre>
</td>
<td  valign="top">
<pre lang="cpp">
static_assert(sizeof(short) == 2)
</pre>
</td>
</tr>
<tr>
<th colspan="2">Output</th>
</tr>
<tr>
<td colspan="2">static assertion failure: sizeof(short) == 2</td>
</tr>
</table>



some new [[attributes]]
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
   [[fallthrough]]
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
</tr>
<tr>
<th>static assertion failure: sizeof(short) == 2</th>
</tr>
</table>


warning: ignoring return value of 'bool f()', declared with attribute nodiscard 
