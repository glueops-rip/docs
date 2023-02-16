## Configuring your first repository to have preview environments on demand

Requirements:
Access to your application Stack repository<br>
Your glueopshosted apps cluster domain (ex. apps.nonprod.antoniostacos.glueopshosted.com)<br>
A repository that publishes a docker image on every commit/push.

1. Go to your application stack repository. This is configured at the time of your cluster setup.
2. Copy this template file below and save it as: `hello-world-preview-environments.yaml`

```yaml
apiVersion: argoproj.io/v1alpha1
kind: ApplicationSet
metadata:
  name: hello-world-preview-environments
  namespace: glueops-core
spec:
  generators:
  - pullRequest:
      github:
        owner: GITHUB_ORG_NAME_PLACE_HOLDER
        # The Github repository
        repo: GITHUB_REPO_PLACE_HOLDER
        tokenRef:
          secretName: git-provider-api-token
          key: token
      requeueAfterSeconds: 60
  template:
    metadata:
      name: 'preview-{{number}}-{{head_short_sha}}'
      annotations:
        pull_request_number: '{{number}}'
        branch: '{{branch}}'
        branch_slug: '{{branch_slug}}'
        head_sha: '{{head_sha}}'
        head_short_sha: '{{head_short_sha}}'
    spec:
    source:
      repoURL: 'https://helm.gpkg.io/project-template'
      targetRevision: 0.1.0
      helm:
        parameters:
          - name: domain
            value: DOMAIN_REPLACE_THIS_VALUE
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
      project: "development"
      destination:
        server: https://kubernetes.default.svc
        namespace: development
```

For `DOMAIN_REPLACE_THIS_VALUE` use your `apps` cluster domain + `preview-{{number}}-{{head_short_sha}}`. Example: preview-{{number}}-`{{head_short_sha}}.apps.nonprod.antoniostacos.glueopshosted.com`
For `GITHUB_ORG_NAME_PLACE_HOLDER` use your github org name. This should be the same github org name that the cluster was deployed in.
For `GITHUB_REPO_PLACE_HOLDER` use the repo of the app code you want preview environments for. This should just be the repo name and not a URL.