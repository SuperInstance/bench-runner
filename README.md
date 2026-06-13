# Bench Runner

**A Rust library for running and collecting benchmark results** — a scaffold for a benchmark execution harness that times code under controlled conditions.

## Why It Matters

Benchmarking is essential for performance engineering. Unlike tests (which check correctness), benchmarks measure speed. Good benchmark infrastructure must:

- **Reduce noise** — warm up caches, pin CPU frequency, isolate from other processes
- **Collect statistics** — mean, median, p99, standard deviation across runs
- **Compare baselines** — detect regressions between code changes
- **Report deterministically** — format results for CI/CD consumption

The Rust ecosystem's `criterion` crate is the gold standard, but many use cases need a lighter-weight, customizable harness — for CI pipelines, embedded targets, or custom metric collection.

## How It Works

This crate is currently in scaffold phase with a placeholder entry point. The planned design includes:

- **Benchmark registration** — declarative `#[bench]` attributes or explicit registration
- **Warmup phase** — run the function N times to stabilize CPU caches and branch predictors
- **Measurement phase** — time M iterations using high-resolution `std::time::Instant`
- **Statistical analysis** — compute mean, min, median, p99, and confidence intervals
- **Comparison mode** — diff current results against a saved baseline

## Quick Start

```rust
// This crate is in scaffold phase.
// Planned API:
//
// let runner = BenchRunner::new();
// runner.bench("vec_push", || {
//     let mut v = Vec::new();
//     for i in 0..1000 { v.push(i); }
// });
// runner.report();
```

## API

*In development.* The crate is currently a scaffold awaiting implementation of the benchmark execution engine.

## Architecture Notes

Part of the SuperInstance performance toolchain. When complete, it will integrate with CI to detect performance regressions across releases and feature flags. See the [architecture overview](https://github.com/SuperInstance/SuperInstance/blob/main/ARCHITECTURE.md).

## License

MIT
