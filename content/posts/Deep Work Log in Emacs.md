---
title: "Deep Work Log in Emacs"
author: ["Sab"]
date: 2023-09-13
lastmod: 2026-01-01T20:20:05+11:00
categories: ["Productivity"]
draft: false
tags: ["Emacs", "Workflow"]
---

I have been experimenting with tracking my deep work. As such I've made a function that queries me on clocking out from a task and tracks it to a table if requested.

<a id="table--Log"></a>
<div class="table-caption">
  <span class="table-number"><a href="#table--Log">Table 1</a>:</span>
  Deep work log made upon asking user after clocking out
</div>

| Date                                                                                   | Time  | Duration | Task Name                | C | Activity   | Focus | Location | Technique | Notes             |
|----------------------------------------------------------------------------------------|-------|----------|--------------------------|---|------------|-------|----------|-----------|-------------------|
| <span class="timestamp-wrapper"><span class="timestamp">[2023-05-13 Sat]</span></span> | 03:25 | 1:00     | Example task             | t | Coding     | 3     | Home     | nil       | probs &gt; time   |
| <span class="timestamp-wrapper"><span class="timestamp">[2023-05-14 Sun]</span></span> | 13:58 | 0:43     | Example work             | t | Work       | 4     | Home     | nil       | pressured by time |
| <span class="timestamp-wrapper"><span class="timestamp">[2023-05-14 Sun]</span></span> | 22:42 | 0:15     | Planing task             | t | Planning   | 3     | Home     | nil       | bit distracted    |
| <span class="timestamp-wrapper"><span class="timestamp">[2023-05-15 Mon]</span></span> | 18:04 | 0:37     | Make flashcards on topic | t | Cardmaking | 4     | Home     | nil       | made flashcards   |
| <span class="timestamp-wrapper"><span class="timestamp">[2023-05-16 Tue]</span></span> | 10:48 | 0:15     | Watch lecture            | t | Cardmaking | 4     | Library  | nil       | short lecture     |

In the table above date, time, duration and task name automatically. Additionally I make it track some user inputted data that I wanted to track such as the completion status (marked by "C"), type of activity, level of focus, any productivity techniques used and additional notes.

This table can be used to generate graphs using something like gnuplot. For example:

```gnuplot
reset
set title "Duration over time"
unset key
set xdata time
set timefmt "%Y-%m-%d %a"
set format x "%d/%m"

set xtics "2023-05-13 Sat 00:00", 86400*1

set ydata time
set format y "%H:%M"
set yrange [0:7200]

set boxwidth 86400*0.8 absolute
set style fill solid 0.1 border -1
plot data using 1:((timecolumn(3,"%H:%M"))*1) smooth frequency with boxes
```

{{< figure src="/ox-hugo/test.png" >}}

This produces the image above. You can change it up to graph different data together to find new relationships. For example time of day against focus to figure out which time of the day you tend to be more focused.


## How its done {#how-its-done}

A function is defined to grab last clocked duration of the item at point

```emacs-lisp
(defun my/last-duration ()
  (let* ((e (org-element-at-point))
         (type (org-element-type e)))
    (when (and (eq type 'drawer) (string= (org-element-property :drawer-name e) "LOGBOOK"))
      (let ((contents-beg (org-element-property :contents-begin e)))
        (save-excursion
          (goto-char contents-beg)
          (let* ((e (org-element-at-point))
                 (type (org-element-type e)))
            (when (eq type 'clock)
              (org-element-property :duration e))))))))
```

Another function is used to grab the name of the last clocked task

```emacs-lisp
(defun my/get-last-clocked-task ()
  "Get the name of the last clocked task."
  (save-excursion
    (goto-char (car org-clock-history))
    (nth 4 (org-heading-components))))
```

The following function is used to prompt the user for data (to be run when clocking out from a task)

```emacs-lisp
(defun my/org-clock-out-hook ()
  "Run after clocking out of a task to display time clocked in."
  (when (yes-or-no-p "Was that a deep work session?")
    (save-excursion
      (org-back-to-heading)
      (search-forward ":LOGBOOK:" nil t)
      (let* ((org-clocked-duration (my/last-duration))
             (org-last-clocked-task (my/get-last-clocked-task))
             (org-capture-templates
              `(("d" "Deep Work Task" table-line
                 (file ,(concat org-roam-directory "deep_work.org"))
                 "| %u | %(format-time-string \"%H:%M\" (float-time)) | %(with-current-buffer (org-capture-get :original-buffer) org-clocked-duration) | %(identity org-last-clocked-task) | %(prin1-to-string (yes-or-no-p \"Did you complete the task\")) | %^{Activty Type|Flashcards|Notetaking|Questions|Work|Planning|Writing|Coding} | %(string (read-char-from-minibuffer \"[1-5..]:How focused were you? \" '(?1 ?2 ?3 ?4 ?5))) | %^{Location|Home|Coffee|Library} | %^{Techniques|nil|Timer|Recording} | %? "
                 :unnarrowed t))))
        (org-capture nil "d")))))
```

In detail `(when (yes-or-no-p "Was that a deep work session?")...` Prompts the user "_Was that a deep work session?_", where if the user responds with true, then the rest executes. This way, more trivial non-deep work sesssions can be opted out from being tracked.

```emacs-lisp
...
(save-excursion
      (org-back-to-heading)
      (search-forward ":LOGBOOK:" nil t)
...
```

Saves the point in the buffer, goes to the heading and puts the cursor at the first instance of ":LOGBOOK:". Hence I don't anticipate this to work if there is text in the properties drawer that has ":LOGBOOK:[^fn:1]".

```emacs-lisp
...
(let* ((org-clocked-duration (my/last-duration))
             (org-last-clocked-task (my/get-last-clocked-task))
             (org-capture-templates
              `(("d" "Deep Work Task" table-line
                 (file ,(concat org-roam-directory "deep_work.org"))
                 "| %u | %(format-time-string \"%H:%M\" (float-time)) | %(with-current-buffer (org-capture-get :original-buffer) org-clocked-duration) | %(identity org-last-clocked-task) | %(prin1-to-string (yes-or-no-p \"Did you complete the task\")) | %^{Activty Type|Flashcards|Notetaking|Questions|Work|Planning|Writing|Coding} | %(string (read-char-from-minibuffer \"[1-5..]:How focused were you? \" '(?1 ?2 ?3 ?4 ?5))) | %^{Location|Home|Coffee|Library} | %^{Techniques|nil|Timer|Recording} | %? "
                 :unnarrowed t))))
        (org-capture nil "d"))
...
```

Uses a table-line org-capture template to fill the last clocked duration, task name to parts of the template and clock out date and time, and prompt the user for additional information such as:

-   Whether the task was completed
-   The type of activity being completed
-   Focus level (quantitatively from 1 to 5)
-   Location
-   Any productivity techniques that were used
-   Leaves the cursor at the additional notes part of the table

Finally, `(add-hook 'org-clock-out-hook 'my/org-clock-out-hook)` adds the function as a hook to `org-clock-out-hook` so when clocking out of a task, it is run.


## Final Configuration {#final-configuration}

In total this should be able to be incorporated into your cofig with reasonable success:

```emacs-lisp
(defun my/last-duration ()
  (let* ((e (org-element-at-point))
         (type (org-element-type e)))
    (when (and (eq type 'drawer) (string= (org-element-property :drawer-name e) "LOGBOOK"))
      (let ((contents-beg (org-element-property :contents-begin e)))
        (save-excursion
          (goto-char contents-beg)
          (let* ((e (org-element-at-point))
                 (type (org-element-type e)))
            (when (eq type 'clock)
              (org-element-property :duration e))))))))

(defun my/get-last-clocked-task ()
  "Get the name of the last clocked task."
  (save-excursion
    (goto-char (car org-clock-history))
    (nth 4 (org-heading-components))))

(defun my/org-clock-out-hook ()
  "Run after clocking out of a task to display time clocked in."
  (when (yes-or-no-p "Was that a deep work session?")
    (save-excursion
      (org-back-to-heading)
      (search-forward ":LOGBOOK:" nil t)
      (let* ((org-clocked-duration (my/last-duration))
             (org-last-clocked-task (my/get-last-clocked-task))
             (org-capture-templates
              `(("d" "Deep Work Task" table-line
                 (file ,(concat org-roam-directory "deep_work.org"))
                 "| %u | %(format-time-string \"%H:%M\" (float-time)) | %(with-current-buffer (org-capture-get :original-buffer) org-clocked-duration) | %(identity org-last-clocked-task) | %(prin1-to-string (yes-or-no-p \"Did you complete the task\")) | %^{Activty Type|Flashcards|Notetaking|Questions|Work|Planning|Writing|Coding} | %(string (read-char-from-minibuffer \"[1-5..]:How focused were you? \" '(?1 ?2 ?3 ?4 ?5))) | %(my/fast-org-selection '(\"Home\" \"Coffee\" \"Library\") \"Where were you? \") | %^{Techniques|nil|Timer|Recording} | %? "
                 :unnarrowed t))))
        (org-capture nil "d")))))

(add-hook 'org-clock-out-hook 'my/org-clock-out-hook)
```


## Improvements {#improvements}

I quite like the dialogue when entering TODO keywords (i.e. using `org-fast-todo-selection`). I took inspiration from [this answer on stackexchange regarding fast effort selection](https://emacs.stackexchange.com/a/59475/31297) and wrote this function

```emacs-lisp
(defun my/fast-selection (values prompt)
  "Modification of `org-fast-todo-selection' for general use.
'VALUES' is a list of accepted entries for user input and 'PROMPT' is a string.

Inspired from https://emacs.stackexchange.com/questions/59424/org-set-effort-fast-effort-selection"
  ;; Format effort values into an alist keyed by index
  (let* ((fulltable (seq-map-indexed (lambda (e i) (cons (car e) (string-to-char (int-to-string i))))
                                     (mapcar 'list values)))
         (maxlen (apply 'max (mapcar
                              (lambda (x)
                                (if (stringp (car x)) (string-width (car x)) 0))
                              fulltable)))
         (expert t)
         (fwidth (+ maxlen 3 1 3))
         (ncol (/ (- (window-width) 4) fwidth))
         tg cnt e c tbl subtable)
    (save-excursion
      (save-window-excursion
        (if expert
            (set-buffer (get-buffer-create " *Fast Selection"))
          (delete-other-windows)
          (set-window-buffer (split-window-vertically) (get-buffer-create " *Fast Selection*"))
          (org-switch-to-buffer-other-window " *Fast Selection*"))
        (erase-buffer)
        (setq tbl fulltable cnt 0)
        (while (setq e (pop tbl))
          (setq tg (car e)
                c (cdr e))
          (print (char-to-string c))
          (when (and (= cnt 0))
            (insert "  "))
          (setq prompt (concat prompt "[" (char-to-string c) "] " tg " "))
          (insert "[" c "] " tg (make-string
                                 (- fwidth 4 (length tg)) ?\ ))
          (when (and (= (setq cnt (1+ cnt)) ncol)
                     ;; Avoid lines with just a closing delimiter.
                     (not (equal (car tbl) '(:endgroup))))
            (insert "\n")
            (setq cnt 0)))
        (insert "\n")
        (goto-char (point-min))
        (unless expert (org-fit-window-to-buffer))
        (message (concat "[1-9..]:Set [SPC]:clear"
                         (if expert (concat "\n" prompt) "")))
        (setq c (let ((inhibit-quit t)) (read-char-exclusive)))
        (setq subtable (nreverse subtable))
        (cond
         ((or (= c ?\C-g)
              (and (= c ?q) (not (rassoc c fulltable))))
          (setq quit-flag t))
         ((= c ?\ ) nil)
         ((setq e (or (rassoc c subtable) (rassoc c fulltable))
                tg (car e))
          tg)
         (t (setq quit-flag t)))))))
```

So that something like `(my/fast-org-selection '("Home" "Coffee" "Library") "Where were you? ")` can be used to request user input. So you can achieve a somewhat more streamlined data entry with:

```emacs-lisp
(defun my/org-clock-out-hook ()
  "Run after clocking out of a task to display time clocked in."
  (when (yes-or-no-p "Was that a deep work session?")
    (save-excursion
      (org-back-to-heading)
      (search-forward ":LOGBOOK:" nil t)
      (let* ((org-clocked-duration (my/last-duration))
             (org-last-clocked-task (my/get-last-clocked-task))
             (org-capture-templates
              `(("d" "Deep Work Task" table-line
                 (file ,(concat org-roam-directory "deep_work.org"))
                 "| %u | %(format-time-string \"%H:%M\" (float-time)) | %(with-current-buffer (org-capture-get :original-buffer) org-clocked-duration) | %(identity org-last-clocked-task) | %(prin1-to-string (yes-or-no-p \"Did you complete the task\")) | %^{Activty Type|Flashcards|Notetaking|Questions|Work|Planning|Writing|Coding} | %(string (read-char-from-minibuffer \"[1-5..]:How focused were you? \" '(?1 ?2 ?3 ?4 ?5))) | %(my/fast-org-selection '(\"Home\" \"Coffee\" \"Library\") \"Where were you? \") | %^{Techniques|nil|Timer|Recording} | %? "
                 :unnarrowed t))))
        (org-capture nil "d")))))
```

Though in hindsight, I much prefer the fast tag selection menu where one can tab out for free user output.

[^fn:1]: Though I can't imagine whiy someone would do that
