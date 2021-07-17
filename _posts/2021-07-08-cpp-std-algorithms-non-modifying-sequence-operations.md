---
layout: post
title:  "C++ Std Algorithms - Coding the 16 Non-Modifying Sequence Operations"
category: software
---

As of C++20 there are [16 non-modifying sequence operations](https://en.cppreference.com/w/cpp/algorithm) such as std::all_of() and std::find(). (I didn't count the matching ranges niebloid/functions.) I created forward iterator versions of these [here](https://github.com/matthewdrees/kata/blob/master/mystl/mystl/algorithm.hpp), and benchmarked them against libc++/libstdc++ implementations below.

This was inspired by 2 talks:
1. [Sean Parent's 2013 "C++ Seasoning"](https://www.youtube.com/watch?v=qH6sSOr-yk8) talk at Going Native.
2. [Andrei Alexandrescu's "Writing Fast Code"](https://www.youtube.com/watch?v=vrfYLlR8X8k) talk at 2015 code::dive conference.

Sean says to learn your algorithms. Andrei says to benchmark. I set out to do a bit of both here with my "kata time".

# Things I noticed while coding up these algorithms
* for_each has a return value: the UnaryFunction predicate parameter. This could be a "poor mans" accumulate, although I've never seen it used this way. An aside: for_each is probably dead, right? Just use a range-based for loop introduced in c++11.
* for_each_n seems dangerous... the behavior is undefined if n is bigger than size of container. Unlike for_each, for_each_n does not have return value and UnaryFunction is passed by value instead of reference.
* any_of is the opposite of none_of. Notice the return values for an empty range... any_of: false, none: true.
* find_first_of has the same API as search, but finds the first value in the second range. Don't know how you do this any better than O(n*m). Dangerous to have this in your code?
* On libstdc++, 7 of the 16 algorithms are based on find_if: all_of, any_of, none_of, find, find_if_not, search, search_n. find_if is very cleverly implemented with an unrolled loop that results in many of the "1.6 times faster" improvements in the benchmarks below.
* If I were to roughly group the algorithms by implementation, I'd group find_end, search, and search_n in one group, the rest in another.
* Some algorithms in the standard library are implemented in a different way for each iterator type (e.g. Forward, BiDirectional, Random). I panicked a little bit when some algorithms like [std::find_end](https://en.cppreference.com/w/cpp/algorithm/find_end) document their iterator as "ForwardIt" in the function API, thinking it was only implemented with forward iterators, because the algorithm can be faster if you start from the end and work backwards. Rest assured, it has implementations for BiDirectional and Random iterators.
* I only implemented the algorithms using forward iterators and benchmarked using vectors, so some of the std:: calls are much faster because they take advantage of random access iterators. If/when I continue this exercise in the future I'll implement them with random access iterators because that's what I use in real life 99% of the time (e.g. arrays and vectors).
* [libc++ algorithm code here](https://github.com/llvm-mirror/libcxx/blob/master/include/algorithm).
* [libstdc++ algorithm code here](https://github.com/gcc-mirror/gcc/blob/99dee82307f1e163e150c9c810452979994047ce/libstdc%2B%2B-v3/include/bits/stl_algo.h).

# Benchmarking my implementations compared to std:: implementations
Runtime benchmarks comparing my implementations to the std::* implementations in clang++/libc++ and g++/libstdc++ using google benchmark on quick-bench.com.

Note on compiler settings:
* Clang 12.0, std=c++20, optim = O3, STL = libc++(LLVM)
* GCC 10.3, std=c++20, optim = O3, STL = libstdc++(GNU)

This was meant to be a quick exercise so the benchmarking isn't thorough. I only benchmarked one case for each algorithm, a std::vector<int> with 65,535 elements, and the element halfway through the vector causes the algorithm to finish. For example, the vector has 65,535 elements that are the same value except the 32,767th element is different, so std::all_of returns at that point (with a value of false, but the return value is ignored for the benchmark).

Note that "faster" below means the std:: algorithm ran faster than my version of the algorithm.

I did not compare libc++/libstdc++ implementations directly to each other. Only my version to each of their versions. The table below does NOT imply that libstdc++ is faster than libc++.

| Algorithm  | clang/libc++ | g++/libstdc++      |
|:----------:|:------------:|:------------------:|
| all_of | [same](https://quick-bench.com/q/BTeKg-NkhfKzQrEt1mFClMRwBAw) | [1.6 times faster](https://quick-bench.com/q/PzUOgy6dOj0IPo7GCzvoSbmrcAo) |
| any_of | [same](https://quick-bench.com/q/POwYGLppyA1q1UuCRwuMWPIMsVM) | [1.6 times faster](https://quick-bench.com/q/vKLeGU2mL0cN8OmTaNHEikXd86s) |
| none_of | same as !any_of | same as !any_of |
| for_each | [same](https://quick-bench.com/q/CaM8aMKN1aK-8Q9yuh-OisiKpWY) | [same](https://quick-bench.com/q/KQVEcNEtiSi6SwxuRNsT7NaK0Gk) |
| for_each_n | same as for_each | same as for_each |
| count | [1.1 times slower](https://quick-bench.com/q/2jPn9J-9j9MH1lHIuX2OZMxkWUw) | [same](https://quick-bench.com/q/JxXVfOJwbp1qLgVLIRGcWBPBLuE) |
| count_if | same as count | same as count |
| mismatch | [same](https://quick-bench.com/q/KbmKbXpcKDLjuTiUL-9ejnqd360) | [same](https://quick-bench.com/q/KwBC31ENOwuONVKVZCTw8_ndfig) |
| find | same as find_if | same as find_if |
| find_if | [same](https://quick-bench.com/q/Gw2nmpHJaoJJ4pP0BiVRvqBpAHI) | [1.6 times faster](https://quick-bench.com/q/zdp8JiMVkfZ9GYeo9mI_UokUXno) |
| find_if_not | same as find_if | same as find_if |
| find_end | [4.8 times faster](https://quick-bench.com/q/DMl9SbbWiKNu62SAGCUWtL6VYYQ) | [5.8 times faster](https://quick-bench.com/q/zsc0t-yo2znuevv-MvM-NbfTwdA)
| find_first_of | [same](https://quick-bench.com/q/We-Ezqfws9_Elfx6ACOznZ9SzCg) | [same](https://quick-bench.com/q/Xxr3q80VUZIMKASm-PiH5Id_WPI) |
| adjacent_find | [same](https://quick-bench.com/q/We-Ezqfws9_Elfx6ACOznZ9SzCg) | [same](https://quick-bench.com/q/CHFAhQ9EYH4hn5mtB8tPm7IXOLI) |
| search | [1.1 times slower](https://quick-bench.com/q/SMZnubA1BRooO5MsfecejAKDb_E) | [2.2 times faster](https://quick-bench.com/q/Gxxul3shpxb5Akd2HmtEx7gvUsw) |
| search_n | [1.3 times slower](https://quick-bench.com/q/4MaC-vVLd6apKRl7Hjb5sW5BLkY) | [1.5 times slower](https://quick-bench.com/q/SoRgDxvdfhUZBWYt05lvaFV2osU) |
