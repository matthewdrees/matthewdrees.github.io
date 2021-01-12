---
layout: post
title:  "PSP A Self-Improvement Process for Software Engineers"
category: book
---

![Book cover](/assets/psp-a-self-improvement-process-for-software-engineers.jpg)

I actually bought this book a decade ago. I recall doing the first project and measuring the PSP0 statistics and finding value in the process, then I let the book languish on the shelf. Time for another go.

Here's the link to the programming and report exercises: https://resources.sei.cmu.edu/library/asset-view.cfm?assetid=530425. In the zip file: Student Workbook/PSP Student Workbook.2006.10.07/PSP Course Materials/ASGKIT PROG*.doc. (The link in the book doesn't work anymore.)

### Key Takeaways
1. Introduces a personal development process. Asks the reader to write ~20 coding problems and record some metrics (time, # defects, etc.), then analyze those metrics for process improvement and planning.
2. A software group at Maturity Model Level 1 introduces ~7.5 defects/KLOC (p 133). We should demand defect rates an order of magnitude lower. The PSP, and TSP (Team Software Process) describes a way to get there.

### Preface
"The record of most development groups is poor, but the record of software groups is particularly bad." (p xiii). "If we are to call ourselves engineers, we must learn to produce quality products on predictable schedules" (p xiv). The author compares the challenege of improving software skills to that of running faster, where you measure your current fitness with metrics, and find ways to improve.

To learn the PSP, "write a program at each of the six PSP process levels plus an additional two to four programs to master the methods and build the data to support your continuning work" (p xvii).

### 1. The Personal Process Strategy
We need better better personal performance on a software team, like a musician in a symphony. Interestingly, the author wrote 72 programs before "getting the hang" of his own process. 

### 2. The Baseline Personal Process
Do a small coding project using their introductory "PSP0" process. (Note above where these are found.) This lightweight process measures time and defects in 3 phases: planning, development, and postmortem.

The process asks you to do all your coding up front before compiling. I would normally put all the unit test code in place first, then compile, convince myself that a failing test behaves the way I want, and then code up the solution. I kept an open mind and did it all up front. In the future perhaps that will become 2 separate iterations of the process?

### 3. Measuring Software Size
"The principal ways to use size data are in planning, quality management, and process analysis" (p 45). A good measure has high correlation (degree that two sets are related) and significance (probability that the relationship occurred by chance).

Establish standards for databases (e.g. number of tables, columns) and lines of code (LOC). LOC standards can be different per language. Modified LOC can count like new LOC, though managing that is trickier than it sounds, there are a few pages dedicated to a "size accounting system" for dealing with modified/added/reused code (though instinctively, I want to use whatever git gives me for free on this).

For program quality, defects per 1,000,000 LOC (MLOC).

Calculating productivity, generally LOC divided by hours. Generally use LOC = added + modified. Expect lower productivity on small changes on larger code bases.

Physical vs Logical LOC. The author spent 50 hours on his 939 LOC pascal logical line counter. "Presently there is no compelling evidence to support any one counting method over another" (p 49). Pick one and be precise and consistent.

### 4. Planning
Why plan? ¯\_(ツ)_/¯. Kidding. See Figure 4.1 (p 63) "Project Planning Framework". The general strategy is familiar... start with a detailed "statement of work", create a "conceptual design" break down the work into tasks that you know can be done, and estimate with historical data. Follow up afterwards to see how accurate your estimate was. Report progress and follow up with any requirements changes. In the conceptual design phase, spend a few minutes thinking through alternate implementations that can reduce the work. Interestingly, don't spend more than a few hours estimating, even for "fairly large programs" (p 64).

### 5. Software Estimating
PSP estimating process. "The conceptual design must reflect the way you plan to build the product" (p 70). Break down the conceptual design into parts. Based on the function (Data, I/O, etc), number of items in each part, and size of each item (5 sizes, very small through very large), use a "relative size table" to convert that to total LOC. Then use LOC to estimate total effort.

Proxy-based estimating. A proxy should have a correlation >= 0.7 to development effort, calculated automatically, and be easily visualized. Examples are # classes and database elements. Each person on a team can create different LOC for a given proxy, so use personal data. Table 5.1 shows "relative size table" with difference in LOC per item based on category (p 77).

### 6. The PROBE Estimating Method
PROxy-Based Estimating (PROBE). Development time uses a linear-fit line based on historical size data. Example estimating problem, and how to estimate if you don't have enough data points (ideally > 3). You can use this process for non-programming tasks.

### 7. Software Planning
How to make a software schedule. Distinguish "project time" (i.e. task hours) from "calendar time" (weeks, months). It's common to only average 12-15 task hours per week with meetings, vacations, etc. 20 task hours in a week is very productive. Track "Earned Value", the "Planned Value" of a task (task hours divided by total hours), when each task is complete. The author shows the plan for creating this book, which took ~400 task hours to complete the first draft. (Nice job eating the dog food!) Calculate 70% prediction interval for estimating. Strive for task lengths for updates 2-4 times per week. Alert management to changes. The Earned Value score for planning/designing/reviewing will hopefully give motivation to actually do them :-). 

### 8. Software Quality
Great chapter! It starts with a challenge to the software industry... software developed with Capability Maturity Model level 1 (the most prevalent) result in about 7.5 defects/KLOC (p 133). We should demand this be an order of magnitude lower.

It's cheaper to find defects earlier in the process (we knew that, but worth mentioning again).

Track defect types.

Quality Measures: Defect removal yield, cost of quality (failure, prevention, and appraisal), review rates, phase-time ratios.

PSP defines: (p 146)
* Failure COQ: (compile + test time) / total time
* Appraisal COQ: (design review time + code review time) / total time
* Total COQ: Appraisal COQ + Failure COQ
* Appraisal as a % of Total Quality Costs: Appraisal COQ / Total COQ
* Appraisal to Failure Ratio (A/FR): Appraisal COQ / Failure COQ.

Several pages dedicated to PSP problem data on the above to come up with the following rules of thumb:
* A/FR value ~= 2.0 (p 146).
* 100-200 LOC/hour for effective code review rate, ideally looking for a 70% yield (p 148).
* 1.5:1 ratio of design time to coding time (p 149).
* Spend 70% of your design time in a high-quality design review. (p 150)
* Spend 70% of your coding time in a high-quality code review. (p 150)

Process Quality Index = Design/Code Time * Design Review Time * Compile Defect / KLOC * Unit Test Defects/ KLOC (p 151)

* Design/Code Time = design time / coding time
* Design Review Time = 2 * design review time / design time
* Code Review Time = 2 * code review time / design time
* Compile Defects / KLOC = 20 / (10 + compile defects / KLOC)
* Unit Test Defect / KLOC = 10 / (5 + unit test defects / KLOC)

"PQI values of 0.4 historically have had no defects found after unit testing." (p 151)

"Surprisingly, however, when developers and their teams use reasonable care throughout the process, their finished products have essentially no defects." (p 155)

Have meetings once a month or so to discuss process improvements for systemic defects.

### 9. Design and Code Reviews
This book covers personal (self) reviews, as opposed to team inspections. "Finding defects in personal reviews is much more efficient than finding them during testing" (p 195). The PSP goal is to find and fix all defects before the first compile.

Review Principles (p 169): Review your work before moving on, find and fix your defects, use checklists, follow sound review principles (small amounts, print on paper, don't do them when you're tired), measure time and defects, improve your review process, design your product so it is easily reviewed, and identify ways to prevent defects.

Review measures:
* Review yield: % of total defects removed in this review phase. It's the best measure, but you don't know the precise number until the product has shipped and been in use for a while.
* Defect-removal leverage (DRL): (p192) Defects removed / hour. Interesting graphs of yield vs LOC reviewed/hour. PSP data on programs 7-10 said code reviews twice as effective as unit tests at finding and fixing defects. Design reviews are as effective (but likely have follow on benefits) (p 193).

Even experienced developers introduce 100 defects / KLOC before compiling.
