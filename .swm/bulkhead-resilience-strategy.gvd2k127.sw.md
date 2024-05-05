---
title: Bulkhead Resilience Strategy
---
This document will cover the concept of Bulkhead in the DEMO-Polly project. We'll cover:

1. What is Bulkhead in src/Polly/Bulkhead
2. Main classes/functions/files that are related to Bulkhead in src/Polly/Bulkhead.

# What is Bulkhead in src/Polly/Bulkhead

Bulkhead is a resilience strategy used in the DEMO-Polly project. It is implemented as a policy that limits the maximum concurrency of actions executed through it. This is done to prevent a fault in one part of the system from bringing down the entire system. The Bulkhead policy is implemented in the `BulkheadPolicy` class in `src/Polly/Bulkhead/BulkheadPolicy.cs`.

<SwmSnippet path="/src/Polly/Bulkhead/BulkheadPolicy.cs" line="4">

---

# Main classes/functions/files that are related to Bulkhead in src/Polly/Bulkhead

The `BulkheadPolicy` class is the main class that implements the Bulkhead policy. It uses semaphores to limit the maximum concurrency and queueing actions. The `Dispose` method is used to release its internal resources.

```c#
/// <summary>
/// A bulkhead-isolation policy which can be applied to delegates.
/// </summary>
public class BulkheadPolicy : Policy, IBulkheadPolicy
{
    private readonly SemaphoreSlim _maxParallelizationSemaphore;
    private readonly SemaphoreSlim _maxQueuedActionsSemaphore;
    private readonly int _maxQueueingActions;
    private readonly Action<Context> _onBulkheadRejected;

    internal BulkheadPolicy(
        int maxParallelization,
        int maxQueueingActions,
        Action<Context> onBulkheadRejected)
    {
        _maxQueueingActions = maxQueueingActions;
        _onBulkheadRejected = onBulkheadRejected ?? throw new ArgumentNullException(nameof(onBulkheadRejected));

        (_maxParallelizationSemaphore, _maxQueuedActionsSemaphore) = BulkheadSemaphoreFactory.CreateBulkheadSemaphores(maxParallelization, maxQueueingActions);
    }

```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm AI 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBREVNTy1Qb2xseSUzQSUzQXN3aW1taW8=" repo-name="DEMO-Polly"><sup>Powered by [Swimm](/)</sup></SwmMeta>
