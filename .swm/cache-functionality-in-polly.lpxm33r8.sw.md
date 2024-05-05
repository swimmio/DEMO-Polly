---
title: Cache Functionality in Polly
---
This document will cover the topic of 'Cache' in the DEMO-Polly project. We'll cover:

1. What 'Cache' in src/Polly/Caching are
2. The main classes/functions/files that are related to 'Cache' in src/Polly/Caching.

# What is 'Cache' in src/Polly/Caching

In the context of the DEMO-Polly project, 'Cache' refers to the caching functionality provided by the Polly library. This functionality is primarily implemented in the `CachePolicy` class, which is a policy that can be applied to the results of delegate executions. It uses an `ISyncCacheProvider` to provide cache functionality and an `ITtlStrategy` to determine the time-to-live (TTL) of cache entries. The `CachePolicy` class also uses various delegates to handle cache gets, puts, misses, and errors.

<SwmSnippet path="/src/Polly/Caching/CachePolicy.cs" line="4">

---

# Main classes/functions/files related to 'Cache'

The `CachePolicy` class is the main class that implements the caching functionality. It uses an `ISyncCacheProvider` to provide cache functionality, an `ITtlStrategy` to determine the TTL of cache entries, and various delegates to handle cache gets, puts, misses, and errors.

```c#
/// <summary>
/// A cache policy that can be applied to the results of delegate executions.
/// </summary>
public class CachePolicy : Policy, ICachePolicy
{
    private readonly ISyncCacheProvider _syncCacheProvider;
    private readonly ITtlStrategy _ttlStrategy;
    private readonly Func<Context, string> _cacheKeyStrategy;

    private readonly Action<Context, string> _onCacheGet;
    private readonly Action<Context, string> _onCacheMiss;
    private readonly Action<Context, string> _onCachePut;
    private readonly Action<Context, string, Exception>? _onCacheGetError;
    private readonly Action<Context, string, Exception>? _onCachePutError;

    internal CachePolicy(
        ISyncCacheProvider syncCacheProvider,
        ITtlStrategy ttlStrategy,
        Func<Context, string> cacheKeyStrategy,
        Action<Context, string> onCacheGet,
        Action<Context, string> onCacheMiss,
```

---

</SwmSnippet>

<SwmSnippet path="/src/Polly/Caching/CacheSyntax.cs" line="4">

---

The `CacheSyntax` class provides methods for building `CachePolicy` instances. It provides various overloads for the `Cache` method, allowing for different configurations of cache providers, TTL strategies, and cache key strategies.

```c#
public partial class Policy
{
    /// <summary>
    /// <para>Builds a <see cref="Policy"/> that will function like a result cache for delegate executions returning a result.</para>
    /// <para>Before executing a delegate returning a result, checks whether the <paramref name="cacheProvider"/> holds a value for the cache key specified by <see cref="Context.OperationKey"/>.
    /// If the <paramref name="cacheProvider"/> provides a value from cache, returns that value and does not execute the governed delegate.  If the <paramref name="cacheProvider"/> does not provide a value, executes the governed delegate, stores the value with the <paramref name="cacheProvider"/>, then returns the value.
    /// </para>
    /// </summary>
    /// <param name="cacheProvider">The cache provider.</param>
    /// <param name="ttl">Duration (ttl) for which to cache values.</param>
    /// <param name="onCacheError">Delegate to call if an exception is thrown when attempting to get a value from or put a value into the cache, passing the execution context, the cache key, and the exception.</param>
    /// <returns>The policy instance.</returns>
    /// <exception cref="ArgumentNullException">Thrown when <paramref name="cacheProvider"/> is <see langword="null"/>.</exception>
    public static CachePolicy Cache(ISyncCacheProvider cacheProvider, TimeSpan ttl, Action<Context, string, Exception>? onCacheError = null) =>
        Cache(cacheProvider, new RelativeTtl(ttl), DefaultCacheKeyStrategy.Instance.GetCacheKey, onCacheError);

    /// <summary>
    /// <para>Builds a <see cref="Policy" /> that will function like a result cache for delegate executions returning a result.</para>
    /// <para>Before executing a delegate returning a result, checks whether the <paramref name="cacheProvider"/> holds a value for the cache key specified by <see cref="Context.OperationKey"/>.
    /// If the <paramref name="cacheProvider"/> provides a value from cache, returns that value and does not execute the governed delegate.  If the <paramref name="cacheProvider"/> does not provide a value, executes the governed delegate, stores the value with the <paramref name="cacheProvider"/>, then returns the value.
    /// </para>
```

---

</SwmSnippet>

<SwmSnippet path="/src/Polly/Caching/CacheEngine.cs" line="4">

---

The `CacheEngine` class is a helper class used by `CachePolicy` to implement the caching functionality. It provides the `Implementation` method, which performs the actual caching logic.

```c#
internal static class CacheEngine
{
    internal static TResult Implementation<TResult>(
        ISyncCacheProvider<TResult> cacheProvider,
        ITtlStrategy<TResult> ttlStrategy,
        Func<Context, string> cacheKeyStrategy,
        Func<Context, CancellationToken, TResult> action,
        Context context,
        CancellationToken cancellationToken,
        Action<Context, string> onCacheGet,
        Action<Context, string> onCacheMiss,
        Action<Context, string> onCachePut,
        Action<Context, string, Exception>? onCacheGetError,
        Action<Context, string, Exception>? onCachePutError)
    {
        cancellationToken.ThrowIfCancellationRequested();

        string cacheKey = cacheKeyStrategy(context);
        if (cacheKey == null)
        {
            return action(context, cancellationToken);
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm AI 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBREVNTy1Qb2xseSUzQSUzQXN3aW1taW8=" repo-name="DEMO-Polly"><sup>Powered by [Swimm](/)</sup></SwmMeta>
