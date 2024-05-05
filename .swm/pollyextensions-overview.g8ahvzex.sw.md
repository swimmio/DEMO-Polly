---
title: Polly.Extensions Overview
---
This document will cover the following aspects of the 'Polly.Extensions' in the DEMO-Polly repo:

1. What 'Polly.Extensions' are and where they are implemented.
2. The main classes/functions/files related to 'Polly.Extensions'.

<SwmSnippet path="/src/Polly.Extensions/Polly.Extensions.csproj" line="1">

---

# What are 'Polly.Extensions' and where are they implemented

`Polly.Extensions` is a project within the DEMO-Polly repo. It contains extension methods for the Polly library, which is a .NET resilience and transient-fault-handling library.

```xml
<Project Sdk="Microsoft.NET.Sdk">
```

---

</SwmSnippet>

<SwmSnippet path="/src/Polly.Extensions/DependencyInjection/PollyServiceCollectionExtensions.cs" line="16">

---

# Main classes/functions/files related to 'Polly.Extensions'

The `PollyServiceCollectionExtensions` class is a key part of 'Polly.Extensions'. It provides extension methods for registering resilience pipelines using the `IServiceCollection`. One of its main methods is `AddResiliencePipeline`, which adds a resilience pipeline that handles a specific type of result to the service collection.

```c#
public static class PollyServiceCollectionExtensions
{
    /// <summary>
    /// Adds a resilience pipeline that handles <typeparamref name="TResult"/> to service collection.
    /// </summary>
    /// <typeparam name="TKey">The type of the key used to identify the resilience pipeline.</typeparam>
    /// <typeparam name="TResult">The type of result that the resilience pipeline handles.</typeparam>
    /// <param name="services">The <see cref="IServiceCollection"/> to add the resilience pipeline to.</param>
    /// <param name="key">The key used to identify the resilience pipeline.</param>
    /// <param name="configure">An action that configures the resilience pipeline.</param>
    /// <returns>The updated <see cref="IServiceCollection"/> with the registered resilience pipeline.</returns>
    /// <exception cref="ArgumentNullException">Thrown when <paramref name="services"/> or <paramref name="configure"/> is <see langword="null"/>.</exception>
    /// <remarks>
    /// You can retrieve the registered pipeline by resolving the <see cref="ResiliencePipelineProvider{TKey}"/> class from the dependency injection container.
    /// <para>
    /// This call enables telemetry for the registered resilience pipeline.
    /// </para>
    /// </remarks>
    public static IServiceCollection AddResiliencePipeline<TKey, TResult>(
        this IServiceCollection services,
        TKey key,
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm AI 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBREVNTy1Qb2xseSUzQSUzQXN3aW1taW8=" repo-name="DEMO-Polly"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
