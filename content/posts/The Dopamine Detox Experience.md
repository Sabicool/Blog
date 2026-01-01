---
title: "My progress with a dopamine detox of sorts"
author: ["Sab"]
date: 2023-03-31
lastmod: 2026-01-01T20:19:43+11:00
categories: ["Productivity"]
draft: false
tags: ["Study", "Workflow"]
---

_TLDR: I improved my productivity but not by way of increasing my attention span but rather by removing access to unproductive behaviours._

The dopamine detox is a confusing productivity fad rooted in the idea that excessive stimulation of the brain's reward centre can lead to experience satisfaction from everyday activities and a greater propensity for addictive behaviour. It involves avoiding stimuli that trigger the release of dopamine in the hope to control impulses such as reaching for your phone.

My main goal out of doing a dopamine detox was to increase my productivity by improving my attention span. It seems that removing distractions was essential to a dopamine detox (<a href="#citeproc_bib_item_2">Meurisse 2021</a>; <a href="#citeproc_bib_item_1">Knapp and Zeratsky 2018</a>) and reaching deep work (<a href="#citeproc_bib_item_3">Newport 2016</a>) and no better way to start that than with open systems.


## Open Systems {#open-systems}

The idea of an open system is a source of enjoyment which is effectively _endless_ (e.g. Facebook feeds but even things like messages[^fn:1]). This ultimately revolves around short form videos and memes.
I figured there was effectively two ways to "close" an open system:

1.  Not engage in the open system at all, by going pretty much cold turkey or making it so annoying to use that you only use it productively
2.  Setting a limit (such as time or frequency) on an open system so you're unable to access it after you reach the limit

I used approach 1 for facebook, instagram and reddit. I uninstalled the applications from my iPad and phone. If I have to use instagram and facebook I use them inside the browser but also set a time limit as well. Viewing reels inside of the browser is such an unpleasant experience that I don't do it for very long if I ever do it. If I did ever really need to use the app version, I would install it but then quickly uninstall it after completeing whatever required me to use the app[^fn:2].

I used approach 2 for youtube mainly. I uninstalled/disabled the Youtube app from my phone and iPad. This worked quite well provided that I had the self control to not use the application. On my laptop I used the addon [LeechBlock](https://addons.mozilla.org/en-US/firefox/addon/leechblock-ng/) which limits the time I can access open systems to a certain duration[^fn:3].


### On Social Media {#on-social-media}

You'll also find a propensity of videos describing experiences and advice regarding quitting social media and it is almost always mentioned in passing when discussing a dopamine detox

-   [Quit social media | Dr. Cal Newport | TEDxTysons](https://www.youtube.com/watch?v=3E7hkPZ-HTk)
-   [i quit social media, and it was the best decision of my life](https://www.youtube.com/watch?v=45h4aWhmvfg)
-   [The Actual Benefits of Quitting Social Media (Dopamine Detox)](https://www.youtube.com/watch?v=PS6QBarL9UQ)

Some people suggest the benefit of quitting social media on their mental health. Quotes such as the following come to mind when discussing such things:

> “If you try to be someone else, you’ll become nobody at all. The only great person you have the possibility of becoming is the greatest version of yourself, and that is a pretty great person.

I never really found myself to be using social media for posting and improving my self image to begin with, so my main benefit from detoxing derrived from not engaging in unproductive behaviours.


## The Daily Highlight {#the-daily-highlight}

This is the idea that you set one task that should be done for that day as the _highlight_ of the day. I realised the importance of setting a daily highlight on reflecting halfway into my 8 week placement realising that medical school thus far this year has felt like a blur loosely consisting of:

1.  Waking up
2.  Going to placement
3.  Going home tired
4.  Studying for a bit
5.  Go to sleep

Importantly, I was letting placement be the dominant thing in the day. Instead, the purpose of a highlight is essentially what accomplishment or moment you want to savour for that day. I began by picking highlights based on urgency (i.e. What's the most pressing thing I have to do today), but it felt like I was reacting to my circumstances and my highlights were mainly studying or medicine related tasks.

Part of the process of a dopamine detox is to substitute hyperstimulatory activities with less stimulating activities (i.e. instead of watching Netflix, going gym or reading). To do this, I made my highlights more related to satisfaction and joy.

Importantly, do not choose a highlight that cannot be completed in that day. I found that this was bad because:

1.  I didn't finish it
2.  Once I realised it was too long and didn't complete it, I'd become unmotivated

<a href="#citeproc_bib_item_1">Knapp and Zeratsky 2018</a> recommends choosing a highlight that takes **sixty to ninety minutes.**


## Reflect {#reflect}

I will also try to reflect on the day to see if I managed to complete my daily highlight, how I feel about detoxing, where I wasted time and in general. I currently just use an [org-roam](https://www.orgroam.com/) dailies capture template for this:

```emacs-lisp
("r" "reflection" plain
             (file "~/reflection.org")
             :target
             (file+head "%<%Y>/%<%B>/%<%Y-%m-%d>.org" "
:PROPERTIES:
:ENERGY: %^{Energy|~|1|2|3|4|5|6|7|8|9|10}
:FOCUS: %^{Focus|~|1|2|3|4|5|6|7|8|9|10}
:HIGHLIGHT: %^{Highlight achieved|yes|no}
:END:
#+title: %<%A, %d %B %Y>
#+filetags :Dailies: "))
```

Where ~/reflection.org is:

```org
* Reflection
- Today's Highlight :: %(mapconcat 'identity (org-ql-select (org-agenda-files) '(and (ts :on today) (tags "highlight")) :action '(org-get-heading t t)) " ")
- Tactics tried today :: %^{Tactics tried}

** How it went
%^{How it went}%?

** Moment I'm grateful for tomorrow
%^{Moment I'm grateful for}

** What to try for tomorrow
%^{What to try (again) for tomorrow}
```

This is the function to pull today's highlight. It uses [org-ql](https://github.com/alphapapa/org-ql).

```emacs-lisp
(mapconcat 'identity
           (org-ql-select
             (org-agenda-files)
             '(and (ts :on today)
                   (tags "highlight"))
             :action '(org-get-heading t t))
           " ")
```

I generally try to link the tactics using org-roam to nodes specific for that tactic. This way I'm able to show backlinks (using org-roam-dblocks from [nursery](https://github.com/chrisbarrett/nursery)) and lookup properties for days where I used this tactic such as whether the highlight was completed that day, what my focus and energy levels for that day was. For example I have a node specific for the tactic of writing down your highlight on a sticky note and putting it in front of you.

```org
** Write it Down
Here are all the days on which I used this technique and managed to accomplish my highlight:
#+BEGIN: backlinks :filter (my/roam-db-filter "HIGHLIGHT" "yes" it)
- [[id:some ID][Friday, 10 February 2023]]
- [[id:some ID][Tuesday, 14 February 2023]]
#+END:

Here are all the days on which I used this technique and managed to accomplish my highlight:
#+BEGIN: backlinks :filter (my/roam-db-filter "HIGHLIGHT" "no" it)
- [[id:some ID][Tuesday, 28 February 2023]]
#+END:
```

Where `my/roam-db-filter` is:

```emacs-lisp
(defun my/roam-db-filter (property value node)
    (equal value (cdr (assoc property (org-roam-node-properties node)))))
```


## My Lessons {#my-lessons}

I found myself to still be as easily as distractible as before, the main change being that by limiting my access to distractions I am more likely to make progress on meaningful work. I've found some tactics that help me engage in deep work more readily, and while dopamine detoxing helped I found that it helps more in sustaining deep work rather than initiating it. In essence, I still need to plan out my day otherwise I'll find some way to engage in a dopamine binge. In summary:

-   I'm still distractible but have put up barriers to getting distracted
-   Its still hard to initiate work unless you do other things (e.g. setting up a schedule)
-   I'll occassionally engage in a dopamine binge, but importantly I try not to get upset about it

## References

<style>.csl-entry{text-indent: -1.5em; margin-left: 1.5em;}</style><div class="csl-bib-body">
  <div class="csl-entry"><a id="citeproc_bib_item_1"></a>Knapp, Jake, and John Zeratsky. 2018. <i>Make Time: How to Focus on What Matters Every Day</i>. Transworld Digital.</div>
  <div class="csl-entry"><a id="citeproc_bib_item_2"></a>Meurisse, Thibaut. 2021. <i>Dopamine Detox: A Short Guide to Remove Distractions and Get Your Brain to Do Hard Things</i>. Edited by Kerry J. Donovan.</div>
  <div class="csl-entry"><a id="citeproc_bib_item_3"></a>Newport, Cal. 2016. <i>Deep Work: Rules for Focused Success in a Distracted World</i>. 1st edition. New York: Grand Central Publishing.</div>
</div>

[^fn:1]: Provided you get enough messages sent to you at a regular enough rate
[^fn:2]: There has been a few instances, where I reinstalled an app after detoxing from it and never really spend much time on it anymore so I just kept it.
[^fn:3]: I currently have it set at 15 minutes every 2 hours between working hours
