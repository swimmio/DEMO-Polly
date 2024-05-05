---
title: Testing Strategies and Patterns for the Fallback Feature
---
This document will cover the testing strategies or patterns used for the Fallback feature in the DEMO-Polly repo. We'll cover:

1. The Fallback feature's implementation
2. The usage of FallbackStrategyOptions
3. The testing strategies used in the Fallback feature.

<SwmSnippet path="/src/Polly.Core/Fallback/FallbackResilienceStrategy.cs" line="8">

---

# Fallback Feature Implementation

The `FallbackResilienceStrategy` class in `FallbackResilienceStrategy.cs` is the core of the Fallback feature. It uses a `FallbackHandler` to determine whether the fallback should be executed for a given outcome and executes the fallback action if necessary.

```c#
{
    private readonly FallbackHandler<T> _handler;
    private readonly Func<OnFallbackArguments<T>, ValueTask>? _onFallback;
    private readonly ResilienceStrategyTelemetry _telemetry;

    public FallbackResilienceStrategy(FallbackHandler<T> handler, Func<OnFallbackArguments<T>, ValueTask>? onFallback, ResilienceStrategyTelemetry telemetry)
    {
        _handler = handler;
        _onFallback = onFallback;
        _telemetry = telemetry;
    }

    protected internal override async ValueTask<Outcome<T>> ExecuteCore<TState>(Func<ResilienceContext, TState, ValueTask<Outcome<T>>> callback, ResilienceContext context, TState state)
    {
```

---

</SwmSnippet>

<SwmSnippet path="/src/Snippets/Docs/Fallback.cs" line="10">

---

# Usage of FallbackStrategyOptions

The `FallbackStrategyOptions` class is used to configure the fallback resilience strategy. It is used in the `Usage` method in `Fallback.cs` to define different fallback strategies, such as using a substitute value, a dynamically generated value, or executing an additional action if the fallback is triggered.

```c#
    public static void Usage()
    {
        #region fallback

        // A fallback/substitute value if an operation fails.
        var optionsSubstitute = new FallbackStrategyOptions<UserAvatar>
        {
            ShouldHandle = new PredicateBuilder<UserAvatar>()
                .Handle<SomeExceptionType>()
                .HandleResult(r => r is null),
            FallbackAction = static args => Outcome.FromResultAsValueTask(UserAvatar.Blank)
        };

        // Use a dynamically generated value if an operation fails.
        var optionsFallbackAction = new FallbackStrategyOptions<UserAvatar>
        {
            ShouldHandle = new PredicateBuilder<UserAvatar>()
                .Handle<SomeExceptionType>()
                .HandleResult(r => r is null),
            FallbackAction = static args =>
            {
```

---

</SwmSnippet>

<SwmSnippet path="/test/Polly.Core.Tests/Fallback/FallbackResilienceStrategyTests.cs" line="1">

---

# Testing Strategies for the Fallback Feature

The `FallbackResilienceStrategyTests.cs` file contains tests for the Fallback feature. It tests different scenarios and validates the behavior of the Fallback feature under these scenarios.

```c#
using Polly.Fallback;
using Polly.Telemetry;

namespace Polly.Core.Tests.Fallback;

public class FallbackResilienceStrategyTests
{
    private readonly FallbackStrategyOptions<string> _options = new();
    private readonly List<TelemetryEventArguments<object, object>> _args = [];
    private readonly ResilienceStrategyTelemetry _telemetry;
    private FallbackHandler<string>? _handler;

    public FallbackResilienceStrategyTests() => _telemetry = TestUtilities.CreateResilienceTelemetry(_args.Add);

    [Fact]
    public void Ctor_Ok() =>
        Create().Should().NotBeNull();

    [Fact]
    public void Handle_Result_Ok()
    {
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm AI 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBREVNTy1Qb2xseSUzQSUzQXN3aW1taW8=" repo-name="DEMO-Polly"><sup>Powered by [Swimm](/)</sup></SwmMeta>
