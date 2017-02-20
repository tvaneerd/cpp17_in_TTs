std::string_view
---

Standardization of existing practice.  See boost::string_ref, QStringRef, etc.

---

Let's say I write some kind of parser:

`Foo parseFoo(std::string const & input);`

But then I have some users using `char *` - add creating a `string` just to pass to the parser, so I add (or change to) this interface:

`Foo parseFoo(char const * str);`

But this parser becomes really popular.  Some are embedding Foos into the middle of their own formats - so no null at the end:

`Foo parseFoo(char const * str, int length);`

Oh, and we use a custom string class (or QString,...)

`Foo parseFoo(MyString const & str);`

etc!  How do you maintain this interface?


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
Foo parseFoo(std::string const & input);
Foo parseFoo(char const * str);

Foo parseFoo(char const * str, int length);




Foo parseFoo(MyString const & str);
</pre>
</td>
<td  valign="top">
<pre lang="cpp">
Foo parseFoo(std::string_view input);

// I would say don't offer this interface, but:
Foo parseFoo(char const * str, int length)
{
   return parseFoo(string_view(str,length));
}

class MyString {
   //...
   operator string_view() const
   {
      return string_view(this->data, this->length);
   }
};
</pre>
</td>
</tr>
</table>

**Example 2**

Think of something like an XML parser, that is constantly returning `string` objects for the XML entities that it finds.
Each of those strings is a potential allocation.
So instead, return `string_view`.

**Caveats**

`string_view` does NOT own the string memory.  It points to memory owned elsewhere, similar to how a reference or pointer or iterator works.
It has _reference semantics_.
