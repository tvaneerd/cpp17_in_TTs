Parallel STL
---

A bunch of std:: algorithms can now run in parallel, if you request it.

| | | |
|---|---|---|
| adjacent_difference | is_heap_until | replace_copy_if | 
| adjacent_find | is_partitioned | replace_if | 
| all_of | is_sorted | reverse | 
| any_of | is_sorted_until | reverse_copy | 
| copy | lexicographical_compare | rotate | 
| copy_if | max_element | rotate_copy | 
| copy_n | merge | search | 
| count | min_element | search_n | 
| count_if | minmax_element | set_difference | 
| equal | mismatch | set_intersection | 
| fill | move | set_symmetric_difference | 
| fill_n | none_of | set_union | 
| find | nth_element | sort | 
| find_end | partial_sort | stable_partition | 
| find_first_of | partial_sort_copy | stable_sort | 
| find_if | partition | swap_ranges | 
| find_if_not | partition_copy | transform | 
| generate | remove | uninitialized_copy | 
| generate_n | remove_copy | uninitialized_copy_n  | 
| includes | remove_copy_if | uninitialized_fill  | 
| inner_product | remove_if | uninitialized_fill_n | 
| inplace_merge | replace | unique | 
| is_heap | replace_copy | unique_copy | 


**How do you 'request' it?**


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
std::for_each(first, last,
    [](auto& x){ process(x); }
);
std::copy(first, last, output);
std::sort(first, last);
std::transform(xfirst, xlast, yfirst,
    [=](double xi, double yi){ return a * xi + yi; }
);
</pre>
</td>
<td  valign="top">
<pre lang="cpp">
std::for_each(std::par, first, last,
    [](auto& x){ process(x); }
);
std::copy(std::par, first, last, output);
std::sort(std::par, first, last);
std::transform(std::par_unseq, xfirst, xlast, yfirst,
    [=](double xi, double yi){ return a * xi + yi; }
);</pre>
</td>
</tr>
</table>


Execution Policies
---

| | |
---|---
std::seq | indeterminately sequenced in the **calling thread**
std::par | **multiple threads** - calls are indeterminately sequenced with respect to each other within the same thread
std::par_unseq | **multiple threads and may be vectorized** - calls are unsequenced with respect to each other and possibly interleaved


