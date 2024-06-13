---
title: >-
  Design pattern for fluent integration of resilience strategies in
  Polly.Extensions
---
This document will cover the design pattern used to implement the fluent integration of resilience strategies in the DEMO-Polly repo. We'll cover:

1. The Builder pattern in use
2. The role of ResilienceStrategyOptions
3. The implementation of specific resilience strategies.

<SwmSnippet path="/src/Snippets/Docs/ResilienceStrategies.cs" line="13">

---

# The Builder pattern in use

The Builder pattern is used to create a `ResiliencePipeline` object. This pattern allows for a step-by-step construction of a complex object, providing a clear and fluent interface. In this case, the `ResiliencePipelineBuilder` is used to add a Timeout strategy to the pipeline, and then build the final pipeline object.

```c#
        ResiliencePipeline pipeline = new ResiliencePipelineBuilder()
            .AddTimeout(new TimeoutStrategyOptions
            {
                Timeout = TimeSpan.FromSeconds(5)
            })
            .Build();
```

---

</SwmSnippet>

<SwmSnippet path="/src/Polly.Core/ResilienceStrategyOptions.cs" line="3">

---

# The role of ResilienceStrategyOptions

`ResilienceStrategyOptions` is an abstract class that serves as a base for specific resilience strategy options. It provides a property `Name` for identifying a particular instance of a strategy. This class is extended by specific strategy options classes, such as `RetryStrategyOptions`, `HedgingStrategyOptions`, and `TimeoutStrategyOptions`.

```c#
/// <summary>
/// The options associated with the individual resilience strategy.
/// </summary>
public abstract class ResilienceStrategyOptions
{
    /// <summary>
    /// Gets or sets the name of the strategy.
    /// </summary>
    /// <remarks>
    /// This name uniquely identifies a particular instance of a specific strategy and is also included
    /// in the telemetry that is produced by the individual resilience strategies.
    /// </remarks>
    /// <value>The default value is <see langword="null"/>.</value>
    public string? Name { get; set; }
```

---

</SwmSnippet>

<SwmSnippet path="/src/Polly.Core/Hedging/HedgingResilienceStrategy.cs" line="7">

---

# The implementation of specific resilience strategies

Each resilience strategy is implemented as a class extending the `ResilienceStrategy` base class. For example, the `HedgingResilienceStrategy` class implements the hedging strategy. It includes properties for the hedging delay, total attempts, delay generator, and hedging handler. The `ExecuteCore` method is overridden to provide the specific execution logic for the hedging strategy.

```c#
internal sealed class HedgingResilienceStrategy<T> : ResilienceStrategy<T>
{
    private readonly HedgingController<T> _controller;

    public HedgingResilienceStrategy(
        TimeSpan hedgingDelay,
        int maxHedgedAttempts,
        HedgingHandler<T> hedgingHandler,
        Func<HedgingDelayGeneratorArguments, ValueTask<TimeSpan>>? hedgingDelayGenerator,
        TimeProvider timeProvider,
        ResilienceStrategyTelemetry telemetry)
    {
        HedgingDelay = hedgingDelay;
        TotalAttempts = maxHedgedAttempts + 1; // include the initial attempt
        DelayGenerator = hedgingDelayGenerator;
        HedgingHandler = hedgingHandler;
        _controller = new HedgingController<T>(telemetry, timeProvider, HedgingHandler, TotalAttempts);
    }

    public TimeSpan HedgingDelay { get; }

```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm AI 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBREVNTy1Qb2xseSUzQSUzQXN3aW1taW8=" repo-name="DEMO-Polly"><sup>Powered by [Swimm](/)</sup></SwmMeta>
