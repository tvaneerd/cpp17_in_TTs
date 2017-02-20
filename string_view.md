std::string_view
---

Let's say I write some kind of parser:

`Foo parseFoo(std::string const & input);`

But then I have some users using `char *`, so I add (or change to) this interface:

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


**Caveats**

`string_view` does NOT own the string memory.  It points to memory owned elsewhere, like a reference or a pointer or iterator, etc.
It has _reference semantics_.
