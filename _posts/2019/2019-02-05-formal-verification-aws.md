---
title: How Amazon web services uses formal methods
date: 2019-02-05
description: A paper a day keeps the doctor awake.
categories:
- a3d2kpt
tags:
- a3d2kpt
---

* [Link2Paper](https://www.cslab.pepperdine.edu/warford/math220/How-Amazon-Web-Services-Uses-Formal-Methods.pdf)
* [Author: Chris Newcombe et al.] 
* [Keywords: Formal Verification]

# Motivation

Conventional design documents consist of prose, static diagrams, and perhaps pseudo-code in an ad-hoc untestable language. Formal verification has two major benefits to writing a precise design:

1. the author is forced to think more clearly, which helps eliminate ‘plausible hand-waving’;
2. tools can be applied to check for errors in the design, even while it is being written.

The authors wanted a language that is simple to learn and apply, avoiding esoteric concepts - an off-the-shelf method with high return on investment. 

# TLA+ \[1\]

## General Info

It is used to describe both the desired correctness properties of the system (the ‘what’), and the design of the system (the ‘how’). In TLA+, correctness properties and system designs are just steps on a ladder of abstraction, with correctness properties occupying higher levels, systems designs and algorithms in the middle, and executable code and hardware at the lower levels. 

```
# Abstraction
+----------------------------+
|   Correctness Properties   |
+----------------------------+
|    System Design & Algo    |
+----------------------------+
| Executable Code & Hardware |
+----------------------------+
```

## Practical PoV

Coreectness properties of TLA+ can be easily and quickly checked by tools such as TLC model checker \[2\].

The syntax and idioms of TLA+ are somewhat unfamiliar to programmers. Fortunately, TLA+ is accompanied by a second language called PlusCal which is closer to a C-style programming language, but much more expressive as it uses TLA+ for expressions and values. However, PlusCal can be automatically translated to TLA+ with a single key press.

Refer to \[1\] for tutorials, and \[3\] for an example of a TLA+ specification from industry that is similar in size and complexity to some of the larger specifications at Amazon.

In general, we could adopt the practice of first writing a conventional prose design document, then incrementally refining parts of it into PlusCal or TLA+.

## Pros & Cons

### Pros

#### A better way of designing systems.

When using formal specification, the authors begin by precisely stating “what needs to go right?” First they state what the system should do, by defining correctness properties. These come in two varieties:

1. Safety properties: “what the system is allowed to do”

> Example: at all times, all committed data is present and correct.
> 
> Or equivalently: at no time can the system have lost or corrupted any committed data.

2. Liveness properties: “what the system must eventually do”

> Example: whenever the system receives a request, it must eventually respond to that request.

After defining correctness properties, we then precisely describe an abstract version of the design along with an abstract version of its operating environment.

#### Improved understanding, productivity and innovation

Formal specification pays several dividends over the lifetime of the system: adding features, re-design, optimization, documentation...

### Cons

We are concerned with two major classes of problems with large distributed systems: 

1. bugs and operator errors that cause a departure from the logical intent of the system, and 
2. surprising ‘sustained emergent performance degradation’ of complex systems that inevitably contain feedback loops.

Problems in the second category can cripple a system even though no logic bug is involved.

The authors also don’t yet know of a feasible way to model a real system that would enable tools to predict such emergent behavior. They use other techniques to mitigate those risks.

# Bias

> Formal verification methods are only suitable for tiny problems and give very low return on investment.

Overcoming the bias against formal methods required evidence that they work on real- world systems. This evidence was provided in a paper by Pamela Zave \[4\]

# Related Works

- TLA+ specification in the appendix of the Paxos consensus algorithm \[5\]
- Conventional informal proofs can miss very subtle problems \[6\]
- Using the TLC model checker to find ‘edge cases’ in the design on which to test the code \[7\]
- Farsite project \[8\], a complex system but different
- Abrial \[9\] lists applications to commercial safety-critical control systems, but which seem less complex 

# Ref

- \[1\] [Lamport, L. The TLA Home Page]( http://research.microsoft.com/en-us/um/people/lamport/tla/tla.html)
- \[2\] [Joshi, R., Lamport, L., et al. Checking Cache-Coherence Protocols With TLA+](http://research.microsoft.com/pubs/65162/fmsd.pdf)
- \[3\] [Lamport, L. The Wildfire Challenge Problem]( http://research.microsoft.com/en-us/um/people/lamport/tla/wildfire-challenge.html)
- \[4\] [Zave, P. Using lightweight modeling to understand Chord; In ACM SIGCOMM Computer Communication Review volume 42 number 2, April 2012]( http://www2.research.att.com/~pamela/chord.html)
- \[5\] [Lamport, L. Fast Paxos. Distributed Computing, Volume 19 Issue 2, October 2006, 79-103](http://research.microsoft.com/pubs/64624/tr- 2005-112.pdf)
- \[6\] [Lamport, L. Checking a Multithreaded Algorithm with +CAL; In Proceedings of Distributed Computing: 20th International Symposium, DISC 2006](http://research.microsoft.com/en-us/um/people/lamport/pubs/dcas.pdf)
- \[7\] [Tasiran, S., Yu, Y., Batson, B., Kreider, S. Using formal specifications to monitor and guide simulation. In Proceedings of the 3rd IEEE International Workshop on Microprocessor Test and Verification, 2002](http://research.microsoft.com/apps/pubs/default.aspx?id=65169)
- \[8\] [Bolosky, W., Douceur, J., Howell, J. The Farsite project: a retrospective](http://research.microsoft.com/apps/pubs/default.aspx?id=74211)
- \[9\] [Abrial, J. Formal Methods in Industry: Achievements, Problems, Future](http://www.irisa.fr/lande/lande/icse-proceedings/icse/p761.pdf)