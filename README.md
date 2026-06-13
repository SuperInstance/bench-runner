# Bench Runner

**Bench Runner** is a Rust library providing the execution harness for running fleet-wide benchmarks — wrapping individual benchmark functions with timing, iteration control, warm-up phases, and result collection for the SuperInstance performance monitoring pipeline.

## Why It Matters

Benchmarking infrastructure must be consistent across an organization: the same timing method, the same warm-up protocol, the same statistical treatment of results. When different teams use ad-hoc timing (different clocks, different iteration counts, different warm-up strategies), cross-team performance comparisons become meaningless. Bench Runner standardizes the execution layer: every benchmark in the fleet uses the same high-resolution timer, the same configurable iteration count, and the same warm-up discard logic. This produces comparable, trustworthy performance data that feeds into the bench-compare regression detection pipeline.

## How It Works

**Execution model:**
```
run_benchmark(name, f):
  // Phase 1: Warm-up (discard results)
  for _ in 0..warmup_iterations:
    f()

  // Phase 2: Measurement
  samples = []
  for _ in 0..measurement_iterations:
    t0 = Instant::now()
    f()
    t1 = Instant::now()
    samples.push(t1 - t0)

  return BenchmarkResult(name, samples)
```

**Timer selection:** Uses `std::time::Instant` which provides monotonic nanosecond-resolution timing on Linux (via `clock_gettime(CLOCK_MONOTONIC)`). This avoids wall-clock issues (NTP adjustments, timezone changes) and provides consistent measurements across runs.

**Warm-up rationale:** The first N iterations of any benchmark are slower due to:
- CPU cache warming (data and instruction caches)
- Branch predictor training
- JIT compilation (in runtimes that use it)
- Memory allocator warm-up

Discarding warm-up iterations (typically 10–100) ensures measurements reflect steady-state performance. Without warm-up, benchmarks can show 2–5× slower results than steady state.

**Statistical summary:** Each run produces mean, median, standard deviation, min, max, and percentile distribution from the raw samples. The median is preferred over mean for reporting because it is robust to outlier spikes from OS scheduling interruptions.

## Quick Start

```rust
fn main() {
    println!("Bench runner ready.");
    // Standard fleet benchmark protocol:
    // 1. Define benchmark function
    // 2. Run with 100 warm-up + 1000 measurement iterations
    // 3. Collect BenchmarkResult
    // 4. Feed to bench-compare for regression analysis
}
```

## API

| Component | Description |
|-----------|-------------|
| Runner config | Warm-up iterations, measurement iterations |
| Timer | Monotonic nanosecond timing |
| Result collector | Mean, median, stddev, percentiles |
| Report | JSON and Markdown output formats |

## Architecture Notes

Bench Runner provides the **timing infrastructure** for γ + η = C conservation verification. The conservation-law computations are time-bounded — they must complete within a single agent tick. Bench Runner measures whether this deadline is met consistently, flagging regressions that could cause conservation-law violations due to late or missed evaluations.

See [ARCHITECTURE.md](https://github.com/SuperInstance/SuperInstance/blob/main/ARCHITECTURE.md).

## References

1. Kalibera, T. & Jones, R. (2013). "Rigorous Benchmarking in Reasonable Time." *ISSTA*.
2. Mytkowicz, T. et al. (2009). "Producing Wrong Data Without Doing Anything Obviously Wrong!" *ASPLOS*.

## License

MIT
