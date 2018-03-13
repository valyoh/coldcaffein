---
layout: post
title: N-th Fibonacci number in near constant time
tags: [N-th Fibonacci number]
---

The title is a little misleading. Actually the time is linear, but you don't need to compute recursively or iteratively all the Fibonacci numbers from 1 to n in order to find the n-th number in the sequence. It would be more correct to name the article: "Finding the n-th Fibonacci number without calculating the whole sequence", but this is to long and doesn't draw the attention as much as the one that I used. Right?

The calculation could be done using the formula below:

Here is the program written in Java:

```java
public class Fibonacci {

    public BigDecimal mN1;
    public BigDecimal mN2;
    public BigDecimal mN3;

    public Fibonacci() {
        mN1 = new BigDecimal(1/Math.sqrt(5));
        mN2 = new BigDecimal((1 + Math.sqrt(5)) / 2);
        mN3 = new BigDecimal((1 - Math.sqrt(5)) / 2);
    }

    public BigInteger nth(int n) {
        return mN1.multiply( 
        	mN2.pow(n).subtract( mN3.pow(n) ) ).toBigInteger();
    }

    public static void main(String[] args) throws Exception {
        Fibonacci fib = new Fibonacci();
        
        for(int i = 1000; i < 10000; i+=1000 ) {
            System.out.print("Fibonacci " + i + " found in ");
            Stopwatch timer = new Stopwatch();
            fib.nth(i);
            System.out.println(timer.elapsedTime() + " seconds");
        }
        
    }
}
```
And here is the output of the program:

```
Fibonacci 1000 found in 0.109 seconds
Fibonacci 2000 found in 0.317 seconds
Fibonacci 3000 found in 0.643 seconds
Fibonacci 4000 found in 1.197 seconds
Fibonacci 5000 found in 1.706 seconds
Fibonacci 6000 found in 2.619 seconds
Fibonacci 7000 found in 3.613 seconds
Fibonacci 8000 found in 4.91 seconds
Fibonacci 9000 found in 5.319 seconds
```

It is linear because in the calculations are involved powers. Usually powers are calculated iteratively. Depending on the implementation of the power function the time could be near constant. Well in the Java case it is near linear. This program uses a result from the linear algebra to calculate the n-tn Fibonacci number directly. In Wikipedia this method is referred as "Computation by rounding". For more information see Wikipedia.

P.S. The calculation could be speed up a little bit if the second term is removed. It can be ignored because for big N it converges towards zero.