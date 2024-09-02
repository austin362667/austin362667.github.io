---
title: "Lighter and Faster: Incorporate Rust into Python"
date: 2024-08-30T16:33:12+08:00
draft: false
---

> #python  #rust  #gprc  #protobuf  #pyo3

## The Great Language Conundrum: Why Bother with a New One?

Learning a new programming language is a monumental task that requires a serious commitment of time, effort, and dedication. For many of us, Python has become the go-to language for its simplicity, flexibility, and dominance in the realms of machine learning and artificial intelligence. It's the perfect "glue" that holds our projects together, freeing us from distractions and allowing us to focus on the core business logic.

## But Then There's Rust...

Rust, on the other hand, is a systems programming language that's been gaining traction in recent years. It's a powerhouse of a language that's designed to build fast, reliable, and portable software. With its modern architecture and sleek syntax, Rust is perfect for crafting command-line tools, systems software, and other high-performance applications. But for those of us who dwell in the realms of machine learning and data pipelines, Rust may seem like a distant cousin - intriguing, yet unfamiliar. So, the question remains: what could possibly lure us away from our beloved Python and into the uncharted territory of Rust?

## Why `#RewriteInRust` Took Off?

So, what's the real story behind the surge in rewriting projects in Rust? Let's skip the usual buzzwords like memory safety, performance, and thread safety—we've all heard those before. One of the biggest reasons Rust rewrites have become so popular is the incredible community backing it. Rust has attracted a wealth of smart people, companies, and resources, creating a highly productive environment where new projects are constantly emerging.
Another reason Rust rewrites are a smart choice is the impressive return on investment (ROI). Developing in Rust is a breeze, thanks to its fantastic toolchain, which includes Cargo, rustfmt, and Clippy. Plus, the welcoming community makes it easy for beginners to dive in and start building without the headache of dealing with complicated tools like Make, CMake, Ninja, or Maven.
But here's the real game-changer: Rust's interoperability with other languages is incredibly smooth. Once you have a Rust core up and running, exposing it to Python via PyO3 is practically effortless. When choosing a new language, it’s all about what you can achieve with it. The beauty of Rust lies in its seamless integration with various languages, making it the perfect foundation for building a common Rust runtime that can support any language SDK.

## The Invisible Burden: What You Need to Know Beforehand?

Imagine you're taking a massive, complex, and super performance-critical piece of code written in Python and trying to rewrite it in Rust. If you were just switching between two garbage-collected languages, you could probably automate a good chunk of that process. But moving from a garbage-collected language like Python to a non-garbage-collected one like Rust? That's pretty much like starting from scratch.

This kind of rewrite isn't just about changing the syntax—it's about rethinking how everything works under the hood. It would take a lot of time to get it right, and validating that everything works as expected would be a huge effort. Plus, all the engineers on the project would need to get up to speed with Rust, which is no small task.

And let's not forget, while you're spending those two or three quarters making the switch, you've got to keep both versions in sync. So, while you're busy rewriting, all the ongoing feature development still needs to happen for both versions. It's a massive undertaking, and definitely not something to be taken lightly.

[TBC..]