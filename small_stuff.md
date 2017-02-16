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
