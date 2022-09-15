---
title: A Formal Verified NAT
date: 2019-02-10
description: A paper a day keeps the doctor awake.
categories:
- a3d2kpt
tags:
- a3d2kpt
photos:
- /assets/images/X/PhD/a3d2kpt/formal-nat/proof.png
---

* [Link2Paper](https://infoscience.epfl.ch/record/231983/files/vignat-sigcomm17.pdf)
* [Author: A Zaostrovnykh et al.] 
* [Keywords: Formal Verification, NAT, Network Function (NF)]

# Background

## Research Area

This paper works in the general area of “data-plane verification.” There are basically two approaches:

1. "Network verification" （宏观）: treat the combination of the configured data planes of network devices in a network as one big data plane, and reason about network properties (reachability, loops, etc.) 
2. "NF verification" （微观）: reason about properties of the data-plane software running on individual devices, and reasons about software properties (crash freedom, bounded execution time, memory safety, etc.)

## Motivation

There is a rich body of work on network verification [24, 25, 30– 32, 38, 39, 46, 52, 55, 59]. In contrast, there is much less work on NF verification [19].

# Contribution

<p align="center">
  <img src="/assets/images/X/PhD/a3d2kpt/formal-nat/nat.png" alt="NAT">
</p>

This work belongs to the category of NF verification and aims to improve the state of the art on two fronts: (1) verify high-level semantic properties, such as the correct implementation of an RFC, and (2) verify NFs that are stateful.

<p align="center">
  <img src="/assets/images/X/PhD/a3d2kpt/formal-nat/proof.png" alt="NAT">
</p>

Workflow:
1. Split the NAT NF into stateless and stateful (contained in the libVig library) part
2. Use formal thorem proving to verify P3
3. Use exhaustive symbolic execution (ESE) w/ a modified version of KLEE to explore all paths in the stateless part (using symbolic models of the data structures) and verify P2
4. Prove P4 and P5 (which are the assumption of previous step) with Vigor Validator and the VeriFast proof checker
5. Use the same combination of tools to prove P1

# Requirement

The trusted computing base for a Vigor-verified NF consists of the Vigor toolchain (the Clang LLVM compiler, VeriFast, KLEE, and our own Validator) and the environment in which the NF runs (DPDK, device drivers, OS kernel, BIOS, and hardware). It's assumed that the compiler implements the same language semantics employed by Vigor (e.g., same byte length for C primitive types).

# Discussion

## Limitations

1. Vigor will not produce an incorrect proof, but it may fail to prove a property that actually holds for a given NF, because of an invalid model;
2. The current version of Vigor cannot verify concurrent code;
3. They do not have yet experience with applying Vigor to mature, legacy code.

## Related work

### Network function models effectively reason about network configuration

A bunch of listed papers on the top right paragraph in the first page. Reference in the original paper [24, 25, 30–32, 38, 39, 46, 52, 55, 59].

### NF verified written in Click [[2]](#ref2)

Dobrescu, M., and Argyraki, K. Software Dataplane Verification. In Symp. on
Networked Systems Design and Implem. (2014) [[1]](#ref1)

# Ref

- \[1\] [Software dataplane verification](https://www.usenix.org/system/files/conference/nsdi14/nsdi14-paper-dobrescu.pdf)<a name="ref1"></a>
- \[2\] [The Click modular router](https://dspace.mit.edu/bitstream/handle/1721.1/86585/48118278-MIT.pdf?sequence=2)<a name="ref2"></a>