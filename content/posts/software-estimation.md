+++
date = '2024-11-01T23:36:56-07:00'
draft = false 
title = 'Software Estimation'
+++
(v0.3)

The amount of effort and time required to complete any software task is almost always underestimated. In other words, software always takes longer than you think. Here are three somewhat tongue-in-cheek strategies for estimating any software task;

* Time how long it takes to do a thing the first time and you should have a good enough handle on estimating how long it will take doing *that exact thing* again.

* Alternatively, round your first estimate up to the unit of estimation (day, week, month, etc), then double that. For example, if the initial estimate to complete a project is three and a half months, then round up to four months and double that, giving eight months. Double that again if you are providing this estimate to a producer, product owner or project manager; so sixteen months -- as there are now other groups involved. And event this estimate will be wrong, but at least you will have sufficient runway to jump ship to a different project before the uncomfortable questions are asked as to why the delivery date is continually pushed out by three weeks. 

* If you need to hit an arbitrary delivery date, and this is your first time implementing this feature, and management just sent an email raising concerns about developers padding their estimates; then work towards jettisoning seventy percent of the initial ask. This is called working towards an MVP ([Minimum Viable Product](https://en.wikipedia.org/wiki/Minimum_viable_product)). An MVP comprises of features that, if delayed, will cause a corresponding slip in the project delivery date to accommodate. If the project can launch without a feature then that feature is not MVP.

With a project running late and the deadline fast approaching, what typically happens is a series of spirited discussions with the product owner regarding the features that will be delayed until after initial launch. These features were considered MVP at the start of the project but are deemed less so as the project deadline approaches. Delaying discussions on MVP until late in the project means that significant effort was expended working on these non-MVP features, instead of having focused efforts on the features that are actually MVP. It is critical to a project's success to reach an agreement on the MVP early in the project life-cycle, and to continue these discussions at regular cadences throughout the project to account for inevitable changes in the market and the associated changes in business priorities. Do not push off these discussions until a week before the deadline.

The longer a project drags on the more requirements are subject to change, which is why the software development process should include releases of working software at regular cadences. Each release engages those with a vested interest in the project, driving business expectations (requirements) to slowly converge to reality. Releases don't have to be into production (sometimes these cannot be production releases even when using feature flags), but each release should be into an environment that supports a level of test and integration. Constant releases serve to drive out surprise. A single release at the end of a project serves to generate surprise. No-one likes surprises.

To summarize;
* Collaborate with the product owner at the project outset to limit scope.
* Release working software at a regular cadence into an environment. 
* No-one likes surprises; constant communication is a requirement for the project to succeed.
* It helps to have worked on *and delivered* software projects in order to become proficient at estimating software projects.
