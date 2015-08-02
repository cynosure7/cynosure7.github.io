---
date: 2008-12-17
layout: post
title: Quize - a Balanced Index
category: tech
tags: coding
---

##Quize
Give an interger array A[N], P is a balanced index if  
A[0] + A[1] + ... + A[P−1] = A[P+1] + ... + A[N−2] + A[N−1].

N is an integer within the range [0, 100,000];  
Expected worst-case complexity is O(N);
##Solution
I thought it's quite easy:  
just scan the array from A[0] to A[n] and add each vaule in an new array, then do it again in reverse order.

In 10 minutes, I submitted the answer:

```java
class Quize {
    public int findBalancedIndex(int[] A) {
		
		/* arrary stores the vaule A[0], A[0]+A[1], ... , A[0]+A[1]+A[2]+...+A[N-1] */
        int[] begin = new int[A.length];
		/* arrary stores the vaule A[0]+a[1]+...+A[N-1], ... ,A[N-2]+A[N-1], A[N-1] */
        int[] end = new int[A.length];
        
        int sum = 0;
        for (int i = 0; i < A.length; i++) {
            sum += A[i];
            begin [i] = sum;
        }
        
        sum = 0;
        for (int j = A.length-1; j >= 0; j--) {
            sum += A[j];
            end [j] = sum;
        }
		/*case 1: 0 = A[1]+A[2]+...A[N-1]*/
        if (end [1] == 0) {
			return 0;
		}
		/*case 2: A[0]+A[1]+A[2]+...A[N-2] = 0*/
        if (begin [A.length-2] == 0) {
			return A.length-1;
		}
		/*case 3: A[0]+A[1]+A[k-1] = A[k+1]+...+A[N-1] = 0*/
        for (int k = 1; k < A.length - 1; k++) {
            if(begin[k-1] == end[k+1])
            return k;
        }
        return -1;
    }
}
```
It failed in the test, and after a careful review I find the mising points below:

**input validation**

```java
if (A == null || A.length == 0 ) {
	return -1;
} 

if (A.length > 100000) {
	return -1;
}

if (A.length == 1) {
	return 0;
}
```
**wrong primitive type**

Should use long for array ```begin``` and ```end``` to avoid arithmetic overflow.

What I get from this quize is that:  
Although it's a quite easy task, before committing the code I need more reviews and tests.