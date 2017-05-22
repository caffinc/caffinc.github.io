---
title: "The Requirement behind the requirement"
author: admin
layout: post
permalink: /2016/11/requirement-behind-the-requirement/
categories:
  - Life
  - Work
tags:
  - life
  - work
---

# The Requirement behind the requirement

*Or why your manager seems so anal-retentive*

For as far as I can remember, I have been very, _very_, **_very_** careful about presentation when it comes to things I do on a computer. Strangely this affinity towards perfection does not always extend beyond computer screens.

![Logo doing what it does best](http://sydlexia.com/imagesandstuff/logo/square.png)

The oldest memory I have of this is when I was first taught "programming" in `Logo`, a brilliant tool to get kids interested in coding, back in the second grade. Watching that turtle draw a staircase as it moved on my screen to my almost mechanical "FD 10 LT 90 FD 10 RT 90", and my classmate Alice remarking in amazement "Look at him typing his code. It's so methodical and perfect." almost made me feel like a rock star and it was magical. All around me were people struggling to find the right keys on the keyboard and here I was typing away like a pro. From that moment on I never wanted to be anything short of the guy who could do amazing things with a computer.

My schooling in computer programming consisted of hundreds of programs that followed the following template:

+ Show a menu
+ Get user input
+ Perform something based on the input, OR
+ Exit if the user chooses to do so

For example, we would be asked to write a simple "bank account" program that allowed a user to perform simple deposit and withdrawal transactions. My friends would write programs that looked something like this when you ran it:

    1 deposit
    2 withdraw
    3 exit1
    enter amount100
    1 deposit
    2 withdraw
    3 exit2
    enter amount150
    sorry amount too high
    1 deposit
    2 withdraw
    3 exit2
    enter amount150
    1 deposit
    2 withdraw
    3 exit3

While this _worked_, I would be appalled at their lack of aesthetics. My program would look like this:

    Welcome to The Royal Bank

    Press any key to continue...

    ************clear screen************

    ==================
    = The Royal Bank =
    ==================

	Please choose an option:

	1. Deposit
	2. Withdraw
	3. View balance
	4. Exit
	
	Your choice: 1
	
	Enter the amount you wish to deposit: 100

	Thank you for depositing 100. Your current balance is 100.

	Press any key to continue...

    ************clear screen************

    ==================
    = The Royal Bank =
    ==================

	Please choose an option:

	1. Deposit
	2. Withdraw
	3. View balance
	4. Exit
	
	Your choice: 2
	
	Enter the amount you wish to withdraw: 150

	Insufficient funds. You may withdraw up to 100.

	Press any key to continue...

I worried about punctuation, about proper messages, about making sure that the output, while basic and clean, was still pleasing and professional. I never liked code with multiple exclamations, sloppy text, careless testing and lack of attention to user experience. This was the seventh grade and we were writing code in C++.

![Turbo C++ - The choice of champions](https://winworldpc.com/res/img/screenshots/3x-64181992530a605dfd1d88607613ccda-Turbo%20CPP%203.0%20-%20Debug.png)

I remember when we switched to Java in the ninth grade (We essentially wrote the same simple command-line programs, but this time in Java) and there was no equivalent to the `clrscr()`, `getch()` and `gotoxy()` methods from the `Turbo C++` we used to use and it annoyed me that I couldn't clear the screen and display a fresh menu to the user each time. Madness.

When we were first taught the basics of code formatting, I suddenly realized that all my code was so _sloppy_. I immediately made sure that my code had proper spacing and braces were aligned correctly. We never used a modern IDE, though there were plenty available. The school, and the teachers, had bigger issues to address. When I saw that the teachers didn't want to turn us into amazing coders, but were happy with just whatever was on the syllabus, I started learning things on my own. I used to write small text based games, Caesar ciphers, a text encryption tool I called "Scrypt" which produced randomized output based on a key, a LAN based chatting application called "Heartless Chatter" (because why not?), and a whole bunch of other things which I felt I'd invented all by myself only to realize that the world outside had thousands of versions of the stuff I was making.

However, this desire for creating things was always motivated by one goal - Make something that I could show off, something that looked like it was made by a professional Software Company (Back then the only companies I'd heard of were the big names - Microsoft, Adobe, IBM, etc. I wanted to make software that looked like it was made by several people and done _just right_.), something that that was _Classy_. I wanted people to use the software that I'd written. I wanted it to be beautiful, indispensable, and I wanted it to solve problems for people. Eventually, I hoped, I'd make something that would change the world!

So now that I've set the stage, let me tell you what motivated me to write this piece.

I work with a lot of developers with varied levels of expertise. Expertise doesn't always matter to me, but there is a different metric that I judge people by. That metric is their ability to see the Requirement behind the requirement. What does that mean? It is the ability of a person to judge _why_ a requirement exists, and handle _that_ requirement, and not just the obvious literal requirement.

Let's take an overly simplified example. Suppose your manager tells you "I need a method which can add numbers". What is your solution? The naive coder goes back with this:

	int sum(int a, int b) {
		return a + b;
	}

While this is correct, there are several assumptions being made here, some of which are:

1. You are handling only `int` values here.
2. You aren't checking for overflow.
3. You weren't asked to add **two** numbers, you were asked for a method that adds numbers.

Suppose you end up using this method in ten places, and your manager gets back to you and says "That's not right, your method is only handling integers!", is the right response to add another method which handles `doubles` and be done with it? Nope. Your manager will get back to you with "What about overflow? What about adding more than two numbers?" More often than not, your manager doesn't have all the questions. When he gives you a requirement, chances are that even he or she doesn't know what the possibilities of his requirements are. It is your job to find them, handle them, test them, and justify them.

When I work with someone, I judge them based on this ability while ranking them against myself. Due to human nature, and also due to me being somewhat of an ass, my scale is as follows:

1. Completely Useless
2. Almost there
3. At par
4. Wasteful

### 1. Completely Useless
The people who are completely useless are the ones who fail to pick up on the hidden requirements even after repeatedly being put in scenarios to learn from. They are the ones who copy+paste code blindly and do not give a flying f what happens with the company or the code. They are the people who don't take ownership of their code, and will shift the blame away from themselves the first chance they get. They are the ones who during code review will tell you that you didn't make things clear. It is never going to be clear. However even if you make things painfully obvious, they will still find a way to spectacularly fail you.

### 2. Almost there
These people are great to work with. They are the ones who have an inner spark, and pick up every dropped hint and learn quickly. They are team players who ensure that they take charge of things when they go bad and will not rest until they fix it. They are the people who might not always get things right, but a little nudge and an occasional helping hand is all they need. You can spot them asking you questions at the right moment. You can spot them when they write code which you have lesser things to complain about, and sometimes even becomes slowly indistinguishable from code you might write yourself (With their own traits of course).

### 3. At par
These are people who are great assets to the company. These are people who you can hand off large projects and expect them to make sure that every single use-case scenario is handled. They are people who will go to any lengths to make sure that the experience for the user is impeccable. They will make sure that whatever work is given to them is done correctly, in any reasonable measure of correctness you can think of. These are also people that I probably wouldn't be able to work with. When people get really good at understanding requirements, and seeing the requirements behind the requirements, they make brilliant stuff. I am furiously envious of people who build brilliant stuff because _I_ want to build all of that. Also, when I say "At par" and praise the folk who fall under this criteria for me, I am nagged by my conscience that I'm also somehow boasting. I merely have to look at all my past work (works?) that have large, perhaps yet-to-be-discovered issues for a sense of humility to set in.

### 4. Wasteful
There _is_ such a thing as _too_ good. There are people who delve into the details so deep that they're lost in it. They can't stop wanting to tweak things. They are never happy with anything that you might deliver. They spend so much time making things better that they lose focus on what's actually required (To me they are the people who aren't happy with the things that I deliver ;), of course). But that's just human nature to get defensive about yourself. However, if you find yourself losing track of the goal because you've swamped yourself with all these new requirements that you've come up with all by yourself, and they're not realistically required, and you see that your TODOs are a bunch of nice-to-haves overshadowing the must-haves, you know you've gone too far. I sometimes find myself swimming into this deep end and have to consciously pull myself back and say "You know what? Forget about it. Put it on a backlog and maybe if I have the time and really have to do it, I'll get back to it." Sometimes I do, sometimes I don't, and almost always it doesn't _really_ matter if I do.

----------------------------

So the next time your manager tells you something along the lines of "I want the list of products to show up on the screen", go ahead and see if along with the name of the products, you can also display an image, make the whole thing a link, have a neat little description show up on hover, and also make the whole thing scale correctly on any device or browser. And make it _Classy_.