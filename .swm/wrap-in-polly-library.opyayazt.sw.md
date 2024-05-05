---
title: Wrap in Polly Library
---
This document will cover the topic of 'Wrap' in the DEMO-Polly repo. We'll cover:

1. What 'Wrap' is and where it is implemented
2. The main classes/functions/files related to 'Wrap'

# What is 'Wrap' and where is it implemented

'Wrap' in the DEMO-Polly repo is a concept used in the Polly library to wrap executions of delegates with resilience strategies. It is implemented in the `src/Polly/Wrap` directory. The main classes related to 'Wrap' are `IPolicyWrapExtension`, `PolicyWrap`, `IPolicyWrap`, `IAsyncPolicyPolicyWrapExtensions`, and `ISyncPolicyPolicyWrapExtensions`.

<SwmSnippet path="/src/Polly/Wrap/IPolicyWrapExtension.cs" line="3">

---

# Main classes/functions/files related to 'Wrap'

The `IPolicyWrapExtension` class provides extension methods for `IPolicyWrap`. It includes methods to get all policies in the wrap, get policies of a specific type, and get a single policy of a specific type.

```c#
/// <summary>
/// Extension methods for IPolicyWrap.
/// </summary>
public static class IPolicyWrapExtension
{
    /// <summary>
    /// Returns all the policies in this <see cref="IPolicyWrap"/>, in Outer-to-Inner order.
    /// </summary>
    /// <param name="policyWrap">The <see cref="IPolicyWrap"/> for which to return policies.</param>
    /// <returns>An <see cref="IEnumerable{IsPolicy}"/> of all the policies in the wrap.</returns>
    public static IEnumerable<IsPolicy> GetPolicies(this IPolicyWrap policyWrap)
    {
        var childPolicies = new[] { policyWrap.Outer, policyWrap.Inner };
        foreach (var childPolicy in childPolicies)
        {
            if (childPolicy is IPolicyWrap anotherWrap)
            {
                foreach (var policy in anotherWrap.GetPolicies())
                {
                    yield return policy;
                }
```

---

</SwmSnippet>

<SwmSnippet path="/src/Polly/Wrap/PolicyWrap.cs" line="3">

---

The `PolicyWrap` class allows two or more Polly policies to wrap executions of delegates. It includes methods to get the outer and inner policies and to implement the wrap.

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

<SwmSnippet path="/src/Polly/Wrap/IAsyncPolicyPolicyWrapExtensions.cs" line="3">

---

The `IAsyncPolicyPolicyWrapExtensions` class provides extension methods for configuring `PolicyWrap` instances on an `IAsyncPolicy` or `IAsyncPolicy<TResult>`. It includes methods to wrap the specified outer policy around the inner policy.

```c#
/// <summary>
/// Defines extensions for configuring <see cref="PolicyWrap"/> instances on an <see cref="IAsyncPolicy"/> or <see cref="IAsyncPolicy{TResult}"/>.
/// </summary>
public static class IAsyncPolicyPolicyWrapExtensions
{
    /// <summary>
    /// Wraps the specified outer policy round the inner policy.
    /// </summary>
    /// <param name="outerPolicy">The outer policy.</param>
    /// <param name="innerPolicy">The inner policy.</param>
    /// <returns>A <see cref="PolicyWrap"/> instance representing the combined wrap.</returns>
    public static AsyncPolicyWrap WrapAsync(this IAsyncPolicy outerPolicy, IAsyncPolicy innerPolicy)
    {
        if (outerPolicy == null)
        {
            throw new ArgumentNullException(nameof(outerPolicy));
        }

        return ((AsyncPolicy)outerPolicy).WrapAsync(innerPolicy);
    }

```

---

</SwmSnippet>

<SwmSnippet path="/src/Polly/Wrap/ISyncPolicyPolicyWrapExtensions.cs" line="3">

---

The `ISyncPolicyPolicyWrapExtensions` class provides extension methods for configuring `PolicyWrap` instances on an `ISyncPolicy` or `ISyncPolicy<TResult>`. It includes methods to wrap the specified outer policy around the inner policy.

```c#
/// <summary>
/// Defines extensions for configuring <see cref="PolicyWrap"/> instances on an <see cref="ISyncPolicy"/> or <see cref="ISyncPolicy{TResult}"/>.
/// </summary>
public static class ISyncPolicyPolicyWrapExtensions
{
    /// <summary>
    /// Wraps the specified outer policy round the inner policy.
    /// </summary>
    /// <param name="outerPolicy">The outer policy.</param>
    /// <param name="innerPolicy">The inner policy.</param>
    /// <returns>A <see cref="PolicyWrap"/> instance representing the combined wrap.</returns>
    public static PolicyWrap Wrap(this ISyncPolicy outerPolicy, ISyncPolicy innerPolicy)
    {
        if (outerPolicy == null)
        {
            throw new ArgumentNullException(nameof(outerPolicy));
        }

        return ((Policy)outerPolicy).Wrap(innerPolicy);
    }

```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm AI 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBREVNTy1Qb2xseSUzQSUzQXN3aW1taW8=" repo-name="DEMO-Polly"><sup>Powered by [Swimm](/)</sup></SwmMeta>
