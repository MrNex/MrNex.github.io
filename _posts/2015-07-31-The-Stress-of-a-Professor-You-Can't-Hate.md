---
layout: post
title: The Stress of a Professor You Can't Hate
---

Like most studious academics, I unfortunately give a shit about my GPA. It's a fault of mine, really, because it proves that I care what other people think about me. While yes, attaining the knowledge itself is most important, my GPA *proves* to the world that I have attained that knowledge. So forgive me, but I care.

Now don't get me wrong; I am all for **hard** courses. I love a challenge. I am okay with getting a B in the course, especially when *it's just that difficult a topic*. In that scenario, a B says I have certainly come a long way throughout the course even though I have not mastered such a difficult topic. But you better count on me continuing my studies in the area until I have! As a matter of fact, I think most courses I am subject to just aren't difficult enough. I often feel the professors give too much leeway and that many of the grades given out weren't truly deserved (yes, mine included in a few cases). This frustrates me for two reasons:

1. I feel my grade is less meaningful an achievement.
2. I feel the grade is a poor representation of my level of knowledge on the subject- and therefore difficult to use constructively.

However I've recently met the flip side of this scenario: a nit-picking nightmare grader of a professor who's grades **do not at all** indicate my level of understanding of a topic.

Let me start by saying this: I do not dislike this professor. As far as his teaching style and demeanor go I really do enjoy him as a person. Even if he is so completely unapprochable and downright frightening and demeaning. Not to mention no help at all during office hours when he outright encourages students to come and see him and he will help them. But his grading is cruel. Let me break this down for you- the entire course grade is graded based on 4 projects and 3 examinations. Each project grade is graded out of 30 points, where 10 points are for meeting "Design Criteria" and 20 points are for passing test cases.

The test cases are graded strictly, but that's not too big a deal. By strictly I mean if you accidently have a double-space after a period in the output and he doesn't-- you get 0 credit for that test case. This is totally fine though because he specifies exactly what he wants the output to be. Pain in the ass or not-- it's fair. However, let me clarify what he apparently means by "Design Criteria". He means if anything in your entire program isn't the way he likes it, you lose 33.3% of the entire project grade. Let me stress the fact that your program can pass *every single test case* and be designed to be extensible and modular but if he finds one thing wrong, 33.3% of your grade gone. Here's an example of what I lost 33.3% of my entire project grades on: I did not check for an error which has absolutely 0% chance of ever happening. I approached him about this, kind of frustrated but moreso utterly confused and he told me, "That's what is called a pre-condition check", and I was SOL.

Mind you, in the documents he never said, "Check for this error", he said, "It is an error if...", which I absolutely agree with! That would have been a horrible error! It would have been a massive error on the programmers part! A massive error that when programmed correctly **has no chance of ever happening**. On every single project he has come up with something to dock off 33.3% of my grade. Each and every one literally comes down to ~6 words being added to the program.

The best part? The exams are graded with the exact same scrutiny.

For any professors out there reading this, please give grades that assess a students knowledge of a topic, not grades that are nit-picking just to spite them. And if you do want things *perfect*, I understand. A 100% should only be granted to *perfect* projects! But don't knock them down to a 66.6% for something that is solved by:

    ASSERT(i > count);


