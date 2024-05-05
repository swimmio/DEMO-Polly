---
title: Interplay between NoOp and Other Components in the Polly Library Context
---
This document will cover the NoOp functionality within the larger context of the Polly library. We'll cover:

1. The role of NoOp in the Polly library
2. How NoOp interacts with other components of the Polly library.

# The role of NoOp in the Polly library

The NoOp directory in the Polly library is where the NoOp functionality is implemented. NoOp, or 'No Operation', is a placeholder policy which performs no operation. It's used when no specific policy is required.

<SwmSnippet path="/src/Polly/Wrap/PolicyWrap.cs" line="3">

---

# How NoOp interacts with other components of the Polly library

The PolicyWrap class in the Polly library allows two or more Polly policies to wrap executions of delegates. This includes the NoOp policy, which can be used as an outer or inner policy in a PolicyWrap. This shows how NoOp interacts with other components of the Polly library.

```c#
/// <summary>
/// A policy that allows two (and by recursion more) Polly policies to wrap executions of delegates.
/// </summary>
public partial class PolicyWrap : Policy, IPolicyWrap
{
    private readonly ISyncPolicy _outer;
    private readonly ISyncPolicy _inner;

    /// <summary>
    /// Gets the outer <see cref="IsPolicy"/> in this <see cref="IPolicyWrap"/>.
    /// </summary>
    public IsPolicy Outer => _outer;

    /// <summary>
    /// Gets the next inner <see cref="IsPolicy"/> in this <see cref="IPolicyWrap"/>.
    /// </summary>
    public IsPolicy Inner => _inner;

    internal PolicyWrap(Policy outer, ISyncPolicy inner)
        : base(outer.ExceptionPredicates)
    {
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm AI 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBREVNTy1Qb2xseSUzQSUzQXN3aW1taW8=" repo-name="DEMO-Polly"><sup>Powered by [Swimm](/)</sup></SwmMeta>
