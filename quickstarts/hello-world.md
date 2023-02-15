## Deploying your first Hello World Application

Requirements:
- Access to your application Stack repository
- Your glueopshosted apps cluster domain (ex. apps.nonprod.antoniostacos.glueopshosted.com)

1. Go to your application stack repository. This is configured at the time of your cluster setup.
2. Copy this template file below and save it as: `hello-world-web-app.yaml`

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: hello-world-web-app
  namespace: glueops-core
  finalizers:
    - resources-finalizer.argocd.argoproj.io
spec:
  destination:
    namespace: development
    server: https://kubernetes.default.svc
  project: development
  source:
    repoURL: 'https://helm.gpkg.io/project-template'
    targetRevision: 0.1.0
    helm:
      parameters:
        - name: domain
          value: REPLACE_THIS_VALUE
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
    retry:
      backoff:
        duration: 5s
        factor: 2
        maxDuration: 3m0s
      limit: 2
    syncOptions:
    - CreateNamespace=true
```

The one change you will need to make is updating: `REPLACE_THIS_VALUE` to use your `apps` cluster domain. For this example, we will assume "apps.nonprod.antoniostacos.glueopshosted.com" So change the value to something like: `hello-world.apps.nonprod.antoniostacos.glueopshosted.com` and then save the file and check it into the repository. Within seconds you should see the app running at: https://hello-world.apps.nonprod.antoniostacos.glueopshosted.com