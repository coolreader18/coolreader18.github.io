---
layout: post
title: WebAssembly as a Universal Executable Format
---

This is a paper I wrote for a scholarship application, and that I've rehosted
here for the benefit of those interested.

WebAssembly, or Wasm, is an executable bytecode format designed to run in the browser as a companion to JavaScript. It allows lower-level languages like C, C++, or Rust to be compiled to a ‘.wasm’ binary and run CPU-intensive operations efficiently while staying in the sandbox of a website. This essay will explore why Wasm has a bright future as an executable format not just for the web, but in many other domains including servers, user plugins, and distributed computing.

#### Why Wasm?
There have been languages and bytecodes claiming to be “write once run anywhere” before, most notably Java and its JVM. However, there are multiple drawbacks to the JVM that hindered its adoption, although it was, to be fair, very widely used from the mid 2000s to early 2010s. For one, the architecture of the JVM is very much tied to Java as a language, with its object-oriented style. The JVM, while for the most part a low-level stack machine, has a concept of classes, objects, and inheritance, due to the existence of those constructs in Java the language (JVM specification). Therefore, any languages that run on the JVM have to be object-oriented themselves, or at the very least support defining classes and creating objects in order to interoperate with the rest of the JVM. Even a language like Scala, which pushes the bounds of the JVM with its functional programming capabilities, still needs classes and OOP objects at a base level so that the JVM can understand it. In addition, both Scala (Mazur) and Kotlin (Kotlin docs), languages intended as direct alternatives to Java for similar problem domains, are actively exploring native compilation backends to allow users to avoid the JVM altogether if so desired. That may be because the JVM is fairly heavy, with a runtime including the entire Java standard library, a full threading model, operating system functions, and in most cases a profiling JIT interpreter. While these features may be desirable for an enterprise-grade server, the JVM doesn’t allow for easy sandboxing, and the decline of the JVM on desktop and the rise of browser applications has shown that some aspects of the JVM aren’t particularly endearing to users.

Wasm addresses all these concerns. It’s entirely language-agnostic, standing much closer to actual assembly than Java bytecode, and as such has better performance when it comes to raw “number-crunching” as well as being easy and fast to compile to native machine code. Its instruction set is very minimalist; most instructions are related to either control flow or arithmetic, and there’s as a small suite for reading or writing data to/from the heap and one instruction for requesting that the host extend the heap with more memory from the OS, which can fail. Any sort of allocator for managing dynamically allocated objects on the heap must be implemented in the Wasm module itself, as the Wasm “heap” (referred to as “linear memory”) is just a block of bytes that can be modified by the application (Wasm spec).

Instructions are grouped into functions, which can receive and return integer and floating point values and are in turn grouped into modules. Wasm modules are essentially just a list of definitions of functions, but also contain a section for imported and exported functions. The exports section just determines which functions are made available to the host under what name, but the imports section can bring in native functions that the host provides, giving the module access to the outside environment. A module has no capabilities except for what the host provides, meaning that entirely unknown and untrusted modules can be loaded and run with few concerns for security, so long as all the functions exposed to the module are properly locked down. However, that doesn’t mean that a Wasm module has no access to the operating system – there’s a specification called WASI (WebAssembly System Interface) that defines a set of functions a module can import that provide capability-based access to the file system, random number generators, and eventually networking sockets. Capability-based means that even if a module is run as a wasi program, it doesn’t receive access to the entire file system; rather, it’s given access to only certain directories, and can only read or modify files in those directories (WASI goals doc).

All of these features mean that Wasm is very well suited not just for expanding what’s possible in a web browser, but also for any use case that necessitates untrusted code execution. Untrusted JavaScript can’t be run unsandboxed in a web page, due to the possibility of cross-site-scripting attacks, but an untrusted Wasm module can be; there even exists a WASI implementation for the web, meaning that a program written by a user expecting to be run as a native binary can be run easily in the browser. Or, imagine a platform like BOINC or SETI@home for allowing researchers to distribute computation, but available to any research project, since data-crunching programs written in Wasm wouldn’t be able to access the outer OS or spread malware.

These are only a few possibilities of what Wasm is capable of, and I believe that Wasm truly represents a new era for the Web, and for many other domains of computing.

#### Works Cited
"The class File Format." Java Virtual Machine Specification, Oracle, docs.oracle.com/javase/specs/jvms/se7/html/jvms-4.html.

Mazur, Wojciech. "Scala Native 0.4.0 is here!." Scala Blog, 19 Jan. 2021, scala-lang.org/blog/2021/01/19/scala-native-0.4-release.html.

"Kotlin/Native overview." Kotlin Reference, Kotlin Foundation, kotlinlang.org/docs/reference/native-overview.html.

"Module memories." WebAssembly Specification, WebAssembly Working Group, webassembly.github.io/spec/core/syntax/modules.html#memories.

"WASI High-Level Goals." WASI documentation, WASI Working Group, github.com/WebAssembly/WASI/blob/main/docs/HighLevelGoals.md.
