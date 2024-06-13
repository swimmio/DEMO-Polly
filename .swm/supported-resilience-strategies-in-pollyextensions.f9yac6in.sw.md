---
title: Supported resilience strategies in Polly.Extensions
---
This document will cover the specific resilience strategies handled by the extensions in the DEMO-Polly repo. We'll cover:

1. Understanding resilience strategies
2. How these strategies are implemented in the codebase
3. How to use these strategies.

# Understanding resilience strategies

Resilience strategies are essential components of Polly, designed to execute user-defined callbacks while adding an extra layer of resilience. These strategies can't be executed directly; they must be run through a resilience pipeline. Polly provides an API to construct resilience pipelines by incorporating one or more resilience strategies through the pipeline builders. Polly categorizes resilience strategies into two main groups: Reactive and Proactive. Reactive strategies handle specific exceptions that are thrown, or results that are returned, by the callbacks executed through the strategy. Proactive strategies do not focus on handling errors by the callbacks might throw or return. They can make proactive decisions to cancel or reject the execution of callbacks (e.g., using a rate limiter or a timeout resilience strategy).

<SwmSnippet path="/src/Polly.Core/ResilienceStrategy.cs" line="3">

---

# Implementing resilience strategies in the codebase

The `ResilienceStrategy` class is the base class for all proactive resilience strategies. It provides an abstract method `ExecuteCore` that executes the specified callback. The provided callback never throws an exception. Instead, the exception is captured and converted to an `Outcome<TResult>`. Similarly, do not throw exceptions from your strategy implementation. Instead, return an exception instance as `Outcome<TResult>`.

```c#
/// <summary>
/// Base class for all proactive resilience strategies.
/// </summary>
public abstract class ResilienceStrategy
{
    /// <summary>
    /// An implementation of a proactive resilience strategy that executes the specified <paramref name="callback"/>.
    /// </summary>
    /// <typeparam name="TResult">The type of result returned by the callback.</typeparam>
    /// <typeparam name="TState">The type of state associated with the callback.</typeparam>
    /// <param name="callback">The user-provided callback.</param>
    /// <param name="context">The context associated with the callback.</param>
    /// <param name="state">The state associated with the callback.</param>
    /// <returns>
    /// An instance of a pending <see cref="ValueTask"/> for asynchronous executions or a completed <see cref="ValueTask"/> task for synchronous executions.
    /// </returns>
    /// <remarks>
    /// The provided callback never throws an exception. Instead, the exception is captured and converted to an <see cref="Outcome{TResult}"/>.
    /// Similarly, do not throw exceptions from your strategy implementation. Instead, return an exception instance as <see cref="Outcome{TResult}"/>.
    /// </remarks>
    protected internal abstract ValueTask<Outcome<TResult>> ExecuteCore<TResult, TState>(
```

---

</SwmSnippet>

<SwmSnippet path="/src/Polly.Core/Fallback/FallbackResilienceStrategy.cs" line="5">

---

The `FallbackResilienceStrategy` class is an example of a resilience strategy implementation. It is a reactive strategy that provides a fallback mechanism when an error occurs during the execution of the callback. The `ExecuteCore` method executes the callback and checks if a fallback should be handled. If so, it triggers the `OnFallback` event and tries to get the fallback outcome.

```c#
#pragma warning disable CA1031 // Do not catch general exception types

internal sealed class FallbackResilienceStrategy<T> : ResilienceStrategy<T>
{
    private readonly FallbackHandler<T> _handler;
    private readonly Func<OnFallbackArguments<T>, ValueTask>? _onFallback;
    private readonly ResilienceStrategyTelemetry _telemetry;

    public FallbackResilienceStrategy(FallbackHandler<T> handler, Func<OnFallbackArguments<T>, ValueTask>? onFallback, ResilienceStrategyTelemetry telemetry)
    {
        _handler = handler;
        _onFallback = onFallback;
        _telemetry = telemetry;
    }

    protected internal override async ValueTask<Outcome<T>> ExecuteCore<TState>(Func<ResilienceContext, TState, ValueTask<Outcome<T>>> callback, ResilienceContext context, TState state)
    {
        var outcome = await StrategyHelper.ExecuteCallbackSafeAsync(callback, context, state).ConfigureAwait(context.ContinueOnCapturedContext);
        var handleFallbackArgs = new FallbackPredicateArguments<T>(context, outcome);
        if (!await _handler.ShouldHandle(handleFallbackArgs).ConfigureAwait(context.ContinueOnCapturedContext))
        {
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm AI 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBREVNTy1Qb2xseSUzQSUzQXN3aW1taW8=" repo-name="DEMO-Polly"><sup>Powered by [Swimm](/)</sup></SwmMeta>
