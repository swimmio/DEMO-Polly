---
title: RateLimit Policy Overview
---
This document will cover the topic of 'RateLimit' in the DEMO-Polly repo. We'll cover:

1. What 'RateLimit' is and where it is implemented.
2. The main classes/functions/files related to 'RateLimit'.

<SwmSnippet path="/src/Polly/RateLimit/RateLimitSyntax.cs" line="5">

---

# What is 'RateLimit' and where is it implemented

The 'RateLimit' is a policy that limits the number of executions to a specified number per given timespan. It is implemented in the 'Policy' class in the 'RateLimitSyntax.cs' file. The 'RateLimit' method in this class builds a 'RateLimitPolicy' that will rate-limit executions to a specified number of executions per the timespan given.

```c#
public partial class Policy
{
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
```

---

</SwmSnippet>

<SwmSnippet path="/src/Polly/RateLimit/RateLimitPolicy.cs" line="5">

---

# Main classes/functions/files related to 'RateLimit'

The 'RateLimitPolicy' class in the 'RateLimitPolicy.cs' file is a rate-limit policy that can be applied to synchronous delegates. It uses the 'RateLimitEngine' for its implementation.

```c#
/// <summary>
/// A rate-limit policy that can be applied to synchronous delegates.
/// </summary>
public class RateLimitPolicy : Policy, IRateLimitPolicy
{
    private readonly IRateLimiter _rateLimiter;

    internal RateLimitPolicy(IRateLimiter rateLimiter) =>
        _rateLimiter = rateLimiter ?? throw new ArgumentNullException(nameof(rateLimiter));

    /// <inheritdoc/>
    [DebuggerStepThrough]
    protected override TResult Implementation<TResult>(Func<Context, CancellationToken, TResult> action, Context context, CancellationToken cancellationToken) =>
        RateLimitEngine.Implementation(_rateLimiter, null, action, context, cancellationToken);
}
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm AI 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBREVNTy1Qb2xseSUzQSUzQXN3aW1taW8=" repo-name="DEMO-Polly"><sup>Powered by [Swimm](/)</sup></SwmMeta>
