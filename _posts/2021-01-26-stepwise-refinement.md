---
layout: post
title: Stepwise Refinement
categories:
    - Clean Code
description: asdf
---

Stepwise refinement is a top down development technique for tackling a problem by dividing it into multiple smaller abstract steps, i.e. divide and conquer. More abstract code is written first (top) and implementation details later (down), comparable to a breadth first search.

This is especially useful for functional reactive programming, because every step can be build and preview.

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