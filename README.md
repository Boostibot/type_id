# type_id
minimal code and runtime implementation of unique id per type capable of storing additional info

For type erasure we need to store information about the erased typed in some way. C++11 does give us typeid operator which should fulfill this need but it is lacking in two key aspects:
1) Its impossible to modify the implementation to store extra data (such as sizeof type, alignemnt, or basic type_traits like classififcation)
2) Is not constexpr 

This simple library solves both in just under 150 LOC. And can be further reduced to about 20 if the name extraction is not desired (can be toggled with `#define MINIMAL_TYPE_ID`)

# How this works
In c++ each static variable is guaranteed to be unique. Changing one static variable shouldnt change another. We can use this by generating one (posibbly single byte - minimum for c++) static variable per type and using const pointer to it as our identifier. Since this static constant only gets generated for each template instantiation it is guranteed to **optimally minimal** - only those types that are actually referenced by `get_type_id<T>()` have their ids generated.

Additionally we can use this static constant variable to store information about the type. By default I store the name and size of the type (or just the size represented as single byte with `#define MINIMAL_TYPE_ID`). You are free to modify this and add aditional information. 

Storing pointers to copy/move constructors/assigments and destructors could be helpful while implementing a completely type erased Variant. Although in that case we could use the same technique with constant virtual procedures instead of static variables to achieve this somehwat more natively.
