---
title: Achieving thread-safe resilience with Polly.Extensions
---
This document will cover the topic of achieving resilience in a thread-safe manner with Polly.Extensions. We'll cover:

1. The role of the ResilienceContext class
2. How the ResilienceContextPool class manages instances of ResilienceContext
3. The implementation of the HedgingResilienceStrategy class
4. The use of ResilienceProperties for storing custom resilience properties.

<SwmSnippet path="/src/Polly.Core/ResilienceContext.cs" line="5">

---

# The Role of the ResilienceContext Class

The `ResilienceContext` class is a context assigned to a single execution of `ResiliencePipeline`. It is created manually or automatically when the user calls the various extensions on top of `ResiliencePipeline`. After every execution, the context should be discarded and returned to the pool. It contains properties like `OperationKey`, `CancellationToken`, `IsSynchronous`, `ResultType`, and `ContinueOnCapturedContext` which are essential for managing resilience in a thread-safe manner.

```c#
/// <summary>
/// A context assigned to a single execution of <see cref="ResiliencePipeline"/>. It is created manually or automatically
/// when the user calls the various extensions on top of <see cref="ResiliencePipeline"/>. After every execution the context should be discarded and returned to the pool.
/// </summary>
/// <remarks>
/// Do not re-use an instance of <see cref="ResilienceContext"/> across more than one execution. The <see cref="ResilienceContext"/> is retrieved from the pool
/// by calling the <see cref="ResilienceContextPool.Get(CancellationToken)"/> method. After you are done with it you should return it to the pool
/// by calling the <see cref="ResilienceContextPool.Return(ResilienceContext)"/> method.
/// </remarks>
public sealed class ResilienceContext
{
    internal ResilienceContext()
    {
    }

    /// <summary>
    /// Gets a key unique to the call site of the current execution.
    /// </summary>
    /// <remarks>
    /// Resilience context instances are commonly reused across multiple call sites.
    /// Set an <see cref="OperationKey"/> so that logging and metrics can distinguish usages of policy instances at different call sites.
```

---

</SwmSnippet>

<SwmSnippet path="/src/Polly.Core/ResilienceContextPool.cs" line="1">

---

# Managing Instances of ResilienceContext with ResilienceContextPool

The `ResilienceContextPool` class manages a pool of `ResilienceContext` instances. It provides methods to get an instance from the pool and return an instance back to the pool. This ensures that `ResilienceContext` instances are reused efficiently, contributing to thread safety.

```c#
namespace Polly;

#pragma warning disable RS0026 // Do not add multiple public overloads with optional parameters
#pragma warning disable CA1716 // Identifiers should not match keywords

/// <summary>
/// The pool of <see cref="ResilienceContext"/> instances.
/// </summary>
public abstract partial class ResilienceContextPool
{
    /// <summary>
    /// Gets the shared pool instance.
    /// </summary>
    public static ResilienceContextPool Shared { get; } = new SharedPool();

    /// <summary>
    /// Gets a <see cref="ResilienceContext"/> instance from the pool.
    /// </summary>
    /// <param name="cancellationToken">The cancellation token.</param>
    /// <returns>An instance of <see cref="ResilienceContext"/>.</returns>
    /// <remarks>
```

---

</SwmSnippet>

<SwmSnippet path="/src/Polly.Core/Hedging/HedgingResilienceStrategy.cs" line="5">

---

# Implementation of HedgingResilienceStrategy

The `HedgingResilienceStrategy` class is a specific resilience strategy that uses a `HedgingController` to manage resilience. It includes methods for executing the strategy and getting the hedging delay, which are crucial for achieving resilience.

```c#
namespace Polly.Hedging;

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

```

---

</SwmSnippet>

<SwmSnippet path="/src/Polly.Core/ResilienceProperties.cs" line="1">

---

# Use of ResilienceProperties

The `ResilienceProperties` class represents a collection of custom resilience properties. It provides methods to get and set property values, which can be used to customize the resilience strategy.

```c#
using System.Diagnostics.CodeAnalysis;

namespace Polly;

/// <summary>
/// Represents a collection of custom resilience properties.
/// </summary>
[DebuggerDisplay("{Options}")]
public sealed class ResilienceProperties
{
    [DebuggerBrowsable(DebuggerBrowsableState.RootHidden)]
    internal IDictionary<string, object?> Options { get; set; } = new Dictionary<string, object?>();

    /// <summary>
    /// Gets the value of a given property.
    /// </summary>
    /// <param name="key">Strongly typed key to get the value of the property.</param>
    /// <param name="value">Returns the value of the property.</param>
    /// <typeparam name="TValue">The type of property value as defined by <paramref name="key"/> parameter.</typeparam>
    /// <returns>True, if a property was retrieved.</returns>
    public bool TryGetValue<TValue>(ResiliencePropertyKey<TValue> key, [MaybeNullWhen(false)] out TValue value)
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm AI 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBREVNTy1Qb2xseSUzQSUzQXN3aW1taW8=" repo-name="DEMO-Polly"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
