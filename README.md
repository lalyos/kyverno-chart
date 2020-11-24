This is a sample Chart to show how to deploy CRDs with helm,
which are containing some go template `{{ }}`.

## Helm issue

Lets say you want to deploy a kyverno ClusterPolicy CR with helm.
So you will have something like this, in the middle of your CR:
```
...
  generate:
    kind: NetworkPolicy
    name: default-deny-ingress
    namespace: "{{request.object.metadata.name}}"
...
```

Now if you just simple copy the yaml into the `templates` dir of you chart, than helm will
try to evaluate those `{{ }}` expressions, and complaining about not understending "request".

So you want helm to ignore it.

## Workaround

The trick is to put the original yaml outside of the `templates` dir for example into `files`,
and your actual template will be a single line:

```
{{- .Files.Get "files/original.yaml" }}
```

This way helm will ignore gotemplates.
