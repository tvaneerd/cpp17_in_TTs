<table>
<tr>
<th>
C++
</th>
</tr>
<tr>
<td  valign="top">
<pre lang="cpp">
{
   if (Foo * ptr = get_foo())
      use(*ptr);
   more_code();
}
</pre>
</td>
</tr>
</table>

But what do you do when it isn't (convertible to) boolean?

<table>
<tr>
<th>
C++
</th>

</tr>
<tr>
<td  valign="top">
<pre lang="cpp">
{
   {
      QVariant var = getAnswer();
      if (var.isValid())
         use(var);
   }
   more_code();
}
</pre>
</td>
</tr>
</table>

<table>
<tr>
<th>
C++
</th>
<th>
C++17
</th>
</tr>
<tr>
<td  valign="top">
<pre lang="cpp">
{
   {
      QVariant var = getAnswer();
      if (var.isValid())
         use(var);
   }
   more_code();
}
</pre>
</td>
<td  valign="top">
<pre lang="cpp">
{
   
   
   if (QVariant var = getAnswer(); var.isValid())
      use(var);
      
   more_code();
}
</pre>
</td>
</tr>
</table>
