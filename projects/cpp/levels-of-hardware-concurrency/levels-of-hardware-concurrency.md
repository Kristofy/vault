---
title: Levels Of Hardware Concurrency
created: 2024-07-08
draft: false
publish: true
tags:
---
# Levels Of Hardware Concurrency

## Into

Lets look at some 

**Quiz**: How fast is this program?

```cpp
int main() {
	int v[1'000'000];
	for(int i = 0; i < 1'000'000; i++) {
		for(int j = 0; j < i; j++) {
			v[i] = (v[i] + j) + 1'000'0007;
		}
	}

	return 0;
}
```

```txt
 1.1 ms ±   0.3 ms
```

```cpp
int main() {
	int v[1'000'000];
	for(int k = 0; k < 20; k++) {
		for(int i = 0; i <  1'000'000; i++) {
			for(int j = 0; j < i; j++) {
				v[i] = (v[i] + j) + 1'000'0007;
			}
		}
	}

	return 0;
}
```

```txt
 1.1 ms ±   0.4 ms
```

The program isn't made to be benchmark-ed, the optimizer will do everything it can to reduce the runtime overhead. Since this program has no sideeffects or other observable behavior the compiler optimizes it out.

Thus we sucessfully becnmarked the execution of an empty program.
You may run 
```bash
$ objdump -d file.cpp
```
To check the assembly

## Instruction level parallelism

### [Superscalar execution](https://en.wikipedia.org/wiki/Superscalar_processor) 
- Multiple ALU, FPU and SIMD units in a single core of a CPU
- With multiple execution units a processor can execute multiple instructions in a single cycle
![[execution_engine.png]]

### Benchmark some basic operations

The bencmark is a microbechmark with Google's bechmarking library

### Hyperthreading


**Quiz**: Order the following operations based on speed!

add, sub, bit and, bit or, multiply, div, mod

| Benchmark  | Time    | CPU     | Iterations |
| ---------- | ------- | ------- | ---------- |
| add_bench  | 65.1 ns | 65.1 ns | 10086556   |
| sub_bench  | 41.5 ns | 41.5 ns | 16866030   |
| band_bench | 40.7 ns | 40.7 ns | 17262059   |
| bor_bench  | 40.5 ns | 40.5 ns | 17214392   |
| mul_bench  | 36.8 ns | 36.7 ns | 20222192   |
| div_bench  | 184 ns  | 184 ns  | 3951897    |

| Benchmark          | Time    | CPU     | Iterations |
| ------------------ | ------- | ------- | ---------- |
| mod_bench          | 177 ns  | 177 ns  | 3964187    |
| mod_constant_bench | 98.9 ns | 98.9 ns | 7064763    |







| Benchmark    | Time    | CPU     | Iterations | Per operation |
| ------------ | ------- | ------- | ---------- | ------------- |
| add_bench    | 64.4 ns | 64.4 ns | 10916068   | 64.4 ns       |
| addx2_bench  | 67.2 ns | 67.1 ns | 10692708   | 33.6 ns       |
| addx4_bench  | 99.7 ns | 99.6 ns | 7522478    | 24.9 ns       |
| addx8_bench  | 183 ns  | 183 ns  | 3837707    | 22.9 ns       |
| addx16_bench | 369 ns  | 368 ns  | 1915196    | 23.1 ns       |
|              |         |         |            |               |
|              |         |         |            |               |

We can see that the performance does not get better after 4 additions

**Quiz** Given 4 integers adds then 4 float adds what is the expected runtime of the two together?

| Benchmark          | Time    | CPU     | Iterations | Per operation |
| ------------------ | ------- | ------- | ---------- | ------------- |
| addx4_bench        | 92.9 ns | 92.9 ns | 7534969    | 23.2 ns       |
| addfx4_bench       | 171 ns  | 171 ns  | 4083067    | 42.8 ns       |
| bin_mixed_v0_bench | 216 ns  | 216 ns  | 3468772    | 27 ns         |

## [Instruction pipelining](https://en.wikipedia.org/wiki/Instruction_pipelining)

What if there is some data dependency between iteratinons?

```cpp
std::vector<int> fib(100);
fib[1] = 1;
for(int i = 2; i < v.size(); i++) {
	fib[i] = fib[i-1] + fib[i-2];
}
```

It is hard to benchmark, but if the reference is enougth we can minimize the impact of the pipelinging operation

### SIMD

There are operations that say, please add together 8 floats, or 32 chars
These are called *vector operations*. The compiller automatically vectorises some scenarios where it can.

add
```cpp
for(int i = 0; i < 1'024; i++)
	targets[i]+targets[i];
```
~0.280 ms / 2^20 adds

add simd first try

```cpp
for(int i = 0; i < 1'024 / 8; i++)
  for(int j = 0; j < 8; j++)
	  targets[i*8 + j] + targets[i*8 + j];
```
~0.410 ms / 2^20 adds


```cpp
for(int i = 0; i < 1'024 / 8; i+=8)
  for(int j = 0; j < 8; j++)
  	 targets[i + j] + targets[i + j];
```
 0.051 ms / 2^20 adds
 That is 5.49019607843137 times faster than the original

```cpp
for (int i = 0; i < 1'024 / 8; i += 8) {
	__m256i loaded = _mm256_loadu_si256((__m256i_u *)(data + i));
	__m256i ans = _mm256_add_epi32(loaded, loaded);
}
```
0.004ms / 2^20 adds
Additional 12 times faster

### [Out of order execution](https://en.wikipedia.org/wiki/Out-of-order_execution)
The compiler is working as if ....

## Branch prediction

It is adaptive, ususally it is the best to leave it to the cpu
- There is the [likely and unlikely attrubutes](https://en.cppreference.com/w/cpp/language/attributes/likely) for c++20 / the [`__builtin_expect` for GCC](https://www.ibm.com/docs/en/zos/2.2.0?topic=performance-builtin-expect) for most c++ standards

Always hit
```cpp
auto rng = gen::random_range({
	.n = 1'000'000,
	.min = 0,
	.max = 1'000'000'000}
);

int i = 0;
for (int z = 0; z < 10'000; z++) {
  if (rng[i] < 1'000'000'000 - 1) {
	i += 1;
  } else {
	i += 2;
  }
}

```
~ 0.003 ms
Never hit
```cpp
auto rng = gen::random_range({
	.n = 1'000'000,
	.min = 0,
	.max = 1'000'000'000}
);

int i = 0;
for (int z = 0; z < 10'000; z++) {
  if (rng[i] * 2* < 1'000'000'000) {
	i += 1;
  } else {
	i += 2;
  }
}
```
~0.026 ms

**Quiz** Is the following equivalent implementation faster, why, why not?
```cpp
if (rng[i] * 2* < 1'000'000'000) {
	i += 1;
} else {
	i += 2;
}

i += 1 + (rng[i] * 2 > 1'000'000'000);

```
The compiller is way to smart
But this algorithm is the worst case for pipelining, because there is a strong data dependency

The worst case scenario for the branch predictor is often bad for pipelinging, an algorithm that is hard to optimize well for the branch predictor is *binary search*, we will take a look at it later

## Caches

Memory modell

![[cache_hierarchy.png]]

Cache line
![[cache_timings.png]]

## Array access
![[int_vs_long.png]]

![[enumerate_1d_with_random.png]]

![[enumerate_1d_wo_random.png]]

![[enumerate_2d.png]]
![[enumerate_2d_small.png]]
Prefetching
`builtin_prefetch`


## Puting it all together

Binary search

