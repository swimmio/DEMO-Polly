---
title: CircuitBreaker Overview
---
This document will cover the following aspects of the CircuitBreaker in the DEMO-Polly repo:

1. What is the CircuitBreaker?
2. Main classes and functions related to the CircuitBreaker.

# What is the CircuitBreaker?

The CircuitBreaker is a resilience strategy in the DEMO-Polly library. It is designed to prevent an application from performing an operation that's likely to fail. If the number of failures crosses a certain threshold, the circuit breaker trips, and for the duration of a timeout period, all attempts to invoke the operation are returned with an error immediately. After the timeout period, the circuit breaker allows a limited number of test requests to pass through. If those requests succeed, the circuit breaker resumes normal operation. Otherwise, it re-trips the circuit.

<SwmSnippet path="/src/Polly/CircuitBreaker/CircuitBreakerPolicy.cs" line="3">

---

# Main classes and functions related to the CircuitBreaker

The `CircuitBreakerPolicy` class is the main class that implements the CircuitBreaker functionality. It contains methods like `Isolate` and `Reset` to control the state of the circuit, and properties like `CircuitState` and `LastException` to get the state of the circuit and the last exception that was handled by the circuit breaker.

```c#
/// <summary>
/// A circuit-breaker policy that can be applied to delegates.
/// </summary>
public class CircuitBreakerPolicy : Policy, ICircuitBreakerPolicy
{
    internal readonly ICircuitController<EmptyStruct> _breakerController;

    internal CircuitBreakerPolicy(
        PolicyBuilder policyBuilder,
        ICircuitController<EmptyStruct> breakerController)
        : base(policyBuilder) =>
        _breakerController = breakerController;

    /// <summary>
    /// Gets the state of the underlying circuit.
    /// </summary>
    public CircuitState CircuitState => _breakerController.CircuitState;

    /// <summary>
    /// Gets the last exception handled by the circuit-breaker.
    /// <remarks>This will be null if no exceptions have been handled by the circuit-breaker since the circuit last closed.</remarks>
```

---

</SwmSnippet>

<SwmSnippet path="/src/Polly/CircuitBreaker/CircuitBreakerSyntax.cs" line="3">

---

The `CircuitBreakerSyntax` class provides a fluent API for defining a Circuit Breaker policy. It contains methods to build a policy that will function like a Circuit Breaker, specifying parameters like the number of exceptions allowed before breaking the circuit, the duration the circuit will stay open before resetting, and the actions to call when the circuit transitions to different states.

```c#
/// <summary>
/// Fluent API for defining a Circuit Breaker <see cref="Policy"/>.
/// </summary>
public static class CircuitBreakerSyntax
{
    /// <summary>
    /// <para> Builds a <see cref="Policy"/> that will function like a Circuit Breaker.</para>
    /// <para>The circuit will break if <paramref name="exceptionsAllowedBeforeBreaking"/>
    /// exceptions that are handled by this policy are raised consecutively. </para>
    /// <para>The circuit will stay broken for the <paramref name="durationOfBreak"/>. Any attempt to execute this policy
    /// while the circuit is broken, will immediately throw a <see cref="BrokenCircuitException"/> containing the exception
    /// that broke the circuit.
    /// </para>
    /// <para>If the first action after the break duration period results in a handled exception, the circuit will break
    /// again for another <paramref name="durationOfBreak"/>; if no exception is thrown, the circuit will reset.
    /// </para>
    /// </summary>
    /// <param name="policyBuilder">The policy builder.</param>
    /// <param name="exceptionsAllowedBeforeBreaking">The number of exceptions that are allowed before opening the circuit.</param>
    /// <param name="durationOfBreak">The duration the circuit will stay open before resetting.</param>
    /// <returns>The policy instance.</returns>
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm AI 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBREVNTy1Qb2xseSUzQSUzQXN3aW1taW8=" repo-name="DEMO-Polly"><sup>Powered by [Swimm](/)</sup></SwmMeta>
