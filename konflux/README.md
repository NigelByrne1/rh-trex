# Konflux footnote-demo setup (`nbyrne-tenant`)

## Fix "file does not exist" / invalid integration test

If the error mentions a long string like `Konflux Staging / rh-trex-on-pull-request failed...`, the **Path in repository** field in the UI was filled with pasted log text by mistake.

**Delete** the broken scenario in Konflux UI (or `oc delete integrationtestscenario footnote-demo -n nbyrne-tenant`), then recreate with **exactly** these values:

| Field | Value |
|-------|--------|
| Name | `footnote-demo` |
| Application | `rh-trex` |
| Git URL | `https://github.com/NigelByrne1/rh-trex` |
| Revision | `main` |
| **Resource kind** | `pipelinerun` (required — not `pipeline`) |
| **Path in repository** | `pipelineruns/integration-footnote-demo.yaml` |
| Contexts | `pull-request`, `push` |

Or apply from git (recommended):

```sh
oc apply -f konflux/integration-test-scenario-footnote-demo.yaml
```

## Verify resolver params on cluster

```sh
oc get integrationtestscenario footnote-demo -n nbyrne-tenant \
  -o jsonpath='{range .spec.resolverRef.params[*]}{.name}={.value}{"\n"}{end}'
```

Expected:

```
url=https://github.com/NigelByrne1/rh-trex
revision=main
resourceKind=pipelinerun (in spec.resolverRef)
pathInRepo=pipelineruns/integration-footnote-demo.yaml
```

## Component builds vs integration

- Component **`rh-trex-ab891`** uses `.tekton/rh-trex-ab891-on-pull-request.yaml` (PAC).
- Do **not** use the legacy `rh-trex-on-pull-request` name; that template was removed from this fork to avoid duplicate/conflicting PR pipelines.
