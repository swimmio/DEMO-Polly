---
title: 'Understanding Rate Limiting Rules and Strategies '
---
This document will cover the rate limiting rules and strategies implemented in the DEMO-Polly repo. We'll cover:

1. The RateLimiterStrategyOptions class
2. The RateLimit method
3. The AsyncRateLimitPolicy class.

<SwmSnippet path="/src/Polly.RateLimiting/RateLimiterStrategyOptions.cs" line="6">

---

# RateLimiterStrategyOptions class

The `RateLimiterStrategyOptions` class provides options for the rate limiter strategy. It allows you to set a rate limiter delegate that produces `RateLimitLease`, set the default rate limiter options, and set an event that is raised when the execution of user-provided callback is rejected by the rate limiter.

```c#
/// <summary>
/// Options for the rate limiter strategy.
/// </summary>
public class RateLimiterStrategyOptions : ResilienceStrategyOptions
{
    /// <summary>
    /// Initializes a new instance of the <see cref="RateLimiterStrategyOptions"/> class.
    /// </summary>
    public RateLimiterStrategyOptions() => Name = RateLimiterConstants.DefaultName;

    /// <summary>
    ///  Gets or sets a rate limiter delegate that produces <see cref="RateLimitLease"/>.
    /// </summary>
    /// <value>
    /// The default value is <see langword="null"/>. If this property is <see langword="null"/>, then the strategy
    /// will use a <see cref="ConcurrencyLimiter"/> created using <see cref="DefaultRateLimiterOptions"/>.
    /// </value>
    public Func<RateLimiterArguments, ValueTask<RateLimitLease>>? RateLimiter { get; set; }

    /// <summary>
    /// Gets or sets the default rate limiter options.
```

---

</SwmSnippet>

<SwmSnippet path="/src/Polly/RateLimit/RateLimitSyntax.cs" line="7">

---

# RateLimit method

The `RateLimit` method in the `Policy` class is used to build a RateLimit Policy that will rate-limit executions to a specified number of executions per the given timespan. It also allows you to specify the maximum number of executions that will be permitted in a single burst.

```c#
    /// <summary>
    /// Builds a RateLimit <see cref="Policy"/> that will rate-limit executions to <paramref name="numberOfExecutions"/> per the timespan given.
    /// </summary>
    /// <param name="numberOfExecutions">The number of executions (call it N) permitted per timespan.</param>
    /// <param name="perTimeSpan">How often N executions are permitted.</param>
    /// <returns>The policy instance.</returns>
    public static RateLimitPolicy RateLimit(
        int numberOfExecutions,
        TimeSpan perTimeSpan) =>
        RateLimit(numberOfExecutions, perTimeSpan, 1);

    /// <summary>
    /// Builds a RateLimit <see cref="Policy"/> that will rate-limit executions to <paramref name="numberOfExecutions"/> per the timespan given.
    /// </summary>
    /// <param name="numberOfExecutions">The number of executions (call it N) permitted per timespan.</param>
    /// <param name="perTimeSpan">How often N executions are permitted.</param>
    /// <param name="maxBurst">The maximum number of executions that will be permitted in a single burst (for example if none have been executed for a while).
    /// This equates to the bucket-capacity of a token-bucket implementation.</param>
    /// <returns>The policy instance.</returns>
    public static RateLimitPolicy RateLimit(
        int numberOfExecutions,
```

---

</SwmSnippet>

<SwmSnippet path="/src/Polly/RateLimit/AsyncRateLimitPolicy.cs" line="5">

---

# AsyncRateLimitPolicy class

The `AsyncRateLimitPolicy` class is a rate-limit policy that can be applied to asynchronous delegates. It uses an instance of `IRateLimiter` to limit the rate of executions.

```c#
/// <summary>
/// A rate-limit policy that can be applied to asynchronous delegates.
/// </summary>
public class AsyncRateLimitPolicy : AsyncPolicy, IRateLimitPolicy
{
    private readonly IRateLimiter _rateLimiter;

    internal AsyncRateLimitPolicy(IRateLimiter rateLimiter) =>
        _rateLimiter = rateLimiter ?? throw new ArgumentNullException(nameof(rateLimiter));

    /// <inheritdoc/>
    [DebuggerStepThrough]
    protected override Task<TResult> ImplementationAsync<TResult>(Func<Context, CancellationToken, Task<TResult>> action, Context context, CancellationToken cancellationToken,
        bool continueOnCapturedContext) =>
        AsyncRateLimitEngine.ImplementationAsync(_rateLimiter, null, action, context, cancellationToken, continueOnCapturedContext);
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm AI 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBREVNTy1Qb2xseSUzQSUzQXN3aW1taW8=" repo-name="DEMO-Polly"><sup>Powered by [Swimm](/)</sup></SwmMeta>
