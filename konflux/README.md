# Konflux footnote-demo setup (`nbyrne-tenant`)

## Staging git resolver rules

Konflux Staging’s git resolver accepts **`pipeline`** only (Tekton `Pipeline`), not `PipelineRun`.

| Use | Do not use |
|-----|------------|
| Resource kind: **`pipeline`** | `pipelinerun` (error: “must be … Pipeline Task StepAction”) |
| Path: `pipelines/integration-footnote-demo.yaml` | `pipelineruns/integration-footnote-demo.yaml` |

Tasks are **inline** in the Pipeline (no nested `taskRef` git resolver).

## Konflux UI — recreate `footnote-demo`

Delete the scenario and add again:

| Field | Value |
|-------|--------|
| Name | `footnote-demo` |
| Application | `rh-trex` |
| Git URL | `https://github.com/NigelByrne1/rh-trex` |
| Revision | `main` |
| Resource kind | **`pipeline`** |
| Path in repository | `pipelines/integration-footnote-demo.yaml` |
| Contexts | `pull-request`, `push` |

Or:

```sh
oc delete integrationtestscenario footnote-demo -n nbyrne-tenant
oc apply -f konflux/integration-test-scenario-footnote-demo.yaml
```

## Do you need a new PR?

**No**, if the scenario’s **revision** is `main` and the fix is pushed to `main`.

After updating the scenario and pushing the pipeline fix to `main`:

- Re-run integration on the application/snapshot in Konflux UI, **or**
- Push an empty commit to an open PR, **or**
- Open a new PR only if you want a fresh build + integration cycle.

## Verify resolver params

```sh
oc get integrationtestscenario footnote-demo -n nbyrne-tenant \
  -o jsonpath='{.spec.resolverRef}{"\n"}' | jq .
```

Expect `resourceKind: pipeline` and `pathInRepo: pipelines/integration-footnote-demo.yaml`.

## Component builds

Component **`rh-trex-ab891`** uses `.tekton/rh-trex-ab891-on-pull-request.yaml` only.
