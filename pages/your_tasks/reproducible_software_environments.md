---
title: Reproducible software environments
description: How to create a development environment for your software so others can run and contribute to your software?
contributors: ["Aleksandra Nenadic", "Simon Christ"]
page_id: reproducible_software_environments
related_pages:
  your_tasks: []
quality_indicators: [requirements_specified]
---


## What are reproducible software environments?

Reproducible software environments are crucial for ensuring that software behaves consistently across different systems, 
especially when it comes to research. 

Here are some popular tools and approaches for creating reproducible software environments based on their scope and usage:

- **Programming language-specific environments** - focus on managing dependencies and development/runtime environments 
for specific programming languages, ensuring consistent behaviour of software across different systems. Extremely useful when you
are developing or modifying other people's software.
- **Containerised environments** - use containers to encapsulate software and its dependencies, ensuring the software runs consistently across different systems regardless of the underlying host operating system.
  - {% tool "docker" %} - creates lightweight, isolated containers for packaging software and its dependencies.
  - {% tool "singularity" %}, {% tool "apptainer" %} - focuses on containerisation for high-performance computing (HPC) and scientific computing.
  - {% tool "docker-compose" %} - manages multi-container Docker applications, facilitating reproducible environments with multiple services.
- **System-level environments** - work at the system level, ensuring that the entire system, including the operating system and application configurations, is reproducible.
  - {% tool "vagrant" %} - creates reproducible virtualised environments using configuration scripts to define virtual machines.
  - {% tool "nixos" %} - ensures reproducible environments with declarative package management, tracking all system dependencies and configurations - 
  embodying the "operating system as code" philosophy which treats the entire operating system, including its configuration and infrastructure, as code that can be managed, versioned, and deployed like any other software application.
  - {% tool "packer" %} - automates the creation of consistent machine images, supporting multiple platforms.
- **Workflow-oriented environments** - geared toward creating reproducible environments for scientific research, bioinformatics, and complex workflows.
  - {% tool "wdl" %} - a language to define reproducible research workflows, ensuring that pipelines run consistently across systems.
  - {% tool "galaxy" %} - open-source platform for FAIR data analysis that enables users to access and collect data from reference databases, external repositories and other data sources;
    use tools from various domains

Code produced by researchers is sometimes not packaged in a library, package or container that you can readily run 
on your system. Sometimes you also may want to look at the source code and be able to make modifications. 
In these cases, you need to download the code and reproduce its programming language-specific environment in order to 
run in. 
In the rest of this document, we focus on the **programming language-specific environments** - also known as the **virtual software development environments**.

## What are virtual software development environments?

A virtual software development environment helps us create an **isolated working copy** of a software project that uses a specific 
version of a programming language interpreter/compiler (e.g. Python 3.10 or Python 3.12) together with specific versions of a number of external 
libraries (dependencies) required by our software installed into that virtual environment. 

Virtual environments are typically implemented as sub-directories within your software project with a particular structure (but note 
that some tools can place virtual environments outside your software project). 
They contain links to specified dependencies and allow for isolation from other software projects on your machine that may require 
different versions of the same programming language or external libraries.

## Why should you use virtual software development environments? 

### Description 

Software applications often rely on external libraries that you need to install and manage on your system as you develop your software. 
Software will sometimes need a specific version of an external library (e.g. because they were written to work with feature, class, or 
function that may have been updated in more recent versions), or a specific version of the program language interpreter/compiler 
(e.g. consider legacy software requiring Python 2 vs. new applications written in Python 3). 

This means that each software application you develop on your machine may require a different setup and a set of dependencies so it is useful to be 
able to keep these configurations separate to avoid confusion between projects. 
The solution for this problem is to create a self-contained virtual environment per project, which contains a particular version of your 
programming language interpreter/compiler plus a number of additional external libraries.

Another big motivator for using virtual environments is that they make **sharing your code with others** (users or developers) much easier.
By sharing a description of your virtual development environment you enable others to quickly replicate the same environment 
on their machines and run or further develop your software - making your work **portable**, **reusable** and more **reproducible**.

### Considerations

- As more external libraries are added to your software project over time, you can add them to its specific virtual environment
and avoid a great deal of confusion by having separate (smaller) virtual environments for each project
rather than one huge global environment on your machine with potential package version clashes.
- You have an older project that only works under, e.g., Python 2. You do not have the time to migrate the project to Python 3
or it may not even be possible as some of the third party dependencies are not available under Python 3.
You have to start another project under Python 3. The best way to do this on a single machine is
to set up two separate Python virtual environments.
- One of your Python 3 projects is locked to use a particular older version of a third party dependency.
You cannot use the latest version of the dependency as it breaks things in your project.
In a separate branch of your project, you want to try and fix problems introduced by the new version of the dependency
without affecting the working version of your project. You need to set up a separate virtual environment for your branch to
'isolate' your code while testing the new feature.
- You do not have to worry too much about specific versions of external libraries that your project depends on most of the time.
Virtual environments enable you to always use the latest available version without specifying it explicitly.
They also enable you to use a specific older version of a package for your project, should you need to. 

### Solutions

- Make your research software reusable and your research that relies on that software reproducible by setting up and sharing its virtual development environment.

## How do you create virtual software development environments? 

### Description

Most modern programming languages use some kind of virtual environments or a similar mechanism to isolate libraries or dependencies for a specific project, 
making it easier to develop, run, test and share code with others. 

Part of managing a virtual software development environment involves installing, updating and removing external packages on your system. 
You would need a package manager tool for your programming language to be able to do that - this is typically a command line tool that you invoke from 
a command line terminal. 
In addition to a package manager, you will need another command line tool to create and manage virtual environments on your machine. 
Sometimes, a package manager combines both of these functionalities and you only need to install one extra tool on your system.

### Considerations

- There are often multiple package and environment management tools even for a single programming language:
  - For example, commonly used tools for managing Python packages and virtual environments are {% tool "pip" %} (Python package manager tool which interacts and obtains the packages
  from the central repository called {% tool "pypi" %}) and {% tool "venv" %} (Python virtual environment manager tool available by default from the standard Python distribution from Python 3.3).
  One alternative is to use {% tool "poetry" %} - a modern Python packaging tool which also installs Python packages from PyPI and handles virtual environments automatically. Also check {% tool "uv" %} - a single and fast Python package and project manager, built to replace {% tool "pip" %} and {% tool "venv" %}.
  - If your Python code relies on non-Python packages, for instance when some C++ libraries must also be installed and you want to support multiple platforms, a better choice may be {% tool "conda" %} -
  a Python package and environment management system part of the Anaconda Python distribution (often used by the scientific community). {% tool "conda" %} has its own repository system separate from
  (but compatible with) PyPI that distributes non-Python packages packages as well and has its own non-venv-based virtual environment system.
  - If you are using R - consider {% tool "renv" %} that will help you build reproducible environments for your R projects
  - For Julia programming language - check {% tool "pkg-jl" %}; for C++ - check {% tool "conan" %}, for Java - check {% tool "maven" %}, for Ruby - check {% tool "bundler" %}.
  - There are some some generic tools to have a look at as well - e.g. [Spack][spack], {% tool "nixos" %}, [guix][guix].
- You need to decide what tools are best for you - based on your personal preferences, or what the software project and your team or community is
already using (so you can get help when you need it). Not using virtual environments at all and mixing different tools to manage them could lead to
a [bad example of a spaghetti setup][python-env-hell], not knowing which dependencies are being used and causing issues when running and debugging code.

### Solutions

* Decide on and start using a package manager tool and a virtual environment management tool for your programming language.


[pip-venv]: https://packaging.python.org/en/latest/guides/installing-using-pip-and-virtual-environments/
[fair-rs]: https://carpentries-incubator.github.io/fair-research-software
[intermediate-rs-dev]: https://carpentries-incubator.github.io/python-intermediate-development/
[renv]: https://rstudio.github.io/renv/index.html
[ssi]: https://www.software.ac.uk/
[python-env-hell]: https://xkcd.com/1987/
[guix]: https://hpc.guix.info/
[spack]: https://spack.io/
