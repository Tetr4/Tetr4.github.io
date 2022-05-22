---
layout: post
title: Design Principles
categories:
    - Software Design
description: A concise overview of software design principles.
---

These principles are not strict rules and open to interpretation. There is no correct way to write clean code, as it requires a lot of creativity, but you can use these principles as inspiration:


<details markdown="1">
<summary>Keep It Simple and Stupid (KISS)</summary>

- Less is more. If something is complex it will likely break. So complete a feature with minimum amount of necessary code, that is still perfectly understandable. Try to find the "cleanest" solution.
- Development flow: Make it work, then iteratively refactor to make it more simple/readable. Maybe there is a function in the standard library you can use instead, or you can simplify a layout, or some duplicate code can be extracted. Before submitting a merge request, check if you can simplify your changes even further.

This reduces the overall error surface (less code → less potential bugs) and reduces the maintainable surface (less code to touch when refactoring → more flexible).
</details>


<details markdown="1">
<summary>Single Responsibility</summary>

- Methods should only do one thing (e.g. display, calculate, create, combine, save, get, set, enable, disable, fetch, query, show, hide, etc.) which is reflected in the method name. If there is an "and" in the name, you can probably split it. 
- Rule of thumb: Methods should be roughly less than ~20 lines and fit on page without scrolling. If it is too long, then this is an indicator, that it is doing too much and should be split up. **This is of course only a rough guideline and not always applicable, so don't enforce it!**
- Methods should be roughly on the same layer of abstraction. E.g. if you have function `renderList`, then put the code to render items into a separate `renderItem` function, instead of adding this code to the `renderList` function.
- Classes should only be responsible for a single feature. E.g. try to prevent an abstract base class, where every shared code is dumped, instead pull this out into extension functions or components.
- Don't mix business logic details (e.g. SQL database queries) with presentation details (direct view manipulation, animations), instead use a mediator (controller, viewmodel, component, etc.) between classes that manipulate views and classes that handle data sources.
</details>


<details markdown="1">
<summary>Loose Coupling</summary>

- Relevant principles: Information Hiding, Separation of Concerns, Demeter Law, Interface Segregation, Dependency Inversion
- Reduce scope (references to other classes, member variables, parameters, etc.), e.g. by hiding implementation details behind private fields and methods or using pure functions. **Pure functions** have no side effects or state (no reference to members) and always give the same output for the same input (testable).
- A classes' or function's dependencies should be obvious, when looking at its signature (function parameters, constructor parameters). **Never use global state and singletons**, as this will hide the fact that a class or function depends on another component, without looking at its implementation. Global state will make it really hard to follow the code, as you have to keep in mind every single place it may be modified and where it is used. Global state also makes code nearly untestable, as dependencies can't be replaced easily.
- Parameters should not contain more information than a function or class actually needs I.e. don't pass around a massive context object or the whole viewmodel, if just a callback would suffice.

Loose coupling makes it easier to create a mental model and follow the flow of the code. The code is easier to test and verify, when there are less influencing external factors and we can be more confident, that the code is correct.
</details>


<details markdown="1">
<summary>Principle of least Surprise (POLS)</summary>

- A unit's (methods, class) name is a mental abstraction for its functionality. So reading the name (or signature) should be enough information to use it, without unexpected behavior. When encountering unexpected behavior, we have to look at the implementation details, which consumes time and reduces our trust in the code. Good code looks **boring** as it contains no such surprises.
- If something is not obvious (["black magic"](https://en.wikipedia.org/wiki/Magic_(programming)), workarounds, bugs in frameworks, deviation from best practices, etc.) or may lead to issues in the future, mark it as a place that needs special attention. E.g. by adding a comment or pulling it into a separate function, where the method names describes the behavior. This prevents surprises, communication overhead and regression, when other developers touch this piece of code.
- Prefer appropriately named methods to comments (**self documenting code**, i.e. code is the ground truth) and don't add comments to obvious code. Comments increase the maintenance surface and can be misleading (compiler can't check comments for correctness, comments are usually never updated). Comments reduce readability (code does not fit on single page, reading flow is broken).
</details>


<details markdown="1">
<summary>Don't repeat yourself (DRY)</summary>

- Ritualistic copy-pasting code is easy, but it is likely a sign of [cargo cult programming](https://en.wikipedia.org/wiki/Cargo_cult_programming), i.e. not understanding the reason behind the code. Make sure you know how the code works, maybe there is a better way to do this.
- Copy-pasting code very often leads to copy-paste errors (e.g. something is not renamed). Once there is a change/refactoring to the copied code, the pasted code usually needs to be manually updated in all place (no single source of truth, large maintenance surface), which is easily forgotten. This frequently leads to bugs, that you thought were already fixed.
- Instead move duplicated logic to helper functions, e.g. an extension function for creating a toast message, so the core logic is in a single place (single source of truth) and can be easily changed.
- In general try to keep the signal to noise (business logic code vs. glue code) ration high.
- But: Don't over-engineer, which may obstruct further development and confuse new developers. Keep it easy to adapt and extend. Sometimes it is better to be flexible, e.g. copy an \<ImageView> instead of moving it to a shared \<include> layout, if you know the design for it will be different every time.
</details>


<details markdown="1">
<summary>You aren't gonna need it (YAGNI)</summary>

- **No dead or commented out code.** This increases maintenance surface or even worse is not maintained at all (not type checked by the compiler). If this is really needed, then only with an explanatory comment why this is still there or when it can be removed, e.g. if dependent on a feature that is developed in parallel. Otherwise this will be forever in the codebase, and other developers will not know if this is still needed or can be safely removed.
- Don't wrap every class in an explicit interface class. Classes already provide an interface via their public properties and methods (loose coupling). Interfaces reduce development velocity, because now there are two places (interface and implementation) that need to be changed when adding functionality. Interfaces make it hard to follow the code without actually running/debugging the code. Usually it is easy to add an interface later once it is needed, e.g. if there is actually more than one implementation. However for libraries or public APIs an interface adds an additional layer of separation.
- Don't over-engineer and keep your code flexible.
- But: Some things are not initially planned, but are guaranteed to be needed later in nearly ever project, like error handling or localization. 
</details>


<details markdown="1">
<summary>Composition over Inheritance</summary>

- Don't use abstract "base" classes, which grow huge and can't be split up. They also don't work with multiple inheritance, e.g. when a framework provides its own base class we have to use. Instead put functionality in pure (extension) functions, or delegate it to separate components, which can be injected where needed.
- Compose your data structures out of other data structures. This simplifies integration with other formats that do not have inheritance, like JSON or relational databases.
</details>


<details markdown="1">
<summary>Convention over Configuration</summary>

- **Follow the best practices** and don't fight the system. Usually there is a cleaner and better solution in the official documentation than in StackOverflow code snippets. E.g. for Android it is often suggested to use a custom background to change a button's color, but this breaks animations unlike the best practice (theming).
- Use the recommended code style for the project's platform and enforce it via CI and code reviews (even if it seems pedantic). This makes the code more recognizable, it is easier to read and change other developer's code, and there are less conflicts when autoformatting code.
- A developer should be able to **build a project immediately after a checkout** with as little effort or help as possible. This should require no complex build process (KISS) and should follow platform convention, e.g. `gradle assembleDebug` for Android or `docker-compose up` for backends. Defaults should be provided if possible, so there is no need to configure urls, accounts, keys, etc. If a defaults can not be provided, consider disabling a feature, e.g. use a debug key for signing or providing a template config file, which can be filled in. 
- Deployment should be as easy as possible (KISS) for every developer via CI/CD and should only take a few minutes, e.g. merging `develop` on `master`. Deployment should not be done locally (e.g. deploying a partial development state).
- Keep the CI/CD config simple (KISS) but effective, so there is less time needed for configuring or debugging the CI and more time for coding. If something is too complex it will likely break, so simplify it or make it easy for a developer to do manually. CI should include at least an automatic build (smoke test). Better yet a code quality check (linter), running tests (even if there are no tests written yet) and deployment.
- Technical documentation (README.md) should provide some of the following:
    - What is this project/codebase about?
    - Special requirements / tools
    - How to build
    - How to deploy / create release
    - Some helpful guides / commands (e.g. architecture/structure, how to update translations, how to make migration, how to fix common issues, URLs for services, etc.)
</details>


<details markdown="1">
<summary>Test-driven development (TDD)</summary>

- TDD is a development technique that also produces a test suite that has high test coverage and protects against future regressions, however TDD is not universally applicable to every task.
- Basic development workflow:
    1. Write test for a tiny part of new feature
    2. Write code until test works
    3. Refactor / clean up code
    4. Make sure tests still works (no regression)
    5. Repeat until feature is done
- Only one thing should be tested per test. For this you can structure tests into three parts, which also helps other developers understand and modify your tests:
    1. **Given**: Setup the precondition state, e.g. create the class you want to test and write some  dummy data into it's fields.
    2. **When**: The action you want to test, e.g. call a method on your class.
    3. **Then**: The expected postcondition, e.g. certain fields in your class should have new values.
- What should (not) be tested?
    - No 100% coverage: Tests increase the maintainable surface of the codebase and reduce development velocity, as they need to be rewritten, when a feature changes or is refactored. So the goal should be not to test everything, but to reduce complexity, so **less tests are actually needed**.
    - Most code units should be clean and self evident with high confidence, so tests would be redundant. Test only where it makes sense. E.g. don't test getters/setter or a function that contains only a simple if/else control flow.
    - Good candidates for testing and TDD are complex pure functions (e.g. parsers or date time calculation), code that needs high confidence (e.g. payment processing), or code that can not be easily tested manually (rare crash conditions and edge cases).
    - Don't test implementation details of external dependencies, e.g. if a parser generated by a code generator can parse every datatype correctly. For critical dependencies a boundary or API test should suffice, so you know early on if it is safe to upgrade the library to a new version.
    - Tests may also contain bugs, so a test should usually not be more complex than the code it tests.
    - UI tests (e.g. on an emulator or browser) are slow and fragile, so prefer unit tests. Viewmodels should act as [humble objects](https://martinfowler.com/bliki/HumbleObject.html).
    - Tests can't find high level gaps in specification or overall behavior, so explorative QA testing is still needed.
- If projects don't have tests (or support for tests) from the start, they likely won't have tests later. Usually there is considerable effort involved in starting to write tests, e.g. mocking dependencies, separating framework from test code, etc. To allow easy testing without obstructions or effort there should be at least one moderately complex test case when starting a new project, that can be used as a template, even if there are no other tests planned yet.
</details>


<details markdown="1">
<summary>Graceful Degradation</summary>

- There are two kinds of errors:
    - If an error would inevitably leave your program in an illegal or broken state that would cause other issue (e.g. data corruption), then it is **fatal** and should crash the program.
    - If you can recover from the error, so your program always remains in a valid state, then it is an **exception** and you should handle it. E.g. you could show a message to your users and let them try again after a failed backend request. Your program's state must always stay valid, e.g. when using optimistic request (showing result before a request is finished), then the previous state (before result was shown) must be restored.
- **Never silently suppress exceptions** (empty catch block). Swallowed exceptions make it very hard to find the source of bugs and may introduce bugs that could easily be discovered early during development. Instead at least log it and/or pass it to the user as a generic error (in a server return status 500). In rare cases where a specific type of exception is expected and can safely be ignored, add a comment, so it is clear why there is no logging.
</details>
