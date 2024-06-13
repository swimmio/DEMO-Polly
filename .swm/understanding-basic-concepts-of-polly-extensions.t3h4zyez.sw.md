---
title: Understanding Basic Concepts of Polly Extensions
---
Polly Extensions in DEMO-Polly refers to a set of extension methods and classes that enhance the functionality of the Polly library. These extensions provide additional features such as dependency injection and telemetry support. For instance, the `PollyServiceCollectionExtensions` class provides methods for registering resilience pipelines using the `IServiceCollection`. These pipelines handle different types of results and can be identified using a key. The extensions also include a `RegistryMarker` class and methods for adding resilience pipelines to a service collection. The `Polly.Extensions` project is configured to target multiple .NET frameworks and includes references to several other projects and packages.

<SwmSnippet path="/src/Polly.Extensions/DependencyInjection/PollyServiceCollectionExtensions.cs" line="13">

---

# Registering Resilience Pipelines

This section of the `PollyServiceCollectionExtensions` class shows how to add a resilience pipeline that handles a specific type of result to the service collection. The `AddResiliencePipeline` method takes in the service collection, a key to identify the pipeline, and an action to configure the pipeline.

```c#
/// <summary>
/// Provides extension methods for registering resilience pipelines using the <see cref="IServiceCollection"/>.
/// </summary>
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
```

---

</SwmSnippet>

<SwmSnippet path="/src/Polly.Extensions/DependencyInjection/PollyServiceCollectionExtensions.cs" line="57">

---

# Retrieving Resilience Pipelines

This section of the `PollyServiceCollectionExtensions` class shows how to retrieve the registered pipeline by resolving the `ResiliencePipelineProvider` class from the dependency injection container. This allows the registered resilience pipeline to be used throughout the application.

```c#
    /// You can retrieve the registered pipeline by resolving the <see cref="ResiliencePipelineProvider{TKey}"/> class from the dependency injection container.
    /// <para>
    /// This call enables telemetry for the registered resilience pipeline.
    /// </para>
    /// </remarks>
    public static IServiceCollection AddResiliencePipeline<TKey, TResult>(
        this IServiceCollection services,
        TKey key,
        Action<ResiliencePipelineBuilder<TResult>, AddResiliencePipelineContext<TKey>> configure)
        where TKey : notnull
    {
        Guard.NotNull(services);
        Guard.NotNull(configure);

        services.TryAddKeyedTransient(
            key,
            (serviceProvider, key) =>
            {
                var pipelineProvider = serviceProvider.GetRequiredService<ResiliencePipelineProvider<TKey>>();

                return pipelineProvider.GetPipeline<TResult>((TKey)key!);
```

---

</SwmSnippet>

# Polly Extensions Functions

The Polly.Extensions directory in the DEMO-Polly repository contains several important functions that are used to add and configure resilience pipelines. These functions include AddResiliencePipeline, AddResiliencePipelines, and AddResiliencePipelineRegistry.

<SwmSnippet path="/src/Polly.Extensions/DependencyInjection/PollyServiceCollectionExtensions.cs" line="18">

---

## AddResiliencePipeline

The `AddResiliencePipeline` function is a method in the `PollyServiceCollectionExtensions` class. It is used to add a resilience pipeline that handles a specific type of result to the service collection. The resilience pipeline is identified by a key, and the function takes an action that configures the resilience pipeline as a parameter. This function also enables telemetry for the registered resilience pipeline.

```c#
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
        Action<ResiliencePipelineBuilder<TResult>> configure)
        where TKey : notnull
```

---

</SwmSnippet>

<SwmSnippet path="/src/Polly.Extensions/DependencyInjection/PollyServiceCollectionExtensions.cs" line="152">

---

## AddResiliencePipelines

The `AddResiliencePipelines` function is another method in the `PollyServiceCollectionExtensions` class. This function allows for the deferred addition of one or more resilience pipelines to the service collection. It takes an action that allows configuration of resilience pipelines as a parameter. Like the `AddResiliencePipeline` function, this function also enables telemetry for the registered resilience pipeline.

```c#
    /// <summary>
    /// Allows deferred addition of one or more resilience pipelines to the service collection.
    /// </summary>
    /// <typeparam name="TKey">The type of the key used to identify the resilience pipelines.</typeparam>
    /// <param name="services">The <see cref="IServiceCollection"/> to add resilience pipelines to.</param>
    /// <param name="configure">An action that allows configuration of resilience pipelines.</param>
    /// <returns>The updated <see cref="IServiceCollection"/> with the addition configuration added.</returns>
    /// <exception cref="ArgumentNullException">Thrown when <paramref name="services"/> or <paramref name="configure"/> is <see langword="null"/>.</exception>
    /// <remarks>
    /// This method can be useful if you want to add resilience pipelines as late as possible, e.g.
    /// to allow other services / configuration to be available before providing the key.
    /// <para>
    /// You can retrieve the registered pipeline by resolving the <see cref="ResiliencePipelineProvider{TKey}"/> class from the dependency injection container.
    /// </para>
    /// <para>
    /// This call enables telemetry for the registered resilience pipeline.
    /// </para>
    /// </remarks>
    public static IServiceCollection AddResiliencePipelines<TKey>(
        this IServiceCollection services,
        Action<AddResiliencePipelinesContext<TKey>> configure)
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm AI 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBREVNTy1Qb2xseSUzQSUzQXN3aW1taW8=" repo-name="DEMO-Polly"><sup>Powered by [Swimm](/)</sup></SwmMeta>
