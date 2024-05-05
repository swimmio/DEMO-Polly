---
title: Fallback Feature Overview
---
This document will cover the following aspects of the Fallback feature in the DEMO-Polly repo:

1. What is Fallback and its purpose
2. Main classes and functions related to Fallback

# What is Fallback and its purpose

Fallback is a resilience strategy provided by the Polly library. It allows developers to specify an alternative action or value to be used when the main execution fails. This is particularly useful in scenarios where a service is temporarily unavailable and you want to provide a default response to prevent the application from failing.

<SwmSnippet path="/src/Polly/Fallback/FallbackPolicy.cs" line="8">

---

# Main classes and functions related to Fallback

The `FallbackPolicy` class is a key component of the Fallback feature. It contains the `_onFallback` action which is executed when the main execution fails. The `FallbackPolicy` class is used to create a Fallback policy.

```c#
public class FallbackPolicy : Policy, IFallbackPolicy
{
    private readonly Action<Exception, Context> _onFallback;
    private readonly Action<Exception, Context, CancellationToken> _fallbackAction;

    internal FallbackPolicy(
        PolicyBuilder policyBuilder,
        Action<Exception, Context> onFallback,
        Action<Exception, Context, CancellationToken> fallbackAction)
        : base(policyBuilder)
    {
        _onFallback = onFallback ?? throw new ArgumentNullException(nameof(onFallback));
        _fallbackAction = fallbackAction ?? throw new ArgumentNullException(nameof(fallbackAction));
    }
```

---

</SwmSnippet>

<SwmSnippet path="/src/Polly/Fallback/FallbackEngine.cs" line="5">

---

The `FallbackEngine` class is responsible for executing the main action and handling any exceptions or results that should trigger a fallback. If a fallback is required, it executes the `_onFallback` action and then the `fallbackAction`.

```c#
internal static class FallbackEngine
{
    internal static TResult Implementation<TResult>(
        Func<Context, CancellationToken, TResult> action,
        Context context,
        CancellationToken cancellationToken,
        ExceptionPredicates shouldHandleExceptionPredicates,
        ResultPredicates<TResult> shouldHandleResultPredicates,
        Action<DelegateResult<TResult>, Context> onFallback,
        Func<DelegateResult<TResult>, Context, CancellationToken, TResult> fallbackAction)
    {
        DelegateResult<TResult> delegateOutcome;

        try
        {
            cancellationToken.ThrowIfCancellationRequested();

            TResult result = action(context, cancellationToken);

            if (!shouldHandleResultPredicates.AnyMatch(result))
            {
```

---

</SwmSnippet>

<SwmSnippet path="/src/Polly/Fallback/FallbackSyntax.cs" line="8">

---

The `FallbackSyntax` class provides a fluent API for defining a Fallback policy. It contains several methods for building a `FallbackPolicy` with different configurations.

```c#
public static class FallbackSyntax
{
    /// <summary>
    /// Builds a <see cref="FallbackPolicy"/> which provides a fallback action if the main execution fails.  Executes the main delegate, but if this throws a handled exception, calls <paramref name="fallbackAction"/>.
    /// </summary>
    /// <param name="policyBuilder">The policy builder.</param>
    /// <param name="fallbackAction">The fallback action.</param>
    /// <exception cref="ArgumentNullException">Thrown when <paramref name="fallbackAction"/> is <see langword="null"/>.</exception>
    /// <returns>The policy instance.</returns>
    public static FallbackPolicy Fallback(this PolicyBuilder policyBuilder, Action fallbackAction)
    {
        if (fallbackAction == null)
        {
            throw new ArgumentNullException(nameof(fallbackAction));
        }

        Action<Exception> doNothing = _ => { };
        return policyBuilder.Fallback(fallbackAction, doNothing);
    }

    /// <summary>
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm AI 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBREVNTy1Qb2xseSUzQSUzQXN3aW1taW8=" repo-name="DEMO-Polly"><sup>Powered by [Swimm](/)</sup></SwmMeta>
