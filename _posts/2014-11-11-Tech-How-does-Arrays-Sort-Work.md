---
date: 2014-11-11
layout: post
title: What's New in Java 8 - Parallel Array Sorting
category: tech
tags: java sort
---
As a major feature release, Java 8 introduces some enhancements, as well as new features. An interesting enchancement in Java 8 is the Parallel Array Sorting. Array sorting is commonly used in programming practice and `Arrays` class provides sorting operations for all the primitive data types and `Comparable` objects. Current sorting implementations in `Arrays.sort` perform the sorting operations sequentially in the calling thread. To take advantage of multicore/multithread machines, this enhancement implement sorting operations with a parallel implementation that utilizes the [Fork/Join framwork](https://docs.oracle.com/javase/tutorial/essential/concurrency/forkjoin.html) introduced in Java 7.

##Test & Result
The following code is a program that sorts a randomized array of doubles using `Arrays.sort()` and `Arrays.parallelSort()`. This program simplely measures the performance difference between this two approaches.
###Test Code
```java
package ParallelSortTest;

import java.util.Arrays;

public class ParallelSortTest {
	private static final int BASE_ARRAY_SIZE = 10000;

	public static double[] generateArray(int size) {
		if (size <= 0 || size > Integer.MAX_VALUE) {
			return null;
		}
		double[] result = new double[size];
		for (int i = 0; i < size; i++) {
			result[i] = Math.random();
		}
		return result;
	}

	public static void main(String[] args) {
		for (int i = 1; i < 10000; i *= 10) {
			int size = BASE_ARRAY_SIZE * i;
			double[] myArray1 = generateArray(size);
			double[] myArray2 = Arrays.copyOf(myArray1, myArray1.length);
			System.out.println("Array Size: " + size);

			long startTime = System.currentTimeMillis();
			Arrays.sort(myArray1);
			long endTime = System.currentTimeMillis();
			System.out.println("Time take in serial: " + (endTime - startTime)
					+ "ms.");

			startTime = System.currentTimeMillis();
			Arrays.parallelSort(myArray2);
			endTime = System.currentTimeMillis();
			System.out.println("Time take in parallel: "
					+ (endTime - startTime) + "ms.");
			System.out.println();
		}
	}
}

```
###Environment
	2.6 GHz Intel Core i5
	java version "1.8.0_25"
###Result
	Array Size: 10000
	Time take in serial: 5ms.
	Time take in parallel: 17ms.

	Array Size: 100000
	Time take in serial: 27ms.
	Time take in parallel: 30ms.

	Array Size: 1000000
	Time take in serial: 180ms.
	Time take in parallel: 61ms.

	Array Size: 10000000
	Time take in serial: 1227ms.
	Time take in parallel: 527ms.

###Analysis
The above result shows parallel sorting on a multicore machine can achieve performance improvements at 1 million  or more elements. While below this threshold it may actually be slower than sequential sorting. As [Success Metrics in JEP 103](http://openjdk.java.net/jeps/103)
says 
>A minimum speedup of at least 1.3 over sequential sort on a two core system, for a suitably sized data set.

this result meets the expectation, and the `suitable size` here may be 1 million. Your mileage may vary, it depends on your environment.

##Now, the code
Now, let's take a look at the code to figure out how this parallel sorting works.

```java
    public static void parallelSort(double[] a) {
        int n = a.length, p, g;
        if (n <= MIN_ARRAY_SORT_GRAN ||
            (p = ForkJoinPool.getCommonPoolParallelism()) == 1)
            DualPivotQuicksort.sort(a, 0, n - 1, null, 0, 0);
        else
            new ArraysParallelSortHelpers.FJDouble.Sorter
                (null, a, new double[n], 0, n, 0,
                 ((g = n / (p << 2)) <= MIN_ARRAY_SORT_GRAN) ?
                 MIN_ARRAY_SORT_GRAN : g).invoke();
    }
```
As we can see, there is a minimum granularity (java.util.Arrays.MIN_ARRAY_SORT_GRAN = 8192 [0x2000]), and if the length of the array is less than the minimum granularity, it is sorted using the `DualPivotQuicksort.sort` directly instead of  the sorting task partition. Typically, using smaller sizes results in memory contention across tasks that makes parallel speedups unlikely.

Another notable judement is `ForkJoinPool.getCommonPoolParallelism()` which returns the targeted parallelism level of the common pool (by default, equal to the number of available processors `Runtime.getRuntime().availableProcessors()`). And if your machine has only 1 worker thread, it will not use parallel task either.

When the array length reaches a minimum granularity and you got more than 1 worker thread, the array is sorted using the parallel sort method. And the ForkJoin common pool is used to execute parallel tasks here.

In later posts, I'll analyze the implementation of `ArraysParallelSortHelpers` and `DualPivotQuicksort`.
##Reference
[JEP 103: Parallel Array Sorting](http://openjdk.java.net/jeps/103)  
[New features in Java 8 Parallel Array sorting](http://www.software-architect.net/articles/new-features-in-java-8/parallel-array-sorting.html)  
[Java 8 – Parallel Array Sorting](http://vaskoz.wordpress.com/2013/07/21/java-8-parallel-array-sorting/)  
[Arrays.sort versus Arrays.parallelSort](http://blog.sanaulla.info/2013/04/08/arrays-sort-versus-arrays-parallelsort/)  
[Parallel Array Operations in Java 8](http://www.drdobbs.com/jvm/parallel-array-operations-in-java-8/240166287)  
[Java 8 added Parallel array sorting and new Date & time API](http://java.dzone.com/articles/java-8-added-parallel-array)  
[Enhancements in Java SE 8](https://docs.oracle.com/javase/8/docs/technotes/guides/lang/enhancements.html#jdk8)