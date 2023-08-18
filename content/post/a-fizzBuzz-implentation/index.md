---
title: "A FizzBuzz Implentation"
description: 
date: 2023-08-09T14:42:25+05:30
image: 003.jpg
math: 
license: 
hidden: false
comments: true
categories:
  - Technical
tags:
  - golang
  - Programming
---

I have been thinking of learning golang for sometime now and decided that well an exercise such as fizzbuzz would be a great way to start familiarizing myself with the language.

![golang](https://i.imgur.com/EjBUA2d.png#center)

## What is FizzBuzz?

> Fizz buzz is a group word game for children to teach them about 
> division. Players take turns to count incrementally, replacing any 
> number divisible by three with the word *fizz*, and any number divisible by five with the word *buzz*. — [*Wikipedia*](https://en.wikipedia.org/wiki/Fizz_buzz)

For Example, in a range 1-15, it will go like:

```
1
2
Fizz
4
Buzz
Fizz
7
8
Fizz
Buzz
11
Fizz
13
14
FizzBuzz
```

Conventionally the range being 1-100.



## The Problem

_Write a program that prints the numbers from 1 to 100. But for multiples
 of three print “Fizz” instead of the number and for the multiples of 
five print “Buzz”. For numbers which are multiples of both three and 
five print “FizzBuzz”._



## My Solution

It is easy to build a simple fizzbuzz solution, doing a loop to 100 and hard coding divisibility checks like `if i%3 == 0`, but I want to generalize my code as much as possible. That is, make changing the inputs very easy without affecting the logic of the program.

Here is my solution:

```go
package main

import "fmt"

// Struct to map Number to String
type NumString struct{
  num int
  txt string
}

func main(){
	// Range
	var rf int = 1    // Range First
	var rl int = 100  // Range Last

	// FizzBuzz Relations
	numfb := []NumString{
		{3, "Fizz"},
		{5, "Buzz"},
	}
	fizz_buzz(numfb, rf, rl)
}

func fizz_buzz(numfb []NumString, rf int, rl int){
	var tempstr string = "" 
	for i:= rf; i <= rl; i++{
		for _, element := range numfb{ 
			if i%element.num == 0 {
				tempstr += element.txt
			}
		}
		if tempstr == "" {
			fmt.Println(i) 
		} else {
			fmt.Println(tempstr)
			tempstr = ""
		}
	}
}
```

### Observations

1. Using a struct instead of a map. I should have probably used a map instead, but really wanted to do this with a struct. I will say, it turned out better than I could have hoped for. The more I look at it, the more I think It should have used a map, but I will resist, this is my masterpiece.

2. On line _27_, the for loop uses `_` because and index value is not needed. Interesting design choice. 

3. `else` start the same line as I close the bracket for the if statement. I cannot place the else statement on the next line, that is a syntax error. If I think about it, it does make sense that the related else statement should be nested, but it does feel weird doing that, since i have a habit of placing the else statement on the next line. The 2 minutes I did not understand why this was happening, I felt very dumb.

4. There is no _output_. Wonder what happened to it, you are now gonna have to run it on your own to find out, if the _output_ is correct or not.



## Closing words

It was pretty fun writing this thing in golang. The documentation was full of great examples which made understanding the basics really easy. Probably one of the easiest languages to learn, if you know any other programming language.

I would say it is an above average implementation of fizzbuzz. It may not be the fastest, or the most generalized one, but this much is sure, if you look at it, it surely is an okay fizzbuzz implementation.

I do think there is still improvement that can be to this. I may comeback to this on a later date once I am more comfortable in the language.