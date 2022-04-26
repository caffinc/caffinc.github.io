---
title: "Picking a backend language"
author: admin
layout: post
image: assets/images/2022/04/chris-ried-ieic5Tq8YMk-unsplash.jpg
permalink: /2022/04/pick-backend-langauge/
featured: true
categories:
  - technology
tags:
---

I recently came across a question on [Ask HN](https://news.ycombinator.com/item?id=31158970) where the asker wanted to know which backend language they should learn. These posts can get opinionated, and I try not to pick sides when it comes to technology. More often than not, this year's winner is next year's bad idea. However there has never been a better time for learning a new language and getting into the development game. I thought about what, in my experience, would be the few things that a backend developer starting out today would benefit most from.

Having worked professionally almost exclusively in the backend for about 11 years now, and being a hobby developer for over 22 years, this is my advice:

## It's important to start and keep at it
<img src="/assets/images/2022/04/jukan-tateisi-bJhT_8nbUA0-unsplash.jpg" alt="Get started" width="800"
  height="200" style="object-fit: cover;"/>
Pick whichever language or technology you want to use, and just learn. The longer you stick with it, the better you'll get. It naturally follows that the more you enjoy something, the longer you'll stick to it. Most of the knowledge you gain from one language can be transferred to another, except perhaps some syntax. Try to pick a language that doesn't feel too overwhelming if you don't have someone else to teach you and push you- Python is an excellent choice.

## Pick a paradigm
<img src="/assets/images/2022/04/damian-siodlak-KyCzR06ypjI-unsplash.jpg" alt="Choice" width="800"
  height="200" style="object-fit: cover;"/>
There are a few different [programming "paradigms"](https://en.wikipedia.org/wiki/Programming_paradigm) out there, but the most popular (i.e. Job market) ones are `Object Oriented Programming (OO/OOP/OOPS)` and `Functional Programming (FP)`. There are distinct advantages for both, so it's almost like an early choice you make in a game that affects the rest of your playthrough. To switch, you have to go back to the beginning and make the other choice, having to learn a lot of new things again, but you can also apply some of your tangential knowledge.

It can sometimes be tricky to wrap your head around one if you're used to the other. You can learn both in Python, but some languages force you to pick one or the other a bit more. Java, until version 8 was almost entirely Object Oriented. With Java 8+ you have a [lot more functional programming (half) baked in](https://www.baeldung.com/java-8-functional-interfaces). However most of the larger companies using Java are used to the OO way of things, even resisting changes that introduce FP into the mix for various reasons (tooling support, debuggability, etc.).

Companies that want to do Functional Programming rarely pick Java, going for more traditional FP-first languages like Closure, Haskell, Scala, etc. There isn't a shortage of jobs in the market for either paradigms at the moment, however being focused on one may make it harder to be employed in the other because that's where your experience will lie. I once had offers to join a company that did Java (OO) vs a company that did Scala (FP). I picked the one that paid more, although a part of me terribly misses Scala now. 

## Learn something relevant
<img src="/assets/images/2022/04/paul-bill-U2TjtLJe4Z0-unsplash.jpg" alt="Get started" width="800"
  height="200" style="object-fit: cover;"/>
Learn a language that is current, has a large community and great IDE support. This can help you learn quickly. [JetBrains has IDEs for several languages- Java and all JVM languages (Scala, Closure, Kotlin, etc.), Javascript (and TypeScript), Python, C, etc.](https://www.jetbrains.com/products/#type=ide) These languages are all great for all three major OSes- Windows, Mac and Linux. You could pick C# which has amazing IDE support ([Visual Studio](https://visualstudio.microsoft.com/)), but that's a bit Microsoft-heavy. Again, Python is a good choice and most OSes already have it pre-installed. [If you do pick Python, pick version 3.](https://www.python.org/doc/sunset-python-2/) A lot of documentation out there is for version 2 as well because somehow even decades later people refuse to move on.

The most important thing to know is that if it's current, you will not run into use cases that are impossible to accomplish in the near future. If it has a large community, you can find an answer to your question on stack overflow because someone else already asked it. If it has great IDE support, it will guide you every step of the way - through syntax errors, compilation errors, stack traces, etc. Once you've learnt one language a fair amount, you can always pick another that is exotic or isn't as forgiving to new comers. You'll see that a large amount of your knowledge can easily transfer. 

## Reduce your cognitive load
<img src="/assets/images/2022/04/magda-v-yrrR17aAGiQ-unsplash.jpg" alt="Choice" width="800"
  height="200" style="object-fit: cover;"/>
If you're 10 years old, you probably have more time on hand than if you're 35. More often than not, you don't have the time to learn *everything* about *something* anymore. If you're learning programming while juggling a day job and kids, you don't want to spend a week learning what things like [Pointers](https://en.wikipedia.org/wiki/Pointer_(computer_programming)), [ASTs](https://en.wikipedia.org/wiki/Abstract_syntax_tree), [Pass-by-reference](https://en.wikipedia.org/wiki/Evaluation_strategy#Call_by_reference), [case matching](https://docs.scala-lang.org/tour/pattern-matching.html) etc. are unless it's what you absolutely need and there isn't a library that will do it for you.

How deep you go and in what language could determine where you will be hired and what kind of roles are right for you. Take a look at what companies you want to work at use. You can get a well paying backend job without knowing what pointers are, because let's face it- companies want to solve problems at the end of the day, not compete with each other on how much esoteric knowledge they possess in an elaborate trivia contest. However there's almost zero chance you will write any Linux kernel code without knowing what pointers are.

If you already know something, use it. If you're a Javascript developer, maybe learn some TypeScript. It extends beautifully to both the front end and the back end and uses a lot of the same tools in both places, so you only have to learn once.

## Build projects
<img src="/assets/images/2022/04/hannah-gibbs-BINLgyrG_fI-unsplash.jpg" alt="Choice" width="800"
  height="200" style="object-fit: cover;"/>
Pick a problem that you want to solve, and try to solve it with the language of your choice. Pretty soon you'll see why that language is a good choice or not, and maybe learn how to overcome a problem with the language you've picked. Most problems can be solved by most languages, only how easy or painful the experience is changes.

Once you solve your original problem somewhat, you can go back to the start and see if what you now know about the language, architecture, components etc. can help you solve the problem better. You'll also probably find that your problem is better defined now, and a lot of things you thought were important at the start don't matter anymore, or you changed your requirements because you found a better way as you worked through it the first time. 

Build a few of these solutions to problems and soon you'll be an expert at your language or technology of choice and will have something to show for it as well. It's like chiseling rock to make a sculpture - your first one probably looks like a smaller, different looking rock, and your current one a more well defined thing of beauty. Your code will improve the same way, except it is a lot more forgiving. You can start over and over on whatever small or large portion of the code, and make it better without losing anything except time. I would even argue that you'll learn a lot in the process, and learning is not a waste of time. *(Please note: I am not a sculptor, I don't know enough about rocks either. Do not apply my advice on rocks.)*

## TL;DR
If your question is simply "I want to be a backend developer, help me pick a language, and that's it", the tl;dr version would be- [Do you know where you want to work at? Learn the language they use](https://en.wikipedia.org/wiki/Programming_languages_used_in_most_popular_websites). If you don't have a preference, pick [TypeScript](https://www.typescriptlang.org/docs/handbook/typescript-from-scratch.html) as it does a lot of things right and your knowledge will extend to the frontend as well. Or pick [Python](https://www.pythontutorial.net/getting-started/) as it also does a lot of things right as well, and will extend to [machine learning](https://scikit-learn.org/stable/) and [microcontrollers](https://micropython.org/). Or pick any backend-oriented language out there with an open mind and use it to solve a real world problem you have, you will learn to be a backend developer.
