---
title: Retry Mechanism in Polly
---
This document will cover the concept of 'Retry' in the DEMO-Polly project. We'll cover:

1. What is 'Retry' in src/Polly/Retry
2. The main classes/functions/files that are related to 'Retry' in src/Polly/Retry

<SwmSnippet path="/src/Polly/Retry/RetrySyntax.cs" line="3">

---

# What is 'Retry' in src/Polly/Retry

The `RetrySyntax` class provides a fluent API for defining a Retry Policy. It contains several methods to build a `RetryPolicy` that will retry once or multiple times. The `RetryPolicy` can be configured to call a specified action on each retry with the raised exception and retry count.

```c#
/// <summary>
/// Fluent API for defining a Retry <see cref="Policy"/>.
/// </summary>
public static class RetrySyntax
{
    /// <summary>
    /// Builds a <see cref="Policy"/> that will retry once.
    /// </summary>
    /// <param name="policyBuilder">The policy builder.</param>
    /// <returns>The policy instance.</returns>
    public static RetryPolicy Retry(this PolicyBuilder policyBuilder) =>
        policyBuilder.Retry(1);

    /// <summary>
    /// Builds a <see cref="Policy"/> that will retry <paramref name="retryCount"/> times.
    /// </summary>
    /// <param name="policyBuilder">The policy builder.</param>
    /// <param name="retryCount">The retry count.</param>
    /// <returns>The policy instance.</returns>
    public static RetryPolicy Retry(this PolicyBuilder policyBuilder, int retryCount)
    {
```

---

</SwmSnippet>

<SwmSnippet path="/src/Polly/Retry/RetryPolicy.cs" line="8">

---

# The main classes/functions/files that are related to 'Retry' in src/Polly/Retry

The `RetryPolicy` class represents a policy that handles certain exceptions and results by retrying the delegated action. It uses the `RetryEngine` for its implementation. The `RetryPolicy` class also contains a `_permittedRetryCount` constant which represents the number of permitted retries.

```c#
{
    private readonly Action<Exception, TimeSpan, int, Context> _onRetry;
    private readonly int _permittedRetryCount;
    private readonly IEnumerable<TimeSpan>? _sleepDurationsEnumerable;
    private readonly Func<int, Exception, Context, TimeSpan>? _sleepDurationProvider;

    internal RetryPolicy(
        PolicyBuilder policyBuilder,
        Action<Exception, TimeSpan, int, Context> onRetry,
        int permittedRetryCount = int.MaxValue,
        IEnumerable<TimeSpan>? sleepDurationsEnumerable = null,
        Func<int, Exception, Context, TimeSpan>? sleepDurationProvider = null)
        : base(policyBuilder)
    {
        _permittedRetryCount = permittedRetryCount;
        _sleepDurationsEnumerable = sleepDurationsEnumerable;
        _sleepDurationProvider = sleepDurationProvider;
        _onRetry = onRetry ?? throw new ArgumentNullException(nameof(onRetry));
    }

    /// <inheritdoc/>
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm AI 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBREVNTy1Qb2xseSUzQSUzQXN3aW1taW8=" repo-name="DEMO-Polly"><sup>Powered by [Swimm](/)</sup></SwmMeta>
