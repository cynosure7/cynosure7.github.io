---
date: 2014-07-29 20:29:00+00:00
layout: post
title: How does Arrays.sort work
category: Tech
tags: java
---

http://java.dzone.com/articles/algorithm-week-quicksort-three
http://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#sort-int:A-

public static void sort(int[] a)
Sorts the specified array into ascending numerical order.
Implementation note: The sorting algorithm is a Dual-Pivot Quicksort by Vladimir Yaroslavskiy, Jon Bentley, and Joshua Bloch. This algorithm offers O(n log(n)) performance on many data sets that cause other quicksorts to degrade to quadratic performance, and is typically faster than traditional (one-pivot) Quicksort implementations.

Parameters:
a - the array to be sorted