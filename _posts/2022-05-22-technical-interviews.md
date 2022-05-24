---
layout: post
title:  Technical Interviews
categories:
    - Tech Industry
description: Tips for conducting technical interviews.
---

Technical interviews have become kind of a meme in the developer community. Especially with Big Tech companies' extensive focus on niche algorithms and datastructures whiteboard tests, that have no relevance for most developer jobs, but are still copied by non tech companies. This post contains some tips for conducting technical interviews as a developer.

# What makes a good candidate?
A good candidate is not just someone who can code or solve algorithm puzzles. You're looking for someone who can apply these skills and **produce value** (directly or indirectly). For example by raising productivity and quality, satisfying customers, bringing in new knowledge, or generally making work pleasant for the team.

So you are looking for self motivated **problem solvers**, not [code monkeys](https://en.wikipedia.org/wiki/Code_monkey). Someone who can say "no" if there are better suited alternative solutions, that will save time and money.

At the end of your interview, you should be able to assess these points about a candidate:
- **Skills**: Do they already have the necessary experience to become productive? Better yet: Are they able to quickly acquire new skills and fill gaps? Missing skills is not a problem, if a candidate has high potential and is a quick learner.
- **Motivation**: Can they get stuff done without constant supervision? Will they ask for help if they are stuck?  Will they proactively solve problems and strive for continuous improvement?
- **Teamwork**: Can they communicate their ideas clearly and openly? Would they be a good fit for the team or project? And is the team a good fit for them?


# Conducting technical interviews

<div class="message" markdown="1">

**Note:** I have only held interviews at small and medium sized companies. Processes are usually more rigid at larger coorporations. However these tips worked well in my experience.
</div>

- Someone with a relevant technical background should be involved in the hiring process. Otherwise there is no way to assess technical skill levels. HR is easily fooled by just mentioning skills from the job description, even if you have only done "Hello World" projects.
- Remember you are talking to a human being (not a "resource"), who is in a stressful situation. Have a friendly introduction to ease the situation and so they know they are talking to a fellow developer and can get more technical. Not knowing an answer is totally fine. Don't make them feel like they gave a wrong answer.
- Get them talking about their past projects, favorite libraries, worst screwups, weirdest bugs, etc. This is something they are comfortable with, shows their motivation and problem solving skills. It also reflects real development reality and is much more effective at assessing their specific **strengths**, than asking a fixed question catalogue and hoping the questions match up with their skills.
- You don't have to talk exlusively about development topics to grasp their **motivation** and **communication** skills. You can also talk about something they are passionate about. Maybe they have some cool hobby projects. And what was their motivation to become a developer?
- Try to find the **limits** of their skills. Often there are chains of skills, that depend on each other or get more advanced with every step. For example, if they know Kotlin, ask them if and how they have used Kotlin Coroutines. If they are proficient ask them about Coroutine Flows (or RxJava). You can then talk about more advanced concepts (cold and hot streams, [unidirectional data flow](https://en.wikipedia.org/wiki/Unidirectional_Data_Flow_(computer_science)), etc.), but try not get caught up in specifics, instead keep a **broad overview** of their skillset.


# Topics for Android Developers
This is a list of topics you can talk about, that are part of typical Android development. They should give a rough indication of the skill level of an Android developer.

Most topics will be answered, by just talking about the details of candidate's past projects. With more experienced developers, you will probably be talking about more advanced topics like architectures, CI/CD and specific Android APIs.

<div class="message" markdown="1">

**Note:** These questions are not intended as a qualitative measurable checklist.
</div>

- Experience:
    - How long have you been programming Android apps?
    - Other related skills (Flutter, iOS, Web, UX/UI, PM, etc.)?
    - What projects did you work on?
        - Technologies?
        - Team sizes?
        - Responsibilities?
        - Difficulties and solutions?
        - …
- Technical knowledge:
    - Kotlin?
    - Coroutines, Flows, RxJava?
    - Databinding?
    - REST, GraphQL?
    - Bluetooth?
    - Other Android APIs?
- Libraries:
    - Networking (Retrofit, Apollo)?
    - Dependency Injection (Dagger, Koin)?
    - Jetpack Compose?
    - Other libraries?
- Architecture knowledge:
    - Architecture patterns (MVVM, MVC, MVI, …)?
    - Can you explain how a pattern works, what are the advantages, etc.?
    - Google's [recommended architecture](https://developer.android.com/topic/architecture#recommended-app-arch) (layers)?
    - Could you plan and build an app’s architecture from scratch without help?
- Workflows:
    - Git?
    - Code Reviews?
    - Git Flow?
    - CI / CD?
