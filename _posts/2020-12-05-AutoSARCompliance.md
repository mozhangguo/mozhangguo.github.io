---
layout: post
title: AUTOSAR Compliance in GCC
author: Mozhang Guo
categories: [Project Idea]
tags: [AUTOSAR, compiler]
---

# Introduction

Since currently, no appropriate coding standards for C++14 or C++11 exist for the use in critical and safety-related software. Existing standards are incomplete, covering old C++ verisons or not applicable for critical/safety-related. In particular, *MISRA C++: 2008* does not cover C++11/14. 

MISRA C++: 2008 is coving the C++ 03 language. However, it is too old for the newly update C++.

# Design Intent

The is the research project to research on exiting compiler plugin in current popular C++ compiler to enforce the coding standard described in the guideline (see reference 1 for more detail).

# Reference
1. [AUTOSAR Guidelines for the use of the C++14 language in critical and safety-related systems](https://www.autosar.org/fileadmin/user_upload/standards/adaptive/17-03/AUTOSAR_RS_CPP14Guidelines.pdf)