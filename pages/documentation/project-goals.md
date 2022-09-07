Project Goals {#project-goals}
========================================

## Project Goals

* **Easy to build.**

    The Framework should be build-able "as is" when checked out as CMake subdirectory,
    without additional requirements and setup steps.
    Exception: heavy platform SDKs like **NDK** or **Emscripten SDK** should be configured by the user.

    The Framework should be easily forked and patched at your discretion, shall you ever need it.

* **Easy to use.**

    Using a feature in the Framework should require as little work and code as possible.
    Don't add features that require writing user code to be useful, try to make them useful "as is".

    Feature should be usable without having other feature(s) as prerequisite,
    whenever it's technically possible and doesn't require excessive effort to maintain.

    Feature should reasonably synergize with each other when it makes sense from user's perspective.

* **Easy to deploy.**

    Creating and deploying an application should require as little effort as possible.

    We are not very good at this right now, but we can aspire to get better.

* **Balance between fidelity and maintainability.**

    The Framework shouldn't grow indefinitely, "more" should not mean "better".

    Don't add new features if they are not generic enough, don't synergize well with other features,
    and/or if they can exist as third-party library.

    Don't add huge and hard-to-maintain features just because they look cool.

* **Balance between fidelity and performance.**

    Avoid wasting realtime performance, memory or build time without a good reason.

    Performance is important, but maintainability is even more important.

## Non-goals

* **Cutting-edge technology.**

    "New things" are invented much faster than we are implementing them.

    "Old things" are often "good enough" for many practical purposes.

    We don't try to be "the best of the best", we try to be "good enough".

* **Scope of features.**

    There are more features that we can possibly implement.

    Therefore, we are not trying to implement everything.

    We are trying to provide the solid foundation, the necessities and defaults,
    while the users customize the engine on demand.
