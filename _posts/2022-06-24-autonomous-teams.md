---
layout: post
title: Autonomous Teams
categories:
    - Tech Industry
description: Why do traditional companies struggle with software development and what can be done about it?
---

If you keep an eye on German automotive companies, you will see a pattern. Their software [is a huge mess](https://www.focus.de/auto/news/pleiten-pech-und-pannen-bei-cariad-software-desaster-fuer-herbert-diess-warum-der-stuhl-des-vw-chefs-wackelt_id_97501221.html) and they have [trouble finding](https://www.spiegel.de/wirtschaft/unternehmen/elektro-offensive-mercedes-sucht-3000-softwareingenieure-a-bc5bcb34-a2c3-4151-978c-31ea89591680) and [keeping](https://www.handelsblatt.com/unternehmen/autobranche-herbert-diess-geraet-unter-druck-kritik-an-vw-softwareeinheit-cariad-waechst/28264920.html) developers, which leads to a high turnover rate, that amplifies existing issues.

In my opinion their failure is deeply rooted in the way these traditional companies are structured and how they see the role of developers. Actually solving the problem, e.g. through **autonomous product teams**, would require a massive change in company structure and culture.

<div class="message" markdown="1">

**Note:** The post might be a bit of rant due to personal experience 😅
</div>


# Traditional Companies
- Traditional companies have a deep hierarchy with lots of middle management layers and external outsourcing:
    - Developers, QA and UX/UI are at the bottom, like classical factory workers.
    - Decisions are made at the top by people without technical background and little contact (sometimes only presentations) with the software they are managing.
- There is no flexibility or room for alternative solutions (outside of R&D), as everything has to be planned and rubber-stamped in advance (waterfall). There is no way a [few rogue engineers](https://www.theverge.com/2015/10/8/9481651/volkswagen-congressional-hearing-diesel-scandal-fault) can just do what they want.
- These companies are suffocating on their own processes. There are many obstacles, privilege restrictions, custom tooling and communication channels involved, so it can take weeks to get even minor things done. Often these companies group out their own technical startups, to get out of this process hell for a short time.
- There is a lot of irrational company politics and unnecessary drama involved. This can be amusing from the sideline, until you find yourself in the line of fire.
- [Conway's law](https://en.wikipedia.org/wiki/Conway%27s_law) is in full effect. Teams are isolated silos and so is their code. This leads to a severe lack of information transparency, difficult integrations and friction losses. Usually there is no [collective or weak code ownership](https://martinfowler.com/bliki/CodeOwnership.html), so it is discouraged to contribute to other teams' code, even if you depend on their work.


# Why exactly is this so wrong?
First I strongly recommend reading this blog post by Gergely Orosz: [What Silicon Valley "Gets" about Software Engineers that Traditional Companies Do Not](https://blog.pragmaticengineer.com/what-silicon-valley-gets-right-on-software-engineers/). It perfectly captures the issues that traditional companies have:
- **Autonomy for software engineers**: Teams have no say in their product, everything is dictated from the top. It's hard to care about something you can't change or improve.
- **Curious problem solvers, not mindless resources**: The companies see developers more like classical assembly line workers, so they are looking for a [huge quantity](https://www.spiegel.de/wirtschaft/unternehmen/elektro-offensive-mercedes-sucht-3000-softwareingenieure-a-bc5bcb34-a2c3-4151-978c-31ea89591680) of "code monkeys" instead of skilled problem solvers. Their potential is wasted, because [instead of business problems to solve, they are given solutions](https://event-driven.io/en/bring_me_problems_not_solutions/) to implement without any context. Experiments or mistakes are usually discouraged.
- **Internal data, code, and documentation transparency**: Access is restricted as much as possible by default. If you can't find the documentation you are looking for, than you can not be sure wether you don't have access to it, or if it just does not exist. Documentation is sometimes written for the process and not for other developers.
- **Exposure to the business and to business metrics**: Business metrics are intended for the higher layers, because they make the decisions. Developers don't know if the feature they worked on for weeks is actually being used, which can be demotivating. Even if the team has access to metric, they can not shift priority on features that create more value.
- **Engineer-to-engineer comms over triangle-communication**: There are innumerable communication channels (i.e. no single open point of access like a group channel for every team) and it is not easy to find out who is responsible. So communication always goes over project managers. Upward communication (jumping the chain of command) is discouraged.
- **Investing in a less frustrating developer experience**: Instead of providing developers with well maintained infrastructure, the company dictates permissible technology, workflows, and internal tooling (sometimes poorly maintained). Fixing tooling issues can take a considerable amount of time, so bad workarounds are common (ignoring certificates, building manually instead of CI/CD, not using open source libs instead of dealing with legal stoppers), so development does not completely grind to a halt. Managment does not know or care about the impact, because it does not appear in their metrics, or in some cases it is just waved aside because "developers like to complain a lot".
- **Higher leverage --> higher {autonomy, pay}**: Pay grade is usually bound to the level in the hierarchy, so developers have lower pay than managers. In contrast developers at silicon valley receive [higher pay](https://www.levels.fyi/company/Google/salaries/) than non technical project managers (though there is more of a focus on strong tech leads, who have even higher pay).


# How to do it right
In my opinion these traditional companies really need small **autonomous product teams** with a **shared vision** and strong **technical leads**. Or with more buzzwords: Self-organized cross-functional agile teams and flat hierarchies.

This actually uses the teams' full potential:
- Product teams know the product they are developing inside out, can estimate costs (e.g. complexity) and see benefits (via business metrics). With this they can find better suited creative solutions to business problems, that minimize cost and maximize benefit.
- They have the necessary flexibility to quickly react to changing circumstances and requirements.
- They are able to optimize their own development flows and collaboration with other teams, which raises motivation and productivity.


# Success Stories
[Androids](https://chethaase.medium.com/androids-765c803d5ff6) by Chet Haase describes the success of the team that build the Android OS. One thing that stands out is, that they were completely autonomous within Google (though they still had access to Google's resources). They made sure to stay free from the rest of Google (processes and company politics) and always followed their long term vision. They were even decoupled from Google's hiring processes, which would have filtered out a lot of skilled OS developers.

Developer lead teams are [common at Big Tech companies](https://blog.pragmaticengineer.com/project-management-at-big-tech/). Autonomous teams are also the focus of the [Spotify Model](https://www.atlassian.com/agile/agile-at-scale/spotify), which is also used at other companies (e.g. [Rewe Digital](https://www.rewe-digital.com/inside-rewe-digital/show/anna-oliver-auf-der-agile-hr-conference-2021.html)).
