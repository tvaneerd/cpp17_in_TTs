Technical Specifications (TS)
---

Remember `std::tr1` and `std::tr2`, which had `shared_ptr` etc (lots of Boost, basically), and later became C++11?
Those were _Technical Reports_ (thus the 'tr').  Now we call them _Technical Specifications_.
(The differences are... technical. Basically, they are still _specs_, just not _the_ Standard.
They are specs, not "reports" about something, like a report on C++ performance wrt exceptions, etc (which is [TR18015](www.open-std.org/jtc1/sc22/wg21/docs/TR18015.pdf))

Also, we put them in `std::experimental`. It would be `std::ish` but I wasn't there that day :-(

The committee has a number of TSes on the go.  Although they are not part of C++17, you can use them NOW.

**Concepts TS**
---

_The biggest addition to C++ since sliced bread_.

Available NOW in latest gcc.

In a nutshell,

<table>
<tr>
<th>
C++14
</th>
<th>
Concepts TS
</th>
</tr>
<tr>
<td  valign="top">
<pre lang="cpp">
//
// T must be a Random Access Iterator
// otherwise you will either get the wrong answer,
// or, most likely, terrible compiler errors
//
template &lt;typename T&gt;
auto binarySearch(T first, T second)
{
   //...
}
</pre>
</td>
<td  valign="top">
<pre lang="cpp">







template &lt;RandomAccessIterator T&gt;
auto binarySearch(T first, T second)
{
   //...
}
</pre>
</td>
</tr>
<tr>
<td  valign="top">
<pre>
error: syntax error '[' unexpected
error: gibberish
error: more compiler gibberish
error: for pages and pages
...
</pre>
</td>
<td  valign="top">
<pre>
error: MyIter does not model RandomAccessIterator
</pre>
</td>
</tr>
</table>

**Modules TS**
---

_Encapsulation at the component level_  
(Precompiled headers on steroids. Don't tell Gaby I said that.)  

Available NOW in Visual Studio and clang.

**Coroutines TS**
---

(a.k.a. Gor-routines.) Similar to `await` et al from C#, python, etc.  But, of course, better.

Available NOW in Visual Studio.


**Ranges TS**
---

_Nothing less than STL 2.0_  
https://github.com/ericniebler/range-v3

**Networking TS**
---

_Boost ASIO._



**Transactional Memory TS**
---

**Parallelism 2 TS**
---

**Concurrency 2 TS**
---

**Library Fundamentals 2 TS**
---

**2D Graphics TS**
---

