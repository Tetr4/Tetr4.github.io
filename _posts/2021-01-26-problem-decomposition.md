---
layout: post
title: Problem Decomposition
categories:
    - Clean Code
description: Problem decomposition will allow you to takle any problem.
---

Software design is all about [problem decomposition](https://en.wikipedia.org/wiki/Decomposition_(computer_science)). Take a large problem, break it up into smaller more managable problems, and you can solve each one individually. This allows you to iteratively solve every problem, no matter how large, in small steps.

Problem decomposition is what we do when we:
- Design software architecture (MVVM, layers, etc.)
- Structure code units (modules, packages, classes, functions)
- Design algorithms ("divide and conquer")
- Split up tasks (features, epics, etc.)
- Design UX and UI (smaller chunks are more easy digested by the user)
- And much more…


# How to decompose a problem?

To decompose a problem, we have to answers these questions:
- What are the "breaking points" for splitting something up?
- When is a problem space small enough, so we can stop breaking it up?

As with nearly everything in software design there are no *one size fits all* answers. The [design principles]({% post_url 2020-12-17-principles %}) that help anwers these questions are these:
- [High Cohesion](https://en.wikipedia.org/wiki/Cohesion_(computer_science)): Put stuff together that belongs together. E.g. put all user management code into a `user` directory, instead of generic `viewmodel`, `ci`, `model` directories that are also used for other features ([package by feature, not type](http://www.javapractices.com/topic/TopicAction.do?Id=205)). **The structure is not right, if you have to jump a lot between files and directories while developing or maintaining a feature.**
- [Loose Coupling](https://en.wikipedia.org/wiki/Loose_coupling): Try to reduce the number of possible dependencies (a.k.a spaghetti) between modules to a minimum. E.g. reduce the number of exposed (`public`) methods and classes. If you think something could be used in another module later, assume YAGNI. You can still expose it later or refactor it to a shared module. **The structure is right, if you can just rip out or refactor a module, and don't need to extensively rewrite other modules.**
- Problem Hierarchy: When decomposing problems and subproblems, you will build a hierarchy. This should be abstract and domain specific (e.g. screen for coffee maker app) at the top and become more generic and detailled (text field components, generic data manipulation algorithms) at the bottom. **The structure is right, if it enables extensive domain specific changes, without having to rewrite implementation details.**

![Cohesion](https://upload.wikimedia.org/wikipedia/commons/thumb/0/09/CouplingVsCohesion.svg/1200px-CouplingVsCohesion.svg.png)


# Example: Stepwise Refinment
Stepwise refinement is a top down development technique for tackling a problem by dividing it into multiple smaller abstract steps. More abstract code is written first (top) and implementation details later (down), comparable to a breadth first search.

This is especially useful for functional reactive programming, because every step can be build and previewed.

1. Define method with an empty body ("stub"). The method name is an abstraction of what you want to achieve.

    ```kotlin
    fun renderScreen() {
    }
    ```

2. Fill in the method's body with methods which are not defined yet:

    ```kotlin
    fun renderScreen() = Column {
        renderHeader()
        renderBody()
    }
    ```

3. Define new methods:

    ```kotlin
    fun renderScreen() = Column {
        renderHeader()
        renderBody()
    }

    fun renderHeader() {}
    fun renderBody() {}
    ```

4. Fill in method bodies:

    ```kotlin
    fun renderScreen() = Column {
        renderHeader()
        renderBody()
    }

    fun renderHeader() = Row {
        renderIcon()
        renderTitle()
    }

    fun renderBody() = renderList()
    ```

5. Etc.

    ```kotlin
    fun renderScreen() = Column {
        renderHeader()
        renderBody()
    }

    fun renderHeader() = Row {
        renderIcon()
        renderTitle()
    }

    fun renderIcon() = Icon(Icons.logo)
    fun renderTitle() = Text("Hello World")

    fun renderBody() = renderList()
    fun renderList() = listOf("Foo", "Bar").map(renderItem)
    fun renderItem(item) = Text(item)
    ```