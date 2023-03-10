# Benchmarks

## System health

Below are some examples for running the system health bench

- `memory_desktop`
   - `tools/perf/run_benchmark system_health.memory_desktop --browser debug --story-filter load:games:miniclip:2018 --reset-results`
- `common_desktop`
   - `tools/perf/run_benchmark system_health.common_desktop --browser debug --story-filter load:news:reddit:2018 --reset-results`

You typically want to only add the `--reset-results` flag when you start a new
_series_ of runs that you'd like to compare side-by-side on the results page
that is emitted after the benchmark runs.

## Pausing benchmarks

Running benchmarks locally is largely automated, but sometimes it is useful to
pause a benchmark, open DevTools, and manually inspect things in the DOM, the
console, or the networking tab for example. A hacky way to do this is to open
`third_party/catapult/telemetry/telemetry/internal/story_runner.py`, and add
the following lines [after `L141`](https://source.chromium.org/chromium/_/chromium/catapult.git/+/c5ac2a64a6688f72cd857b2f6f3a01d7379ae83d:telemetry/telemetry/internal/story_runner.py;l=152):

```
print("State RunStory")
time.sleep(120000)
```
