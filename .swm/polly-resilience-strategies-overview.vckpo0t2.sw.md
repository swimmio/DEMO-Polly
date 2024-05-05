---
title: Polly Resilience Strategies Overview
---
This document will cover the following aspects of Polly Resilience Strategies:

1. What are Polly Resilience Strategies
2. Main classes/functions/files related to Polly Resilience Strategies

# What are Polly Resilience Strategies

Polly Resilience Strategies are a set of strategies provided by the Polly library to handle transient faults in a fluent and thread-safe manner. These strategies include Retry, Circuit Breaker, Hedging, Timeout, Rate Limiter, and Fallback.

<SwmSnippet path="/src/Polly/Utilities/Wrappers/ResiliencePipelineSyncPolicy.cs" line="3">

---

# Main classes/functions/files related to Polly Resilience Strategies

The `ResiliencePipelineSyncPolicy` class is a key part of implementing Polly Resilience Strategies. It uses a `ResiliencePipeline` object to execute the resilience strategy.

```c#
internal sealed class ResiliencePipelineSyncPolicy : Policy
{
    private readonly ResiliencePipeline _pipeline;

    public ResiliencePipelineSyncPolicy(ResiliencePipeline strategy) => _pipeline = strategy;
```

---

</SwmSnippet>

<SwmSnippet path="/src/Polly/Utilities/Wrappers/ResilienceContextFactory.cs" line="5">

---

The `ResilienceContextFactory` class is used to create a `ResilienceContext` object. This object is used to manage the resilience properties and is used in the execution of the resilience strategy.

```c#
    public static ResilienceContext Create(
        Context context,
        CancellationToken cancellationToken,
        bool continueOnCapturedContext,
        out IDictionary<string, object> oldProperties)
    {
        var resilienceContext = ResilienceContextPool.Shared.Get(context.OperationKey, continueOnCapturedContext, cancellationToken);
        resilienceContext.Properties.SetProperties(context, out oldProperties);

        return resilienceContext;
    }
```

---

</SwmSnippet>

<SwmSnippet path="/src/Polly/ResiliencePipelineConversionExtensions.cs" line="8">

---

The `ResiliencePipelineConversionExtensions` class provides methods to convert resilience strategies to policies. This is crucial for the application of the resilience strategies.

```c#
public static class ResiliencePipelineConversionExtensions
{
    /// <summary>
    /// Converts a <see cref="ResiliencePipeline"/> to an <see cref="IAsyncPolicy"/>.
    /// </summary>
    /// <param name="strategy">The strategy instance.</param>
    /// <returns>An instance of <see cref="IAsyncPolicy"/>.</returns>
    /// <exception cref="ArgumentNullException">Thrown when <paramref name="strategy"/> is <see langword="null"/>.</exception>
    public static IAsyncPolicy AsAsyncPolicy(this ResiliencePipeline strategy)
        => new ResiliencePipelineAsyncPolicy(strategy ?? throw new ArgumentNullException(nameof(strategy)));

    /// <summary>
    /// Converts a <see cref="ResiliencePipeline{TResult}"/> to an <see cref="IAsyncPolicy{TResult}"/>.
    /// </summary>
    /// <typeparam name="TResult">The type of the result.</typeparam>
    /// <param name="strategy">The strategy instance.</param>
    /// <returns>An instance of <see cref="IAsyncPolicy{TResult}"/>.</returns>
    /// <exception cref="ArgumentNullException">Thrown when <paramref name="strategy"/> is <see langword="null"/>.</exception>
    public static IAsyncPolicy<TResult> AsAsyncPolicy<TResult>(this ResiliencePipeline<TResult> strategy)
        => new ResiliencePipelineAsyncPolicy<TResult>(strategy ?? throw new ArgumentNullException(nameof(strategy)));

```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm AI 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBREVNTy1Qb2xseSUzQSUzQXN3aW1taW8=" repo-name="DEMO-Polly"><sup>Powered by [Swimm](/)</sup></SwmMeta>
