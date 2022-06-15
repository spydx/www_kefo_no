---
layout: post
title:  "MSc: Exploring Capability-based security in software design with Rust"
date:   2022-06-01 10:00:00 +0200
tags: [projects, capabilities, rust, gnap, monogdb, confused deputy, security, oauth]
---

![Capability-based system](/assets/img/thesis/cap-system.png)

## Thesis Abstract

Access control is one of the most critical aspects of software engineering when designing secure software. In 2021, the Open Web Application Security Project (OWASP) released a new Top10 several years after its last release in 2017. Broken Access Control made a significant jump to the top of the list, marking it as the most prone and vital security aspect of software development.

Previous research shows that security challenges, such as Confused Deputy, can be solved with a capability-based approach. To achieve a capability-based system for REepresentational State Transfer (RESTful) Application Programming Interfaces(APIs), we use the Rust programming language to explore how we can create a capability design pattern. We want to create a library for the developer to harness the power of capabilities when writing the code, adhering to the capability properties and Principles of Least Privilege (PoLP), and creating a RESTful API.

![Capability-based api](/assets/img/thesis/cap-api.png)

We created a capability library we used to implement a RESTful API, simple-api, connecting it with Grant Negotiation and Authorization Protocol (GNAP) into a proof-of-concept capability-based system published on GitHub. Resulting in successfully creating capability-based access control for RESTful APIs. We show a use-case where the core access control model is Capabilities and potentially mitigates confused deputies in a RESTful API software architecture.

![Capability Library in Rust](/assets/img/thesis/lib_example.png)

## Sourcecode and thesis

In this repository, you will find all the code produced for this master thesis, also examples of what has been tried to do. Examples of the use of the created library and generated code from library usage.
Also accompanying the repository, is the thesis itself and the presentation for the defense of the thesis.

[GitHub Capability-POC Repository](https://github.com/spydx/capability-poc/)

[Thesis (complete)](https://github.com/spydx/capability-poc/blob/main/papers/exploring_capability-based_security_in_software_design_with_Rust.pdf)

[Thesis presentation](https://github.com/spydx/capability-poc/blob/main/papers/master_presentasjon.pdf)

## Bibtex

```bibtex
@software{Fossen_Exploring_Capability-based_security_2022,
    author = {Fossen, Kenneth},
    month = {6},
    title = {"Exploring Capability-based security in software design with Rust"},
    url = {https://github.com/spydx/capability-poc},
    version = {1.0.0},
    year = {2022},
}
```