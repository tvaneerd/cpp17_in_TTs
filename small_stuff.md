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
<td colspan="2" align="center">static assertion failure: sizeof(short) == 2</td>
</tr>
</table>





Inline Variables
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
// foo.h
extern int foo;

// foo.cpp
int foo = 10;
</pre>
</td>
<td  valign="top">

<pre lang="cpp">
// foo.h
inline int foo = 10;
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
// foo.h
struct Foo {
   static int foo;
};

// foo.cpp
int Foo::foo = 10;
</pre>
</td>
<td  valign="top">

<pre lang="cpp">
// foo.h
struct Foo {
   static inline int foo = 10;
};
</pre>
</td>
</tr>
</table>






Guaranteed Copy Elision
---



<table>
<tr>
<th>
C++17
</th>
</tr>
<tr>
<td  valign="top">

<pre lang="cpp">

// header &lt;mutex&gt;
namespace std
{
   template &lt;typename M&gt;
   struct lock_guard
   {
      explicit lock_guard(M &amp; mutex);
      // not copyable, not movable:
      lock_guard(lock_guard const &amp; ) = delete;
      //...
   }
}

// your code
lock_guard&lt;mutex&gt; grab_lock(mutex &amp; mtx)
{
   return lock_guard&lt;mutex&gt;(mtx);
}

mutex mtx;

void foo()
{
   auto guard = grab_lock(mtx);
   /* do stuff holding lock */
}
</pre>
</td>
</tr>
</table>





