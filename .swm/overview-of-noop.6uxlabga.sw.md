---
title: Overview of NoOp
---
This document will cover the topic of NoOp in the DEMO-Polly repo. We'll cover:

1. What NoOp is and where it is implemented
2. The main classes/functions/files related to NoOp

<SwmSnippet path="/src/Polly/NoOp/NoOpSyntax.cs" line="7">

---

# What is NoOp and where is it implemented

NoOp is a method in the `Policy` class that builds a NoOp `Policy` that will execute without any custom behavior. It is implemented in the `NoOpSyntax.cs` file.

```c#
    /// <summary>
    /// Builds a NoOp <see cref="Policy"/> that will execute without any custom behavior.
    /// </summary>
    /// <returns>The policy instance.</returns>
    public static NoOpPolicy NoOp() => new();
```

---

</SwmSnippet>

<SwmSnippet path="/src/Polly/NoOp/NoOpPolicy.cs" line="5">

---

# Main classes/functions/files related to NoOp

The `NoOpPolicy` class is a no op policy that can be applied to delegates. It uses the `NoOpEngine` for its implementation.

```c#
/// <summary>
/// A no op policy that can be applied to delegates.
/// </summary>
public class NoOpPolicy : Policy, INoOpPolicy
{
    internal NoOpPolicy()
    {
    }

    /// <inheritdoc/>
    [DebuggerStepThrough]
    protected override TResult Implementation<TResult>(Func<Context, CancellationToken, TResult> action, Context context, CancellationToken cancellationToken) =>
        NoOpEngine.Implementation(action, context, cancellationToken);
}
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm AI 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBREVNTy1Qb2xseSUzQSUzQXN3aW1taW8=" repo-name="DEMO-Polly"><sup>Powered by [Swimm](/)</sup></SwmMeta>
