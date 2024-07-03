---
title: Understanding the Basic Concepts of Polly Core
---
Polly Core is the heart of the Polly library, a .NET resilience and transient-fault-handling library. It provides the core functionality and abstractions for expressing resilience strategies such as Retry, Circuit Breaker, Hedging, Timeout, Chaos, and Fallback. These strategies are expressed in a fluent and thread-safe manner. Polly Core also includes utilities for handling exceptions, tasks, and other common scenarios. It also provides support for legacy versions of Polly, allowing for smooth transitions and backward compatibility.

<SwmSnippet path="/src/Polly.Core/Polly.Core.csproj" line="1">

---

## Polly Core Structure

The `Polly.Core.csproj` file is the project file for Polly Core. It defines the target frameworks, assembly title, and other properties. It also includes references to other packages that Polly Core depends on.

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <TargetFrameworks>net8.0;net6.0;netstandard2.0;net472;net462</TargetFrameworks>
    <AssemblyTitle>Polly.Core</AssemblyTitle>
    <RootNamespace>Polly</RootNamespace>
    <Nullable>enable</Nullable>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <ProjectType>Library</ProjectType>
    <MutationScore>100</MutationScore>
    <LegacySupport>true</LegacySupport>
    <InjectSharedSources>true</InjectSharedSources>
  </PropertyGroup>
  <PropertyGroup Condition="$([MSBuild]::IsTargetFrameworkCompatible('$(TargetFramework)', 'net8.0'))">
    <IsAotCompatible>true</IsAotCompatible>
  </PropertyGroup>

  <PropertyGroup>
    <Description>Polly.Core is a .NET resilience and transient-fault-handling library that allows developers to express resilience strategies such as Retry, Circuit Breaker, Hedging, Timeout, Chaos and Fallback in a fluent and thread-safe manner.</Description>
    <PackageTags>Polly Simmy Exception Handling Resilience Transient Fault Policy Circuit Breaker CircuitBreaker Chaos ChaosEngineering Retry Wait Fallback Timeout Parallelization Hedging</PackageTags>
  </PropertyGroup>
```

---

</SwmSnippet>

<SwmSnippet path="/src/Polly.Core/StrategyBuilderContext.cs" line="1">

---

## Utilizing Polly Core

The `StrategyBuilderContext` class is an example of how Polly Core is used. It provides a context for building resilience strategies, including telemetry for reporting important events and a TimeProvider for strategy timing.

```c#
using Polly.Telemetry;

namespace Polly;

/// <summary>
/// The context used for building an individual resilience strategy.
/// </summary>
public sealed class StrategyBuilderContext
{
    internal StrategyBuilderContext(ResilienceStrategyTelemetry telemetry, TimeProvider timeProvider)
    {
        TimeProvider = timeProvider;
        Telemetry = telemetry;
    }

    /// <summary>
    /// Gets the resilience telemetry used to report important events.
    /// </summary>
    public ResilienceStrategyTelemetry Telemetry { get; }

    /// <summary>
```

---

</SwmSnippet>

# Polly Core Functions

This section provides an overview of the main functions in Polly Core.

<SwmSnippet path="/src/Polly.Core/ResiliencePipeline.cs" line="1">

---

## ResiliencePipeline

The `ResiliencePipeline` is a core component of Polly. It is used to execute user-provided callbacks. It supports various types of callbacks and provides a unified way to execute them. This includes overloads for synchronous and asynchronous callbacks, generic and non-generic callbacks.

```c#
using Polly.Utils.Pipeline;

namespace Polly;

/// <summary>
/// Resilience pipeline is used to execute the user-provided callbacks.
/// </summary>
/// <remarks>
/// Resilience pipeline supports various types of callbacks and provides a unified way to execute them.
/// This includes overloads for synchronous and asynchronous callbacks, generic and non-generic callbacks.
/// </remarks>
public sealed partial class ResiliencePipeline
{
    /// <summary>
    /// Resilience pipeline that executes the user-provided callback without any additional logic.
    /// </summary>
    public static readonly ResiliencePipeline Empty = new(PipelineComponent.Empty, DisposeBehavior.Ignore, null);

    internal ResiliencePipeline(PipelineComponent component, DisposeBehavior disposeBehavior, ResilienceContextPool? pool)
    {
        Component = component;
```

---

</SwmSnippet>

<SwmSnippet path="/src/Polly.Core/ResilienceStrategy.cs" line="1">

---

## ResilienceStrategy

The `ResilienceStrategy` is an abstract base class for all proactive resilience strategies. It provides a method `ExecuteCore` that executes a user-provided callback. The callback should not throw any exceptions. Instead, it should convert them to an `Outcome`.

```c#
namespace Polly;

/// <summary>
/// Base class for all proactive resilience strategies.
/// </summary>
public abstract class ResilienceStrategy
{
    /// <summary>
    /// An implementation of a proactive resilience strategy that executes the specified <paramref name="callback"/>.
    /// </summary>
    /// <typeparam name="TResult">The type of result returned by the callback.</typeparam>
    /// <typeparam name="TState">The type of state associated with the callback.</typeparam>
    /// <param name="callback">The user-provided callback.</param>
    /// <param name="context">The context associated with the callback.</param>
    /// <param name="state">The state associated with the callback.</param>
    /// <returns>
    /// An instance of a pending <see cref="ValueTask"/> for asynchronous executions or a completed <see cref="ValueTask"/> task for synchronous executions.
    /// </returns>
    /// <remarks>
    /// The provided callback never throws an exception. Instead, the exception is captured and converted to an <see cref="Outcome{TResult}"/>.
    /// Similarly, do not throw exceptions from your strategy implementation. Instead, return an exception instance as <see cref="Outcome{TResult}"/>.
```

---

</SwmSnippet>

<SwmSnippet path="/src/Polly.Core/Outcome.TResult.cs" line="1">

---

## Outcome

The `Outcome` struct represents the outcome of an operation which could be a result of a certain type or an exception. It provides methods to get the result or throw the exception if the operation produced an exception.

```c#
#pragma warning disable CA1815 // Override equals and operator equals on value types

using System.Runtime.ExceptionServices;

namespace Polly;

/// <summary>
/// Represents the outcome of an operation which could be a result of type <typeparamref name="TResult"/> or an exception.
/// </summary>
/// <typeparam name="TResult">The result type of the operation.</typeparam>
/// <remarks>
/// Always use the constructor when creating this struct, otherwise we do not guarantee binary compatibility.
/// </remarks>
public readonly struct Outcome<TResult>
{
    internal Outcome(Exception exception)
        : this() => ExceptionDispatchInfo = ExceptionDispatchInfo.Capture(Guard.NotNull(exception));

    internal Outcome(TResult? result)
        : this() => Result = result;

```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm AI 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBREVNTy1Qb2xseSUzQSUzQXN3aW1taW8=" repo-name="DEMO-Polly"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
