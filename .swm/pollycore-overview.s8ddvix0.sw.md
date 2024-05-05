---
title: Polly.Core Overview
---
This document will cover the topic of 'Polly.Core' in the DEMO-Polly repo. We'll cover:

1. What is 'Polly.Core'
2. Main classes/functions/files related to 'Polly.Core'

# What is 'Polly.Core'

'Polly.Core' is a core part of the Polly library, a .NET resilience and transient-fault-handling library. It contains the main functionalities and utilities used by the library to implement resilience strategies such as Retry, Circuit Breaker, Hedging, Timeout, Rate Limiter, and Fallback.

<SwmSnippet path="/src/Polly.Core/LegacySupport.cs" line="5">

---

# Main classes/functions/files related to 'Polly.Core'

One of the main classes in 'Polly.Core' is the `LegacySupport` class. This class provides support for older versions of Polly and is used by the legacy Polly infrastructure. It contains a method `SetProperties` that changes the underlying properties of a `ResilienceProperties` instance.

```c#
/// <summary>
/// Legacy support for older versions of Polly.
/// </summary>
/// <remarks>
/// This class is used by the legacy Polly infrastructure and should not be used directly by user code.
/// </remarks>
[EditorBrowsable(EditorBrowsableState.Never)]
public static class LegacySupport
{
    /// <summary>
    /// Changes the underlying properties of a <see cref="ResilienceProperties"/> instance.
    /// </summary>
    /// <param name="resilienceProperties">The resilience properties.</param>
    /// <param name="properties">The properties to use.</param>
    /// <param name="oldProperties">The old properties used by <paramref name="resilienceProperties"/>.</param>
    /// <remarks>
    /// This method is used by the legacy Polly infrastructure and should not be used directly by user code.
    /// </remarks>
    [EditorBrowsable(EditorBrowsableState.Never)]
    public static void SetProperties(
        this ResilienceProperties resilienceProperties,
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm AI 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBREVNTy1Qb2xseSUzQSUzQXN3aW1taW8=" repo-name="DEMO-Polly"><sup>Powered by [Swimm](/)</sup></SwmMeta>
