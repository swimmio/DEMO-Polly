---
title: Timeout Functionality Overview
---
This document will cover the concept of 'Timeout' in the DEMO-Polly repo. We'll cover:

1. What 'Timeout' is in src/Polly/Timeout
2. The main classes/functions/files that are related to 'Timeout' in src/Polly/Timeout.

# What is 'Timeout' in src/Polly/Timeout

'Timeout' in src/Polly/Timeout is a resilience strategy provided by the Polly library. It allows developers to specify a period of time that a delegate should be allowed to run. If the delegate does not complete within this period, a `TimeoutRejectedException` is thrown. The timeout period can be specified in seconds or as a `TimeSpan`.

<SwmSnippet path="/src/Polly/Timeout/TimeoutPolicy.cs" line="6">

---

# Main classes/functions/files related to 'Timeout'

The `TimeoutPolicy` class is a key part of the 'Timeout' functionality. It uses a `TimeoutStrategy` to determine how timeouts are enforced and a `Func<Context, TimeSpan>` to provide the timeout for each execution.

```c#
public class TimeoutPolicy : Policy, ITimeoutPolicy
{
    private readonly TimeoutStrategy _timeoutStrategy;
    private readonly Func<Context, TimeSpan> _timeoutProvider;
    private readonly Action<Context, TimeSpan, Task, Exception> _onTimeout;

    internal TimeoutPolicy(
        Func<Context, TimeSpan> timeoutProvider,
        TimeoutStrategy timeoutStrategy,
        Action<Context, TimeSpan, Task, Exception> onTimeout)
    {
        _timeoutProvider = timeoutProvider ?? throw new ArgumentNullException(nameof(timeoutProvider));
        _timeoutStrategy = timeoutStrategy;
        _onTimeout = onTimeout ?? throw new ArgumentNullException(nameof(onTimeout));
    }

    /// <inheritdoc/>
    [DebuggerStepThrough]
    protected override TResult Implementation<TResult>(Func<Context, CancellationToken, TResult> action, Context context, CancellationToken cancellationToken) =>
        TimeoutEngine.Implementation(
            action,
```

---

</SwmSnippet>

<SwmSnippet path="/src/Polly/Timeout/TimeoutSyntax.cs" line="383">

---

The `Timeout` method in `TimeoutSyntax.cs` is used to build a `TimeoutPolicy` that will wait for a delegate to complete for a specified period of time. If the delegate does not complete within the configured timeout, a `TimeoutRejectedException` is thrown.

```c#
    /// <summary>
    /// Builds a <see cref="Policy" /> that will wait for a delegate to complete for a specified period of time. A <see cref="TimeoutRejectedException" /> will be thrown if the delegate does not complete within the configured timeout.
    /// </summary>
    /// <param name="timeoutProvider">A function to provide the timeout for this execution.</param>
    /// <param name="timeoutStrategy">The timeout strategy.</param>
    /// <param name="onTimeout">An action to call on timeout, passing the execution context, the timeout applied, the <see cref="Task" /> capturing the abandoned, timed-out action, and the captured <see cref="Exception"/>.
    /// <remarks>The Task parameter will be null if the executed action responded cooperatively to cancellation before the policy timed it out.</remarks></param>
    /// <returns>The policy instance.</returns>
    /// <exception cref="ArgumentNullException">Thrown when <paramref name="timeoutProvider"/> is <see langword="null"/>.</exception>
    /// <exception cref="ArgumentNullException">Thrown when <paramref name="onTimeout"/> is <see langword="null"/>.</exception>
    public static TimeoutPolicy Timeout(
        Func<Context, TimeSpan> timeoutProvider,
        TimeoutStrategy timeoutStrategy,
        Action<Context, TimeSpan, Task, Exception> onTimeout)
    {
        if (timeoutProvider == null)
        {
            throw new ArgumentNullException(nameof(timeoutProvider));
        }

        if (onTimeout == null)
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm AI 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBREVNTy1Qb2xseSUzQSUzQXN3aW1taW8=" repo-name="DEMO-Polly"><sup>Powered by [Swimm](/)</sup></SwmMeta>
