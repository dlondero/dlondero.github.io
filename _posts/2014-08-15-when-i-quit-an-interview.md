---
layout: post
title: On When I Quit An Interview
categories:
- php
- job
---

Some months ago, at the beginning of the year, I did quite a lot of interviews while [I was looking](http://dlondero.it/php/symfony2/job/2014/01/15/looking-for-a-new-opportunity.html) for a new job. I received a total of 12 offers (but this is another story) and for half of them I had on average 3-4 technical interviews with different levels of interviewers.
I can hardly remember now what I was asked to solve/code during all these interviews, what I firmly remember is the one I quit before the end. I was having the second technical interview with one of the CTOs of this venture located in Central Europe (no I will not tell you their name) when the interviewer asked me to write a class to do something (which is not essential to the story). So I did something like this:

	class Foo
	{
		protected $bar;

		public function __construct($obj)
		{
			$this->bar = $obj;
		}

		public function doSomething()
		{
			//...
		}

		//...
	}

I also had a test checking the correctness of the code. It was working as expected. Still interviewer was not amused: 

I: "naming is bad"  
D: "well this is code I'll trash at the end of the call, I won't spend time making it nice, I want it to work"  
I: "so you don't write the best code at the first time?"  
D: "...well, no, sorry"  
I: "ah"  
D: "do you know test - code - refactor?"  
I: "but naming is bad"  
D: "you know what? thanks for your time but I'm not looking for that kind of company. cya!"  

Hiring developers: you're doing it wrong!