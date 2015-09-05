---
layout: post
title:  "Find fun in algorithm - array [1]"
author:	"Leon Guo"
date:   2015-09-04 23:05:00
categories: Algorithm
tags: [algorithm, java]
---
# Find fun in algorithm (In Java)

## Preface

Algorithm always be boring and hard to be understood, I didn't learn it very well in college. After graduated, I spend much of my time on programming language, such as C, Java, Javasript etc. But after worked for several years, I noticed that algorithm is one of the most important thing for a developer. In numerous situations, the logic or performance issues always caused by unsuitable algorithm. But we haven't to research how to write high performance and easily understood code from zero, the scholars have sum up most of them. So we can stand on their shoulders. The most important thing is to learn and digest their knowledge.
	
Since most of algorithm books use C or C++ to describe, maybe it's a little hard for pure Java developer, so I want to write a series of articles to tell about algorithm in Java. I believe it's a good chance for me to review it and maybe also could help others who is interested in it.
I will start from easy to hard and from basic to advanced.
	
So get ready, let's go!

## Array

Array is the most useful and basic data structure. In Java, array is also an object, but as you know, it doesn't such as other 'common' objects.
	
Array has an attribute called `length` which means how many element it has, once it be declared, the length can't be changed. And all the elements in it have a unique index.

In JDK, `java.util.ArrayList` use an array to store its data. We can find ArrayList in our code everywhere, seems like it's the only way to store a set of data, but did we use it in right way? let's look into it to find how it play with array.

### ArrayList
We always declare an ArryList like this: `new ArrayList()`, so after we executed it, it only made the elements point to an empty array, seems like do nothing useful:
 {% highlight Java %}
	private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};
    
	/**
     * Constructs an empty list with an initial capacity of ten.
     */
    public ArrayList() {
        this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
    }
 {% endhighlight %}
 
#### Add

So what will it do when we add element?
{% highlight Java %}
    public boolean add(E e) {
        ensureCapacityInternal(size + 1);  // Increments modCount!!
        elementData[size++] = e;
        return true;
    }
{% endhighlight %}
The new element will be always added to the last position. But before that, it need to allocate space first.
{% highlight Java %}
	private static final int DEFAULT_CAPACITY = 10;
    
	private void ensureCapacityInternal(int minCapacity) {
        if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
            minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
        }

        ensureExplicitCapacity(minCapacity);
    }
	
	private void ensureExplicitCapacity(int minCapacity) {
        modCount++;

        // overflow-conscious code
        if (minCapacity - elementData.length > 0)
            grow(minCapacity);
    }
{% endhighlight %}
Since our elementData equals to empty array, so it will use 10 as the default size of the elements size. But the most important function is `grow`, it will be invoked when current array is full(when you insert 11th element), let's look into it:
{% highlight Java %}
    private void grow(int minCapacity) {
        // overflow-conscious code
        int oldCapacity = elementData.length;
        int newCapacity = oldCapacity + (oldCapacity >> 1);
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        // minCapacity is usually close to size, so this is a win:
        elementData = Arrays.copyOf(elementData, newCapacity);
    }
	
	/**
     * The maximum size of array to allocate.
     * Some VMs reserve some header words in an array.
     * Attempts to allocate larger arrays may result in
     * OutOfMemoryError: Requested array size exceeds VM limit
     */
    private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;
	
	private static int hugeCapacity(int minCapacity) {
        if (minCapacity < 0) // overflow
            throw new OutOfMemoryError();
        return (minCapacity > MAX_ARRAY_SIZE) ?
            Integer.MAX_VALUE :
            MAX_ARRAY_SIZE;
    }
{% endhighlight %}
We can find that:

+	The new capacity equals (old capacity) * 1.5, so if current size is 10, the new size should be 15.
+	The maximum size is 2n31 - 1
+	Will use an new array for storage, copy the values from old array to the new one(start from index 0), so the old array is useless and ready for GC.

So let image a situation, we declare an ArrayList as default size 10, but we have 10000 elements to store and we add them one by one, then it will copy the array time and time again, which is low performance and will trigger GC oftentimes. So the right way is if we know or could estimate the size of data, we should use `new ArrayList(int initialCapacity)` to initialize the ArrayList, it will use the capacity you appointed.

#### Insert
When you add element to ArrayList you can appoint an index which is the position to insert.
{% highlight Java %}
    /**
     * Inserts the specified element at the specified position in this
     * list. Shifts the element currently at that position (if any) and
     * any subsequent elements to the right (adds one to their indices).
     *
     * @param index index at which the specified element is to be inserted
     * @param element element to be inserted
     * @throws IndexOutOfBoundsException {@inheritDoc}
     */
    public void add(int index, E element) {
        rangeCheckForAdd(index);

        ensureCapacityInternal(size + 1);  // Increments modCount!!
        System.arraycopy(elementData, index, elementData, index + 1,
                         size - index);
        elementData[index] = element;
        size++;
    }
{% endhighlight %}

+	The index must less then size -1 and more then -1
+	The logic of check capacity same as add
+	Will copy all the elements after the index to next position and then add the element to the position for insert

If current array has 1000 elements, and we want to add new element to index 0, then we need copy all the 1000 elements, it's bad performance. So If you always need to insert element, you should think about avoid using ArrayList.

#### Remove
{% highlight Java %}
    /*
     * Private remove method that skips bounds checking and does not
     * return the value removed.
     */
    private void fastRemove(int index) {
        modCount++;
        int numMoved = size - index - 1;
        if (numMoved > 0)
            System.arraycopy(elementData, index+1, elementData, index,
                             numMoved);
        elementData[--size] = null; // clear to let GC do its work
    }
{% endhighlight %}

+	After find out the element for deleting, copy all the element after it forward one step, so it will be replaced, and then set the last element to null

So same as insert, it will copy all the elements after it. So ArryList is also not suitable for high frequency removing.

#### Find and iterator
Because it's an array, so if you know the index, you will get the element very fast. The sequence of the data will same as the sequence you added.

#### Summary
So If you have a set of data, and you don't want to change their sequence and only want to iterator it or get element from it by index, ArrayList will be a very good choice, and you'd better initialize it with proper capacity. But it not suitable for inserting, removing.

As you know String is made up of char array, so I will introduce an interesting char array algorithm called `KMP string matching method` in my next article, please look forward to!


