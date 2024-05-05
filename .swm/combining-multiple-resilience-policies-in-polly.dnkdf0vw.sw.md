---
title: Combining Multiple Resilience Policies in Polly
---
This document will cover the process of chaining different resilience policies together in the DEMO-Polly project. We'll cover:

1. How to chain resilience policies
2. Examples of chaining resilience policies.

<SwmSnippet path="/src/Polly/Utilities/Wrappers/ResiliencePipelineSyncPolicy.cs" line="3">

---

# How to chain resilience policies

The `ResiliencePipelineSyncPolicy` class is used to chain resilience policies together. The `_pipeline` field holds the resilience pipeline, which is a sequence of resilience strategies. The `Execute` method is used to execute the resilience pipeline.

```c#
internal sealed class ResiliencePipelineSyncPolicy : Policy
{
    private readonly ResiliencePipeline _pipeline;

    public ResiliencePipelineSyncPolicy(ResiliencePipeline strategy) => _pipeline = strategy;

    protected override void Implementation(Action<Context, CancellationToken> action, Context context, CancellationToken cancellationToken)
    {
        var resilienceContext = ResilienceContextFactory.Create(
            context,
            cancellationToken,
            true,
            out var oldProperties);

        try
        {
            _pipeline.Execute(
                static (context, state) =>
                {
                    state.action(state.context, context.CancellationToken);
                },
```

---

</SwmSnippet>

<SwmSnippet path="/src/Snippets/Docs/ResilienceStrategies.cs" line="13">

---

# Examples of chaining resilience policies

Here is an example of how to chain resilience policies together. The `ResiliencePipelineBuilder` is used to add strategies to the pipeline. In this case, a timeout strategy is added to the pipeline.

```c#
        ResiliencePipeline pipeline = new ResiliencePipelineBuilder()
            .AddTimeout(new TimeoutStrategyOptions
            {
                Timeout = TimeSpan.FromSeconds(5)
            })
            .Build();

        #endregion
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm AI 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBREVNTy1Qb2xseSUzQSUzQXN3aW1taW8=" repo-name="DEMO-Polly"><sup>Powered by [Swimm](/)</sup></SwmMeta>
