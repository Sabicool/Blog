---
title: "Introduction to Bayes' Theorem"
author: ["Sab"]
date: 2022-02-28
lastmod: 2026-01-01T20:20:23+11:00
categories: ["Mathematics"]
draft: false
tags: ["Interesting", "Medicine", "Maths"]
---

So I got a rapid antigen test recently and I took a look at the instructions. It gave a table showing how they calculated its sensitivity and specificity.

<a id="figure--Clinical Performance of the Rapid Antigen Test"></a>

{{< figure src="/ox-hugo/rattable.jpg" caption="<span class=\"figure-number\">Figure 1: </span>Photo of the table that was in the information sheet that came in the box" >}}

<div class="table-caption">
  <span class="table-number">Table 1:</span>
  Table of clinical performance of the SARS-CoV-2 Antigen Rapid Test as on the information sheet (see Fig 1.).
</div>

|                 | PCR confirmed sample number | Correct identified | Rate  |
|-----------------|-----------------------------|--------------------|-------|
| Positive Sample | 341                         | 327                | 95.9% |
| Negative Sample | 500                         | 497                | 99.4% |
| Total           | 841                         | 824                | 98.0% |

Which all in all makes sense. Sensitivity is defined as true positives over all cases with the outcome (true positives and false negatives) and hence it would be \\(\frac{327}{341}\approx95.9\\%\\). Similarly specificity and accuracy can be calculated to be 99.4% and 98.0% respectively using the data from the table.

Say for arguments sake I tested positive. This _does not_ mean that I am 95.9% likely to have SARS-CoV-2. If we take a look at the formula for sensitivity we note that it gives the probability/chance of having a positive result in this test if I already know that I am positive.

\\[
\rm{Sensitivity}=\frac{\rm{True\\; Positives}}{\rm{All\\; With\\; Disease}}=\frac{\rm{True\\; Positives}}{\rm{True\\; Positives}+\rm{False\\; Negatives}}
\\]

Instead we are interested in the chance that I have the disease knowing that I have tested positive (in mathematical terms \\(P(\rm{Have\\;Covid}|\rm{Test\\;Positive})\\)). This is called the positive predictive value and can be calculated as:

\begin{align\*}
\rm{PPV}=&\frac{\rm{TP}}{\rm{Test\\;Positive}}\\\\
&=\frac{\rm{TP}/\rm{Everyone}}{\rm{Test\\;Positive}/\rm{Everyone}}\\\\
&=\frac{P(\rm{Test\\;Positive}\cap\rm{Have\\;Covid})}{P(\rm{Test\\;Positive})}
\end{align\*}

If we assign event \\(A\\) as having covid and event \\(B\\) as testing positive, we can prove that:

\begin{align\*}
P(A|B) &=\frac{P(A\cap B)}{P(B)} \\\\
P(B|A) &=\frac{P(A\cap B)}{P(A)} \\\\
\therefore\quad P(A\cap B)&=P(B|A)P(A) \\\\
P(A|B)&=\frac{P(B|A)P(A)}{P(B)}
\end{align\*}

This implies that:

\begin{align\*}
\rm{PPV}&=\frac{P(\rm{Test\\;Positive}|\rm{Have\\;Covid})\cdot P(\rm{Have\\;Covid})}{P(\rm{Test\\;Positive})}\\\\
&=\frac{\rm{Sensitivity}\cdot P(\rm{Have\\;Covid})}{P(\rm{Test\\;Positive})}
\end{align\*}

If we test positive we know that we could either have the disease and tested positive (i.e. a true positive) or we could not have the disease and tested positive (i.e. a false positive). We can therefore calculate the probability that we test positive (\\(P(\rm{Test\\;Positive})\\)) by calculating the chance we test positive given we have the disease (\\(\rm{Sensitivity}\cdot P(\rm{Have\\;Covid})\\)) and the probability we test positive given that we don't have the disease (\\((1-\rm{Specificity})\cdot P(\rm{Don't\\;Have\\;Covid})\\)). Thus:

\\[
\rm{PPV}= \frac{\rm{Sensitivity}\cdot P(\rm{Have\\;Covid})}{\rm{Sensitivity}\cdot P(\rm{Have\\;Covid})+(1-\rm{Specificity})\cdot P(\rm{Don't\\;Have\\;Covid})}
\\]

This raises the question: _What is the probability that I have Covid (before having the test)_, i.e. what is \\(P(\rm{Have\\;Covid})\\)? And hence what is PPV?

If we were to think about this realistically, \\(P(\rm{Have\\;Covid})\\) is dependent on a clinical judgement based on my symptoms and who I was exposed to. For arguments' sake lets just ignore of all of this and say that \\(P(\rm{Have\\;Covid})\\) is the prevalence of Covid-19.

As of writing this article there are 14,621 active Covid-19 cases and 2.667 million people in my region. This gives a prevalence of 0.737%. Plugging this in we get:

\\[
\rm{PPV}=\frac{95.9\\%\cdot0.737\\%}{95.9\\%\cdot0.737\\%+(1-99.4\\%)\cdot(1-0.737\\%)}\approx54.3\\%
\\]


## The Bayesian Paradox {#the-bayesian-paradox}

Weird right? So if I get a positive result on the RAT I can only be 50% sure I actually have SARS-CoV-2. Therein lies the _Bayesian Paradox_. Though its not really a paradox but rather contradictory to what one might expect.

Suppose I go to a different pharmacy and buy another rapid antigen testing kit but from a different company just to make sure the results are totally independent. What we substitute as \\(P(\rm{Have\\;Covid})\\) is 54.3% as this is the _probability that I have Covid (before doing the second test)_.

\\[
\rm{PPV}=\frac{95.9\\%\cdot54.3\\%}{95.9\\%\cdot54.3\\%+(1-99.4\\%)\cdot(1-54.3\\%)}\approx99.5\\%
\\]

Which makes considerable sense. The probability of having two false positives should be much lower than the probability of just having 1 test screw up and giving a false positive.


## Concluding Remarks {#concluding-remarks}

When talking about Bayes' Theorem, many explain how counter-intuitive it is (myself included). Instead Derek, in his [video](https://www.youtube.com/watch?v=R13BD8qKeTg), argues that Bayes' Theorem is really the opposite, i.e. maybe we're too good at internalising the thinking behind Bayes' Theorem.

He argues that we can get used to results. Getting rejected or failing at something or getting paid a low wage and so we keep updating our beliefs to a point of near certainty so that we think that this is the that nature is. Consider the quote:

> Everything is impossible until it's done -- Nelson Mandella

In essence he further states that your prior \\(P(H)\\) might even be 0 if you've seen no instances of something like what you aim to do being done before.


## References and Useful Links {#references-and-useful-links}

[Derek's video](https://www.youtube.com/watch?v=R13BD8qKeTg) introduced me to the topic which was quite nice

As you can imagine Bayes' Theorem holds a place in medicine and I would argue that it is intrinsically used my clinicans when generating differentials. As such [statmed.org](https://statmed.org/) exists. In its own words, it uses Bayesian inference to formulate differential diagnoses.
