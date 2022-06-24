---
layout: post
title:  Modern Software Development
categories:
    - Tech Industry
description: Why do traditional companies struggle with software development and what can be done about it?
---

If you keep an eye on German automotive companies, you will see a pattern. Their software [is a huge mess](https://www.focus.de/auto/news/pleiten-pech-und-pannen-bei-cariad-software-desaster-fuer-herbert-diess-warum-der-stuhl-des-vw-chefs-wackelt_id_97501221.html) and they have [trouble finding](https://www.spiegel.de/wirtschaft/unternehmen/elektro-offensive-mercedes-sucht-3000-softwareingenieure-a-bc5bcb34-a2c3-4151-978c-31ea89591680) and [keeping](https://www.handelsblatt.com/unternehmen/autobranche-herbert-diess-geraet-unter-druck-kritik-an-vw-softwareeinheit-cariad-waechst/28264920.html) developers, which leads to a high turnover rate, that amplifies existing issues.

In my opinion their failure is deeply rooted in the way these traditional companies are structured and how they see the role of developers. Actually solving the problem, e.g. through autonomous product teams, would require a massive change in company structure and culture.

<div class="message" markdown="1">

**Note:** The post might be a bit of rant due to personal experience 😅
</div>


# Traditional Companies
- Traditional companies have a deep hierarchy with lots of middle management layers and external outsourcing:
    - Developers, QA and UX/UI are at the bottom, like classical factory workers.
    - Decisions are made at the top by people without technical background and little contact (sometimes only presentations) with the software they are managing.
- There is no flexibility or room for alternative solutions (outside of R&D), as everything has to be planned and rubber-stamped in advance (waterfall). There is no way a [few rogue engineers](https://www.theverge.com/2015/10/8/9481651/volkswagen-congressional-hearing-diesel-scandal-fault) can just do what they want.
- These companies are suffocating on their own processes. There are so many obstacles, privilege restrictions, custom tooling and communication channels involved, it can take ages to get even minor things done. Often these companies group out their own technical startups, to get out of this process hell for a short time.
- There is a lot of irrational company politics and drama involved. This can sometimes be amusing as a developer, until you find yourself in the line of fire.
- [Conway's law](https://en.wikipedia.org/wiki/Conway%27s_law) is in full effect. Teams are isolated silos and so is their code. This leads to a severe lack of information transparency, difficult intergations and friction losses. Usually there is no [collective or weak code ownership](https://martinfowler.com/bliki/CodeOwnership.html), so it is discouraged to contribute to other teams code, even if you depend on their work.


# Why is this so wrong?
First I strongly recommend reading this blog post by Gergely Orosz: [What Silicon Valley "Gets" about Software Engineers that Traditional Companies Do Not](https://blog.pragmaticengineer.com/what-silicon-valley-gets-right-on-software-engineers/). It perfectly captures the issues that traditional companies have:
- **Autonomy for software engineers**: Teams have no say in their product, everything is dictated from the top. It's hard to care about something you can't change or improve.
- **Curious problem solvers, not mindless resources**: The companies are looking for a [huge number](https://www.spiegel.de/wirtschaft/unternehmen/elektro-offensive-mercedes-sucht-3000-softwareingenieure-a-bc5bcb34-a2c3-4151-978c-31ea89591680) of "code monkeys". They see developers more as assembly line workers. Their potential is wasted, because instead of problems to solve, they are given ready made tasks without context or underspecified and unnecessary complex solutions. Experiments or mistakes are discouraged.
- **Internal data, code, and documentation transparency**: Access is restricted as much as possible by default. If you can't find the documentation you are looking for, than you can not be sure wether you don't have access to it, or if it just does not exist.
- **Exposure to the business and to business metrics**: Business metrics are intended for the higher layers, because they make the decisions. Developers don't know if feature they developed is actually accepted by users. Even if developers were allowed to prioritize their work, they would have no way of knowing, what is actually creating value.
- **Engineer-to-engineer comms over triangle-communication**: There are too many communication channels to count and no one knows who is responsible or who you can talk to. So communication always goes over project managers. Upward communication (jumping the chain of command) is discouraged.
- **Investing in a less frustrating developer experience**: Instead of providing developers with well maintained infrastructure, the company dictates permissible technology, workflows, tools, etc. Internal tooling is poorly maintained. Often there are bad workarounds (ignoring certificates, not using open source instead of dealing with legal, building manually instead of CI/CD) so development does not completely grind to a halt. Managment does not care about the impact, because it is not on their metrics, or it is just waved aside because "developers like to complain a lot".
- **Higher leverage --> higher {autonomy, pay}**: Pay grade is usually bound to the level in the hierarchy, so developers have lower pay than managers. In contrast developers at Google receive [higher pay](https://www.levels.fyi/company/Google/salaries/) than non technical project managers, though there is more of a focus on strong [tech leads](https://blog.pragmaticengineer.com/project-management-at-big-tech/).


# How to do it right
In my opinion these traditional companies really need small **autonomous product teams** with strong **technical leads**. Or with more buzzwords: Self-organized cross-functional teams with a shared vision and flat hierarchies.

This actually uses developers' potential:
- Product teams know the product they are developing inside and out and can estimate cost (e.g. complexity) and see benefits (via business metrics). With this they can find better suited creative solutions to business problems, that minimize cost and maximize benefit.
- They have the necessary flexibility to quickly react to changing requirements.
- They can optimize their own development flows and collaboration with other teams, which raises raise motivation and productivity.


# Success Stories
One success story of this is detailled in [Androids](https://chethaase.medium.com/androids-765c803d5ff6) by Chet Haase. It describes the success of the team that build the Android OS. One thing that stood out to me is, that they were completely autonomous within Google (though they still had access to Google's resources). They made sure to stay free from the rest of Google (processes and company politics) and always followed their long term vision. They were even decoupled from Google's hiring processes, which would have filtered out a lot of skilled OS developers.

Autonomous teams are also the focus of the [Spotify Model](https://www.atlassian.com/agile/agile-at-scale/spotify) and a [usual approach at Big tech companies](https://blog.pragmaticengineer.com/project-management-at-big-tech/).
