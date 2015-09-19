---
layout: post
title:  "Find fun in algorithm - KMP algorithm"
author:	"Leon Guo"
date:   2015-09-19 21:16:00
categories: Algorithm
tags: [algorithm, java, KMP]
---
# KMP algorithm

## String search algorithm

When you have a string "abacaabacabacabaabb", we call it target string(T). Then there is another string "abacab" named matching string(M). How to figure out the start index in T which match M? If we look into it, we could find that if start from the index 5 of T, it is "abacab" which equals matching string.

How to implement it? We can begin from the index 0 and compare the chars of them one by one, if the char doesn't match, then index++. The performance can't be good because in the worst situation, we should compare (T.length*M.length) times.

The performance must could be better, maybe we could move more than 1 step when comparing chars? So that is what The KMP algorithm (Knuth–Morris–Pratt string searching algorithm) is. 

## Partial match table

The core of KMP algorithm is "Partial match" table. The observation of this table is "pre-search" the string M, that is initial the step, which is if doesn't match in current position, how many extra steps should move forward.

For example, the partial match table of "abacab" is:

<table>
	<tbody>
		<tr><td><em>a</em></td><td><em>b</em></td><td><em>a</em></td><td><em>c</em></td><td><em>a</em></td><td><em>b</em></td></tr>
		<tr><td>0</td><td>0</td><td>1</td><td>0</td><td>1</td><td>2</td></tr>
	</tbody>
</table>

The most difficult of this algorithm is getting this table. Let me show the Jave code first:

{% highlight Java %}

    /**
     * Get the next mapping index
     * @param next The "Partial match" table for creating
     * @param array The matching string
     * @param index current computing position
     */
    public static void getNext(int[] next, char[] array, int index) {
        if (index < 2) {
            return;
        }
        int n = 0;
        for (int i = 0; i < index; i++) {
            if (new String(Arrays.copyOfRange(array, 0, i + 1)).equals(new String(Arrays.copyOfRange(array, index - i, index + 1)))) {
                n = i + 1;
            }
        }
        next[index] = n;
        getNext(next, array, --index);
    }
	
{% endhighlight %}

Obviously, it's a recursion function. The condition of get `n` is `if (new String(Arrays.copyOfRange(array, 0, i + 1)).equals(new String(Arrays.copyOfRange(array, index - i, index + 1))))`. `n` starts from 0, and the start value of index is the last position of M then decrease 1 after invoke this function once. It compare with the beginning of M and the tail of it, and increase compare length each time. For example, when `n` is 0 and `index` is 5.

+	`'a'`(M.substring(0)) doesn't match `'b'`(M.substring(index)).
+	`'ab'`(M.substring(0, 1)) match `'ab'`(M.substring(index - 1, index)), the result is `i + 1 = 2`.
+	`'aba'`(M.substring(0, 2)) match `'cab'`(M.substring(index - 2, index)).
+	...
+	break after the compared all chars and the largest result will be saved to next[index].
+	index=index-1.
+	go back to step 1, but the index subtracted 1, so compare with `'a'`(M.substring(0)) match `'a'`(M.substring(index)), the result is `1`.
+	`'ab'`(M.substring(0, 1)) doesn't match `'ca'`(M.substring(index - 1, index)).
+	...
+	break the recursion funtion when `index < 2`.

After invoke this function for N(the length of M) times, we finally fill all values of the partial match table.

## Search string

Now we already have partial match table, so we could use it when search M in T.

{% highlight Java %}

    /**
     * Get the start index of marching string
     * @param target
     * @param marching
     * @return return -1 if not march
     */
    public static int getKMPIndex(char[] target, char[] marching) {
        int[] next = new int[marching.length];
        getNext(next, marching, marching.length - 1);
        int p = 0;
        while (target.length - p >= marching.length) {
            int a = p;
            for (int j = 0; j < marching.length; j++) {
                if (target[a] == marching[j]) {
                    if (j == marching.length - 1) {
                        return p;
                    }
                    a++;
                } else {
                    if (j > 0) {
                        p += j - next[j-1];
                    } else {
                        p++;
                    }
                    break;
                }
            }

        }
        return -1;
    }
	
{% endhighlight %}

What's the different? We still compare the chars in T and M one by one. But when we find the char in M doesn't match T, we'll move the index of T forward `1 + next[(current char position in M)]`. That avoid just move 1 step for each time.

## Whole java file

{% highlight Java %}

package example;

import java.util.Arrays;

public class KMPAlgorithm {

    public static void main(String[] args) {
        String matching = "abacab"; // string for march
        String target = "abacaabacabacabaabb"; // target string
        int[] next = new int[matching.length()];
        getNext(next, matching.toCharArray(), matching.length() - 1);
        for (int i : next) {
            System.out.print(i + ",");
        }
        System.out.print("\n");
        System.out.println(getKMPIndex(target.toCharArray(), matching.toCharArray()));
    }

    /**
     * Get the next mapping index
     * @param next
     * @param array
     * @param index
     */
    public static void getNext(int[] next, char[] array, int index) {
        if (index < 2) {
            return;
        }
        int n = 0;
        for (int i = 0; i < index; i++) {
            if (new String(Arrays.copyOfRange(array, 0, i + 1)).equals(new String(Arrays.copyOfRange(array, index - i, index + 1)))) {
                n = i + 1;
            }
        }
        next[index] = n;
        getNext(next, array, --index);
    }

    /**
     * Get the start index of marching string
     * @param target
     * @param marching
     * @return return -1 if not march
     */
    public static int getKMPIndex(char[] target, char[] marching) {
        int[] next = new int[marching.length];
        getNext(next, marching, marching.length - 1);
        int p = 0;
        while (target.length - p >= marching.length) {
            int a = p;
            for (int j = 0; j < marching.length; j++) {
                if (target[a] == marching[j]) {
                    if (j == marching.length - 1) {
                        return p;
                    }
                    a++;
                } else {
                    if (j > 0) {
                        p += j - next[j-1];
                    } else {
                        p++;
                    }
                    break;
                }
            }

        }
        return -1;
    }
}

{% endhighlight %}

