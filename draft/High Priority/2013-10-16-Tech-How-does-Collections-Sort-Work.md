---
date: 2013-10-16 20:15:00+00:00
layout: post
title: How Does Collections.sort Work
category: Tech
tags: Java Algorithm
---

#Background
In Java, we can sort List collections using the java.util.Collections.sort() method. This method facilitate our daily work and it's performance is good. This article will introduce you how this method works in details.

#How does this sort work?
##preparation
We are using eclipse to view source code in debug perspective. by default, eclipse does not import the java source code, so we need to import it manually first.
Step 
1. Windows->Preferences->Java->Installed JREs
2. eidt...


http://trinea.iteye.com/blog/1248517
http://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#sort-java.util.List-
sort
public static <T extends Comparable<? super T>> void sort(List<T> list)
Sorts the specified list into ascending order, according to the natural ordering of its elements. All elements in the list must implement the Comparable interface. Furthermore, all elements in the list must be mutually comparable (that is, e1.compareTo(e2) must not throw a ClassCastException for any elements e1 and e2 in the list).
This sort is guaranteed to be stable: equal elements will not be reordered as a result of the sort.

The specified list must be modifiable, but need not be resizable.

Implementation note: This implementation is a stable, adaptive, iterative mergesort that requires far fewer than n lg(n) comparisons when the input array is partially sorted, while offering the performance of a traditional mergesort when the input array is randomly ordered. If the input array is nearly sorted, the implementation requires approximately n comparisons. Temporary storage requirements vary from a small constant for nearly sorted input arrays to n/2 object references for randomly ordered input arrays.

The implementation takes equal advantage of ascending and descending order in its input array, and can take advantage of ascending and descending order in different parts of the same input array. It is well-suited to merging two or more sorted arrays: simply concatenate the arrays and sort the resulting array.

The implementation was adapted from Tim Peters's list sort for Python ( TimSort). It uses techiques from Peter McIlroy's "Optimistic Sorting and Information Theoretic Complexity", in Proceedings of the Fourth Annual ACM-SIAM Symposium on Discrete Algorithms, pp 467-474, January 1993.

This implementation dumps the specified list into an array, sorts the array, and iterates over the list resetting each element from the corresponding position in the array. This avoids the n2 log(n) performance that would result from attempting to sort a linked list in place.

Parameters:
list - the list to be sorted.
Throws:
ClassCastException - if the list contains elements that are not mutually comparable (for example, strings and integers).
UnsupportedOperationException - if the specified list's list-iterator does not support the set operation.
IllegalArgumentException - (optional) if the implementation detects that the natural ordering of the list elements is found to violate the Comparable contract