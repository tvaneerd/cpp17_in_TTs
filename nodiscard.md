\[[nodiscard]]
===============

Some functions have result values that must be stored or used, or else
risk incorrect behavior (sometimes silently!):

<pre lang="cpp">
template <typename T>
class MyOwningPointer {
    T* data = nullptr;

public:
    T* release() { return std::exchange(data, nullptr); }
};

void steal_ownership(MyOwningPointer& ptr) {
    /* delete */ ptr.release(); // Silent memory leak!
}
</pre>

C++20 provides the `[[nodiscard]]` attribute to encourage compilers to
emit a warning when the result of a function is potentially discarded:

Code
----

<pre lang="cpp">
template <typename T>
class MyOwningPointer {
    T* data = nullptr;

public:
    [[nodiscard]]
    T* release() { return std::exchange(data, nullptr); }
};

void steal_ownership(MyOwningPointer<int>& ptr) {
    /* delete */ ptr.release(); // Warning emitted!
}
</pre>

Warning
-------

### GCC

    warning: ignoring return value of ‘MyOwningPointer<int>::release()’, declared with attribute nodiscard [-Wunused-result]
         ptr.release();
                     ^

### Clang

    warning: ignoring return value of function declared with 'nodiscard' attribute [-Wunused-result]
        ptr.release();
        ^~~~~~~~~~~~~
