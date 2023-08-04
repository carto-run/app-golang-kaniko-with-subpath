# app-golang-kaniko-with-subpath

## Creating the Workload

```
tanzu apps workload create app-golang-kaniko-with-subpath \
  --namespace dev \
  --git-branch main \
  --git-repo https://github.com/carto-run/app-golang-kaniko-with-subpath \
  --label apps.tanzu.vmware.com/has-tests=true \
  --label app.kubernetes.io/part-of=app-golang-kaniko-with-subpath \
  --param-yaml testing_pipeline_matching_labels='{"apps.tanzu.vmware.com/pipeline":"golang-pipeline"}' \
  --param dockerfile='./Dockerfile' \
  --type web \
  --yes
```

### Golang Pipeline

```
apiVersion: tekton.dev/v1beta1
kind: Pipeline
metadata:
  labels:
    apps.tanzu.vmware.com/pipeline: golang-pipeline
  name: developer-defined-golang-pipeline
  namespace: dev
spec:
  params:
  - name: source-url
    type: string
  - name: source-revision
    type: string
  tasks:
  - name: test
    params:
    - name: source-url
      value: $(params.source-url)
    - name: source-revision
      value: $(params.source-revision)
    taskSpec:
      params:
      - name: source-url
        type: string
      - name: source-revision
        type: string
      steps:
      - image: golang
        name: test
        script: |
          cd `mktemp -d`
          wget -qO- $(params.source-url) | tar xvz -m
          go test ./...
```

## Logs

```
tanzu apps workload tail app-golang-kaniko-with-subpath
```

## Configuration

| Item            | Config                                                                                |
| --------------- | ------------------------------------------------------------------------------------- |
| Scan Policy     | [default](resources/scan-policy.yaml)                                                 |
| Pipeline        | [developer-defined-golang-pipeline](resources/developer-defined-golang-pipeline.yaml) |
| tap-values.yaml | na                                                                                    |
| Supply Chain    | source-test-scan-to-url                                                               |

