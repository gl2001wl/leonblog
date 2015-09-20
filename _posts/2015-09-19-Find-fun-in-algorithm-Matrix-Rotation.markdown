---
layout: post
title:  "Find fun in algorithm - Matrix Rotation"
author:	"Leon Guo"
date:   2015-09-20 20:48:00
categories: Algorithm
tags: [algorithm, java, matrix]
---
# Matrix Rotation

## Question

>Given a NxN matrix, where each element in the matrix is integer, write a method to rotate the matrix by 90 degrees.

## Answer

We can separate the question to a set of small questions. The matrix could be considered as N/2 layers, which is a circle from innermost to outermost. So the key is swapping the element in each circle.

For example, we have a matrix:

<table>
	<tbody>
		<tr><td>1</td><td>2</td><td>3</td><td>4</td><td>5</td></tr>
		<tr><td>6</td><td>7</td><td>8</td><td>9</td><td>10</td></tr>
		<tr><td>11</td><td>12</td><td>13</td><td>14</td><td>15</td></tr>
		<tr><td>16</td><td>17</td><td>18</td><td>19</td><td>20</td></tr>
		<tr><td>21</td><td>22</td><td>23</td><td>24</td><td>25</td></tr>
	</tbody>
</table>

At first time of computing, in layer one, it's `1,2,3,4,5`, `5,10,15,20,25`, `25,24,23,22,21` and `21,16,11,6,1`(It's edges at the outermost). We start from the first element, so move 1 to 5, 5 to 25, 25 to 21 and 21 to 1. Yes it's a cyclic swap, four times in all. Why four times? Because it's a matrix and it has four corners. After this action:

<table>
	<tbody>
		<tr><td>21</td><td>2</td><td>3</td><td>4</td><td>1</td></tr>
		<tr><td>6</td><td>7</td><td>8</td><td>9</td><td>10</td></tr>
		<tr><td>11</td><td>12</td><td>13</td><td>14</td><td>15</td></tr>
		<tr><td>16</td><td>17</td><td>18</td><td>19</td><td>20</td></tr>
		<tr><td>25</td><td>22</td><td>23</td><td>24</td><td>5</td></tr>
	</tbody>
</table>

Then we move 2 to 4, 4 to 19, 19 to 17 and 17 to 2.

...

After N - 1 times, the matrix is:

<table>
	<tbody>
		<tr><td>21</td><td>16</td><td>11</td><td>6</td><td>1</td></tr>
		<tr><td>22</td><td>7</td><td>8</td><td>9</td><td>2</td></tr>
		<tr><td>23</td><td>12</td><td>13</td><td>14</td><td>3</td></tr>
		<tr><td>24</td><td>17</td><td>18</td><td>19</td><td>4</td></tr>
		<tr><td>25</td><td>22</td><td>15</td><td>10</td><td>5</td></tr>
	</tbody>
</table>

We can find out the outermost layer of the matrix has been completely rotated. Then we need to handle the second layer, which is(except `13`, we needn't take care about it as `13` at the centre point, so there is no third layer):

<table>
	<tbody>
		<tr><td>7</td><td>8</td><td>9</td></tr>
		<tr><td>12</td><td>13</td><td>14</td></tr>
		<tr><td>17</td><td>18</td><td>19</td></tr>
	</tbody>
</table>

...

After process all the N/2 layers, the matrix will be completely rotate:

<table>
	<tbody>
		<tr><td>21</td><td>16</td><td>11</td><td>6</td><td>1</td></tr>
		<tr><td>22</td><td>17</td><td>12</td><td>7</td><td>2</td></tr>
		<tr><td>23</td><td>18</td><td>13</td><td>8</td><td>3</td></tr>
		<tr><td>24</td><td>19</td><td>14</td><td>9</td><td>4</td></tr>
		<tr><td>25</td><td>20</td><td>15</td><td>10</td><td>5</td></tr>
	</tbody>
</table>

## Whole java file

{% highlight Java %}

package example;

public class Matrix {

    /**
     * Rotate a matrix, space complexity is O(1), time complexity is O(2N)
     * @param matrix the matrix to be rotated
     */
    public static void rotate(int[][] matrix) {
        for (int i = 0; i < matrix.length / 2; i++) {
            int[] layer = matrix[i];
            for (int j = i; j < matrix[i].length - i - 1; j++) {
                exchange(matrix, layer[j], i, j, 0);
            }
        }
    }

    /**
     * Exchange the element in matrix
     * @param matrix
     * @param val The value want to replace to other element
     * @param layer The layer of the value
     * @param index The index of the value
     * @param times The times of the recursion function has been invoked. Because of it's a matrix, so after invoked for 3 times, it will return to the beginning position
     */
    private static void exchange(int[][] matrix, int val, int layer, int index, int times) {
        if (times > 4) {
            return;
        }
        int tmp = matrix[index][matrix[index].length - layer - 1];
        exchange(matrix, tmp, index, matrix[index].length - layer - 1, ++times);
        matrix[index][matrix[index].length - layer - 1] = val;
        Matrix.printMatrix(matrix);
    }

    public static void printMatrix(int[][] matrix) {
        for (int i = 0; i < matrix.length; i++) {
            for (int j = 0; j < matrix[i].length; j++) {
                System.out.print(matrix[i][j] + ",");
            }
            System.out.print("\n");
        }
        System.out.print("\n");
    }

    public static void main(String[] args) {
        int[][] matrix = { {1, 2, 3, 4, 5}, {6, 7, 8, 9, 10}, {11, 12, 13, 14, 15}, {16, 17, 18, 19, 20}, {21, 22, 23, 24, 25} };

        Matrix.printMatrix(matrix);
        Matrix.rotate(matrix);
        Matrix.printMatrix(matrix);
    }

}
	
{% endhighlight %}


