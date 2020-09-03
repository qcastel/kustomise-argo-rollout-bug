# Bug in argo Rollout manifest with Kustomise

The Argo Rollout containers configuration is not merging the overlay content but instead override the all container configuration.

## How to reproduce

```
kustomize build ./
```

## Expected result

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Rollout
metadata:
  labels:
    app: alice
  name: alice-Rollout
spec:
  selector:
    matchLabels:
      app: alice
  strategy:
    canary:
      canaryService: alice-canary
      stableService: alice-stable
      trafficRouting:
        smi: {}
  template:
    metadata:
      annotations:
        linkerd.io/inject: enabled
      labels:
        app: alice
    spec:
      containers:
        - name: alice
          image: alice:0.0.1
          ports:
            - name: http-server
              containerPort: 8080
          env:
            - name: spring.application.name
              value: Alice
            - name: foo
              value: bar
```
## Current result

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Rollout
metadata:
  labels:
    app: alice
  name: alice-Rollout
spec:
  selector:
    matchLabels:
      app: alice
  strategy:
    canary:
      canaryService: alice-canary
      stableService: alice-stable
      trafficRouting:
        smi: {}
  template:
    metadata:
      annotations:
        linkerd.io/inject: enabled
      labels:
        app: alice
    spec:
      containers:
      - env:
        - name: foo
          value: bar
        name: alice
```