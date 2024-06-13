---
title: Understanding thread safety in Polly.Core
---
This document will cover the topic of thread safety in Polly.Core. We'll cover:

1. How thread safety is achieved in Polly.Core
2. The role of the SystemClock class in thread safety
3. The use of the TimedLock struct for thread safety.

# Thread Safety in Polly.Core

Thread safety in Polly.Core is achieved through a combination of techniques. One of these is the use of the `SystemClock` class, which provides thread-safe methods for time-related operations. Another is the use of the `TimedLock` struct, which provides a mechanism for acquiring a lock on a resource for a specified duration.

<SwmSnippet path="/src/Polly/Utilities/SystemClock.cs" line="4">

---

# SystemClock and Thread Safety

The `SystemClock` class provides a set of static methods and properties that are used for time-related operations. These operations are designed to be thread-safe. For example, the `Sleep` method uses a `CancellationToken`'s `WaitHandle` to pause execution in a way that can be safely interrupted by other threads.

```c#
/// <summary>
/// Time related delegates used to support different compilation targets and to improve testability of the code.
/// </summary>
public static class SystemClock
{
    /// <summary>
    /// Allows the setting of a custom Thread.Sleep implementation for testing.
    /// By default this will use the <see cref="CancellationToken"/>'s <see cref="WaitHandle"/>.
    /// </summary>
    public static Action<TimeSpan, CancellationToken> Sleep = (timeSpan, cancellationToken) =>
    {
        if (cancellationToken.WaitHandle.WaitOne(timeSpan))
        {
            cancellationToken.ThrowIfCancellationRequested();
        }
    };

    /// <summary>
    /// Allows the setting of a custom async Sleep implementation for testing.
    /// By default this will be a call to <see cref="Task.Delay(TimeSpan)"/> taking a <see cref="CancellationToken"/>.
    /// </summary>
```

---

</SwmSnippet>

<SwmSnippet path="/src/Polly/Utilities/TimedLock.cs" line="3">

---

# TimedLock and Thread Safety

The `TimedLock` struct provides a mechanism for acquiring a lock on a resource for a specified duration. If the lock cannot be acquired within the specified duration, a `LockTimeoutException` is thrown. This helps to prevent deadlocks and ensure thread safety.

```c#

// Adapted from the link below, with slight modifications.

// https://www.interact-sw.co.uk/iangblog/2004/04/26/yetmoretimedlocking
// Ian Griffiths (original TimedLock author) wrote:
// Thanks to Eric Gunnerson for recommending this be a struct rather
// than a class - avoids a heap allocation.
// Thanks to Change Gillespie and Jocelyn Coulmance for pointing out
// the bugs that then crept in when I changed it to use struct...
// Thanks to John Sands for providing the necessary incentive to make
// me invent a way of using a struct in both release and debug builds
// without losing the debug leak tracking.
internal readonly struct TimedLock : IDisposable
{
    // The TimedLock class throws a LockTimeoutException if a lock cannot be obtained within the LockTimeout.  This allows the easier discovery and debugging of deadlocks during Polly development, than if using a pure lock.
    // We do not however ever want to throw a LockTimeoutException in production - hence the forked LockTimeout value below for DEBUG versus RELEASE builds.
    // This applies particularly because CircuitBreakerPolicy runs state-change delegates during the lock, in order that the state change holds true (cannot be superseded by activity on other threads) while the delegate runs.
#if DEBUG
    private static readonly TimeSpan LockTimeout = TimeSpan.FromSeconds(5);
#else
    private static readonly TimeSpan LockTimeout = TimeSpan.FromMilliseconds(int.MaxValue);
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm AI 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBREVNTy1Qb2xseSUzQSUzQXN3aW1taW8=" repo-name="DEMO-Polly"><sup>Powered by [Swimm](/)</sup></SwmMeta>
