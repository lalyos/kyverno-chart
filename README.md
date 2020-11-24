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

## Use with helm

You can check how helm would render this chart:
```
helm template xxx https://github.com/lalyos/kyverno-chart/archive/master.tar.gz
```

## Use with fleet

If you want to use it [fleet](https://github.com/rancher/fleet/) here is a sample `fleet.yaml`
```
namespace: fleet-helm-example
helm:
  releaseName: kyverno-test
  chart: "https://github.com/lalyos/kyverno-chart/archive/master.zip"
```

To test:
- download it into an empty dir
- run `fleet test`

Selfnote: to install fleet-cli:
```
curl -Lo /usr/local/bin/fleet https://github.com/rancher/fleet/releases/download/v0.3.1/fleet-darwin-amd64
chmod +x /usr/local/bin/fleet
```