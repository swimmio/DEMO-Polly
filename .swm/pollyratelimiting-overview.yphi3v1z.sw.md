---
title: Polly.RateLimiting Overview
---
This document will cover the topic of Polly.RateLimiting in the DEMO-Polly repo. We'll cover:

1. What Polly.RateLimiting is and where it's implemented.
2. The main classes/functions/files related to Polly.RateLimiting.

# What is Polly.RateLimiting and where it's implemented

Polly.RateLimiting is a part of the Polly library that provides rate limiting functionality. It allows developers to limit the rate at which certain operations can be performed, which can be useful in scenarios where resource usage needs to be controlled or where there are external rate limits that need to be adhered to. The main implementation of Polly.RateLimiting can be found in the `src/Polly.RateLimiting` directory.

<SwmSnippet path="/src/Polly.RateLimiting/RateLimiterArguments.cs" line="3">

---

# Main classes/functions/files related to Polly.RateLimiting

The `RateLimiterArguments` struct is a key part of the Polly.RateLimiting functionality. It is used to pass arguments to the `RateLimiterStrategyOptions.RateLimiter` delegate. It contains a `ResilienceContext` which is associated with the execution of a user-provided callback.

```c#
#pragma warning disable CA1815 // Override equals and operator equals on value types

/// <summary>
/// The arguments used by the <see cref="RateLimiterStrategyOptions.RateLimiter"/> delegate.
/// </summary>
public readonly struct RateLimiterArguments
{
    /// <summary>
    /// Initializes a new instance of the <see cref="RateLimiterArguments"/> struct.
    /// </summary>
    /// <param name="context">Context associated with the execution of a user-provided callback.</param>
    public RateLimiterArguments(ResilienceContext context) => Context = context;

    /// <summary>
    /// Gets the context associated with the execution of a user-provided callback.
    /// </summary>
    public ResilienceContext Context { get; }
}
```

---

</SwmSnippet>

<SwmSnippet path="/src/Polly.RateLimiting/RateLimiterResilienceStrategy.cs" line="10">

---

`RateLimiterResilienceStrategy` is another important class in Polly.RateLimiting. It uses the `RateLimiterArguments` to control the rate at which operations are performed.

```c#
    public RateLimiterResilienceStrategy(
        Func<RateLimiterArguments, ValueTask<RateLimitLease>> limiter,
        Func<OnRateLimiterRejectedArguments, ValueTask>? onRejected,
        ResilienceStrategyTelemetry telemetry,
        DisposeWrapper? wrapper)
    {
        Limiter = limiter;
        OnLeaseRejected = onRejected;

        _telemetry = telemetry;
        Wrapper = wrapper;
    }

    public Func<RateLimiterArguments, ValueTask<RateLimitLease>> Limiter { get; }

    public Func<OnRateLimiterRejectedArguments, ValueTask>? OnLeaseRejected { get; }

    public DisposeWrapper? Wrapper { get; }

    public void Dispose() => Wrapper?.Dispose();

```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm AI 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBREVNTy1Qb2xseSUzQSUzQXN3aW1taW8=" repo-name="DEMO-Polly"><sup>Powered by [Swimm](/)</sup></SwmMeta>
