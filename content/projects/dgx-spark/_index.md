---
title: "DGX Spark Deep Dive"
date: 2025-11-08T10:00:00-00:00
description: "Debugging container memory overhead on NVIDIA DGX Spark with Grace Hopper"
series: ["dgx-spark"]
categories: ["Investigation", "Performance Engineering"]
tags: ["dgx-spark", "grace-hopper", "docker", "unified-memory", "performance"]
weight: 1
---

A 6-part series investigating why Docker containers use 20-30GB more memory than native execution on Grace Hopper's unified memory architecture.

## Overview

When YouTube reviewers complained about DGX Spark performance, they blamed the hardware. I dug deeper and found the real culprit: Docker's cgroups double-counting unified memory.

## Key Findings

- **Container overhead**: 20-30 GB more memory usage in Docker
- **KV cache reduction**: 40-63% less KV cache in containers (1.7-2.7x reduction)
- **Performance**: Identical throughput - no speed penalty
- **Root cause**: Docker's cgroups double-count unified memory on Grace Hopper
- **Solution**: Use native execution for large models on unified memory architectures

## The Series

1. **[The Mystery](01-the-mystery/)** - YouTubers blamed NVIDIA hardware without technical analysis
2. **[MPI and Chroot Nightmare](02-mpi-chroot-nightmare/)** - Setting up proper test environments
3. **[The Unified Memory Revelation](03-unified-memory-revelation/)** - Why Docker's cgroups double-count unified memory
4. **[The Data: 60 Runs Don't Lie](04-the-data/)** - Comprehensive benchmark results across 3 models
5. **[What I Learned (And What's Next)](05-what-we-learned/)** - Conclusions and Phase 2 preview
6. **[KV Cache Deep Dive](06-kv-cache-deep-dive/)** - The 2x reduction mystery explained

## Related Resources

- **Benchmark Code & Data**: [benchmark-spark](https://github.com/brandonrc/benchmark-spark)
- **Interactive Results**: [brandonrc.github.io/benchmark-spark](https://brandonrc.github.io/benchmark-spark/)

## Impact

This investigation demonstrates the importance of understanding the full stack - from hardware architecture to kernel subsystems to container runtimes. What looked like a hardware problem was actually a software architecture mismatch.
