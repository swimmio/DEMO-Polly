---
title: Investigating the Performance and Resilience Benefits of Using NoOp
---
This document will cover the topic of NoOp in Polly, including:

1. What is NoOp?
2. How is NoOp used in the codebase?
3. The advantages of using NoOp in terms of performance and resilience.

<SwmSnippet path="/src/Polly/NoOp/NoOpSyntax.cs" line="7">

---

# What is NoOp?

NoOp is a policy in Polly that executes without any custom behavior. It is defined in the `NoOpSyntax.cs` file.

```c#
    /// <summary>
    /// Builds a NoOp <see cref="Policy"/> that will execute without any custom behavior.
    /// </summary>
    /// <returns>The policy instance.</returns>
    public static NoOpPolicy NoOp() => new();
```

---

</SwmSnippet>

# Advantages of NoOp in terms of performance and resilience

Polly is designed to be fast and avoid allocations wherever possible. NoOp, as a policy that executes without any custom behavior, contributes to this performance efficiency. It does not add any overhead to the execution, thus maintaining the application's performance. In terms of resilience, NoOp can be used as a placeholder or default policy in a resilience strategy, ensuring that the application can always execute the action, even when no specific resilience policy is defined.

&nbsp;

*This is an auto-generated document by Swimm AI 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBREVNTy1Qb2xseSUzQSUzQXN3aW1taW8=" repo-name="DEMO-Polly"><sup>Powered by [Swimm](/)</sup></SwmMeta>
