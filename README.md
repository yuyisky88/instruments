# Instruments

Instruments allows you to collects metrics over discrete time intervals.

Collected metrics will only reflect observations from last time window only,
rather than including observations from prior windows, contrary to EWMA based metrics.

## Installation

Download and install:

```
$ go get github.com/heroku/instruments
```

Add it to your code:

```go
import "github.com/heroku/instruments"
```

## Usage

```go
timer := instruments.NewTimer(-1)

registry := reporter.NewRegistry()
registry.Register("processing-time", timer)

go reporter.Log("process", registry, time.Minute)

timer.Time(func() {
  ...
})
```

## Instruments

Instruments support two types of instruments: Discrete instruments return a single value, and Sample instruments a sorted array of values.

These base instruments are available:

- Counter: a simple counter.
- Rate: tracks the rate of values per seconds.
- Reservoir: randomly samples values.
- Derive: tracks the rate of values based on the delta with previous value.
- Gauge: tracks last value.
- Timer: tracks durations.

You can create custom instruments or compose new instruments form the built-in instruments as long as they implements the Sample or Discrete interfaces.

## Reporters

Registry enforce the Discrete and Sample interfaces, creating a custom Reporter should be trivial, for example:

```go
for k, m := range registry.Instruments() {
  switch i := m.(type) {
  case instruments.Discrete:
    s := i.Snapshot()
    report(k, s)
  case instruments.Sample:
    s := instruments.Quantile(i.Snapshot(), 0.95)
    report(k, s)
  }
}
```

## See also

* [Instrumentation by Composition](https://engineering.heroku.com/blogs/2014-10-23-instrumentation-by-composition)
* [Go Metrics](https://github.com/rcrowley/go-metrics)
* [Interval metrics](https://github.com/aphyr/interval-metrics)
