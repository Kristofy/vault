---
title: Macro Overload
created: 2024-07-06
draft: true
publish: false
tags:
---
# Macro Overlad


```cpp
#define MACRO_OVERLOAD(_1, _2, _3, _4, NAME, ...) (NAME)
#define ASSERT_SAME_DEFAULT(T, F, H, n) do { if (!is_same<(T), (F), (H)>(n)) std::cerr << "Failed Test, " #F " and " #H " are not the same\n"; } while(0)
#define ASSERT_SAME_N(T, F, H) do { if (!is_same<(T), (F), (H)>()) std::cerr << "Failed Test, " #F " and " #H " are not the same\n"; } while(0)
#define ASSERT_SAME(...) MACRO_OVERLOAD(__VA_ARGS__, ASSERT_SAME_N, ASSERT_SAME_DEFAULT)(__VA_ARGS__)
```

> [!warning]+ Not with macro parameters
> The macro overlaoding works with this variadic macro trick but not if the parameters are macros themselfes. If i recall correctly the macro resulution order is unspecified

