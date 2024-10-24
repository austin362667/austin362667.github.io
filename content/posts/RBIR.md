---
title: "Lighter and Faster: Incorporate Rust into Python"
date: 2024-08-30T16:33:12+08:00
draft: false
---

> #python  #rust  #gprc  #protobuf  #pyo3

## Introduction to Flyte & Flytekit

Flyte is a scalable and flexible cloud native workflow orchestration platform designed to seamlessly schedule and execute machine learning or data analytics tasks remotely. Its core is written in Golang and runs on Kubernetes via an operator called Flyte Propeller. For local development, users typically write tasks in Python using Flytekit (the Python SDK) and register them with the control plane through gRPC communication.

## Our Problem with `grpcio`


The Flytekit remote client relies on gRPC for communication with the control plane (FlyteAdmin). However, our experience has revealed numerous issues within Python gRPC, leading to numerous buggy issues. Additionally, users frequently encounter challenges when working with specific versions of gRPC.

In order to eliminate the gRPC Python dependency from flytekit, our goal is to develop a Rust crate to serve as a replacement for the flytekit remote client. To achieve this, we plan to utilize PyO3 to generate a Python binding, allowing flytekit to seamlessly integrate and utilize the new implementation.


## Alternatives We’ve Considered

Since optimized Python packages are not our main goal. Compiled extension modules in Python enhance execution speed. But, where do Python's performance constraints come from? First, it's an interpreted language. Second, it accepts dynamic typing. Third, it has a Global Interpreter Lock (GIL), meaning only one Python thread can run on a single machine core within its interpreter state.

In order to address the objectives of improving Python's performance constraints and removing Python's gRPC dependency, other existing solutions include the following:
From the performance perspective,
1. PyPy (JIT optimization)
2. Cython, Mypyc (Compiled “Python”)
3. C / C++ Extension Modules
From the dependencies perspective,
1. C / C++ Extension Modules
In comparison, our Rust approach is more like a wrapper of “C / C++ Extension Modules” on the two extremes of the spectrum.

Choose Rust instead of tiresome C / C++, Generally speaking, the PyO3 API approach is a much more exclusive experience that builds to work for Python. Creating low-level C FFI binding with the C API is common and more general to expose functions to other languages than Python like Javascript, Ruby or something else but requires careful management of memory and reference counts, which can be difficult for non-trivial programs.

When comparing their relative strengths and weaknesses from a performance, safety, and maintainability perspective, some of PyO3 APIs are still quite unstable, and Rust is indeed a relatively new language. However in practice, without the need to manually deallocate memory, PyO3 becomes a more feasible solution that helps manage GIL and reference counts for you.


## The Great Language Conundrum: Why Bother with a New One?

Learning a new programming language is a monumental task that requires a serious commitment of time, effort, and dedication. For many of us, Python has become the go-to language for its simplicity, flexibility, and dominance in the realms of machine learning and artificial intelligence. It's the perfect "glue" that holds our projects together, freeing us from distractions and allowing us to focus on the core business logic.


## But Then There's Rust...

Rust, on the other hand, is a systems programming language that's been gaining traction in recent years. It's a powerhouse of a language that's designed to build fast, reliable, and portable software. With its modern architecture and sleek syntax, Rust is perfect for crafting command-line tools, systems software, and other high-performance applications. But for those of us who dwell in the realms of machine learning and data pipelines, Rust may seem like a distant cousin - intriguing, yet unfamiliar. So, the question remains: what could possibly lure us away from our beloved Python and into the uncharted territory of Rust?


## Why #RewriteInRust Took Off?

So, what's the real story behind the surge in rewriting projects in Rust? Let's skip the usual buzzwords like memory safety, performance, and thread safety—we've all heard those before. One of the biggest reasons Rust rewrites have become so popular is the incredible community backing it. Rust has attracted a wealth of smart people, companies, and resources, creating a highly productive environment where new projects are constantly emerging.
Another reason Rust rewrites are a smart choice is the impressive return on investment (ROI). Developing in Rust is a breeze, thanks to its fantastic toolchain, which includes Cargo, rustfmt, and Clippy. Plus, the welcoming community makes it easy for beginners to dive in and start building without the headache of dealing with complicated tools like Make, CMake, Ninja, or Maven.
But here's the real game-changer: Rust's interoperability with other languages is incredibly smooth. Once you have a Rust core up and running, exposing it to Python via PyO3 is practically almost effortless. When choosing a new language, it’s all about what you can achieve with it. The beauty of Rust lies in its seamless integration with various languages, making it the perfect foundation for building a common Rust runtime that can support any language SDK.


## Glossary

Heads up! Before we dive right in, let's make sure we're all on the same page. There might be some words you're not familiar with, so let's clear those up first. Or you can check them later.
Flyteidl is the protobuf message definition which Flyte has.
gRPC can utilize Protocol Buffers as both the Interface Definition Language (IDL) and the underlying message exchange format on both ends.
PyO3 is an actively maintained Rust crate for binding Python, facilitating the creation of native Python extension modules from Rust.
Tonic is a gRPC client & server implementation in Rust with async/await support. It consists of a generic gRPC implementation, a high-performance HTTP/2 implementation, and a codegen tool powered by Prost.

To sum up, our goal is to remove dependency on gRPC in Python and solely replace it with Rust. So we use Rust structs generated from the protobuf compiler, and directly create a python binding for these structs then exposing them to Flytekit used by the underlying remote client. So we need to propose a way that generalizes the process of building a python package from a protobuf-defined rust crate.


What’s PyO3 capable of?
[TODO] Some demo code with PyO3

## Compile Well-Known Types and Avoiding Orphan Rules

When working with Rust and PyO3, we often use the #[pyclass] attribute macro to implement the PyClass trait, allowing Rust structures to interoperate with Python. However, implementing PyClass for structures from external crates like prost_types can trigger the orphan rule, which prevents adding traits to types you don't own. This creates challenges when trying to integrate well-known Protobuf types (WKTs) with PyO3.

To bypass this, builders which wrap protoc like tonic builder offer the option to "compile well-known types," which generates WKTs as Rust primitive types within your crate, avoiding the orphan rule. This approach is especially useful for handling nested structures, as it eliminates the need for cumbersome local wrappers around external types. Wrapping each external type manually, while a possible solution, becomes impractical in large gRPC clients with deep nesting, where every layer would require its own PyClass implementation. Compiling WKTs directly is a cleaner, more efficient solution.


## Seriously! What’s Orphan Rule By The Way?

Imagine you have a house with chairs inside. In Rust, the orphan rule says you can only sit in chairs that either belong to you or are already in your house. Here's what that means:

If you own the chair (like a type you made), you can sit down (add traits/behaviors) as much as you want in your house (your local crate).
If the chair already belongs in your house (like a built-in type like String or a type you created), you can sit there too.
But you can’t bring your neighbor's chair (a type from someone else’s crate or library) and sit on it in your house unless you also own the house (the local crate you’re editing).

So in Rust terms:

You can’t mix a chair from outside (other people's types) and try to sit on it in your own house (add your own traits/behaviors in your own local crate), because that could cause confusion.
The orphan rule keeps things organized, just like you can't mix and match chairs from neighbors without permission!

- [TODO] References Outlive Issue
    - While this PR supports Cell<T>, but doesn't support Box<T>, it also highlights the difficulties of supporting Box<T>.
    - The PyO3 roadmap on addressing these issue seems like this way.
- [TODO] Rust Interface: Types implementing this trait can be used as arguments to client RPC
- [TODO] The drawbacks with nascent packages: 
    - Tuple Variants in Complex Enum: PyO3 supports tuple enum variants after version v0.22.0
    - Python submodules: Use latest feature Declarative modules introduced in version v0.22.0.

## The Invisible Burden: What You Need to Know Beforehand?

Imagine you're taking a massive, complex, and super performance-critical piece of code written in Python and trying to rewrite it in Rust. If you were just switching between two garbage-collected languages, you could probably automate a good chunk of that process. But moving from a garbage-collected language like Python to a non-garbage-collected one like Rust? That's pretty much like starting from scratch.

This kind of rewrite isn't just about changing the syntax—it's about rethinking how everything works under the hood. It would take a lot of time to get it right, and validating that everything works as expected would be a huge effort. Plus, all the engineers on the project would need to get up to speed with Rust, which is no small task.

And let's not forget, while you're spending those two or three quarters making the switch, you've got to keep both versions in sync. So, while you're busy rewriting, all the ongoing feature development still needs to happen for both versions. It's a massive undertaking, and definitely not something to be taken lightly.


[TBC..]