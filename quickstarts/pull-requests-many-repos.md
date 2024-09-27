
## Configuring Previews per Pull Request Across Multiple Identical Repos Simultaneously

### Values to replace

- `CAPTAIN_DOMAIN_PLACE_HOLDER`:
- `GITHUB_ORG_NAME_PLACE_HOLDER`: Replace with your GitHub organization name


```yaml
apiVersion: argoproj.io/v1alpha1
kind: ApplicationSet
metadata:
  name: all-repos-previews
  namespace: glueops-core
spec:
  goTemplate: true
  generators:
  - matrix:
      generators:
      - scmProvider:
          cloneProtocol: https
          github:
            allBranches: false
            organization: GITHUB_ORG_NAME_PLACE_HOLDER
            appSecretName: tenant-repo-creds
            
      - pullRequest:
          github:
            owner: GITHUB_ORG_NAME_PLACE_HOLDER
            appSecretName: tenant-repo-creds
            repo: '{{ .repository }}'
          requeueAfterSeconds: 60
  template:
    metadata:
      name: '{{ .branch_slug }}-{{ .repository }}'
      namespace: TENANT_PROJECT_NAME
      annotations:
        repository_organization: "GITHUB_ORG_NAME_PLACE_HOLDER"
        repository_name: '{{ .repository }}'
        pull_request_number: '{{.number}}'
        branch: '{{.branch}}'
        branch_slug: '{{.branch_slug}}'
        head_sha: '{{.head_sha}}'
        head_short_sha: '{{.head_short_sha}}'
      finalizers:
      - resources-finalizer.argocd.argoproj.io
      labels:
        type: pull-request
    spec:
      sources:
        - repoURL: https://incubating-helm.gpkg.io/project-template
          chart: app
          targetRevision: 0.1.0-alpha3
          helm:
            values: |
            
                        image:
                          registry: docker.io
                          repository: httpd
                          tag: latest
                          port: 80
                        
                        service:
                          enabled: true
                        
                        deployment:
                          enabled: true
                          imagePullPolicy: Always
                          resources:
                            requests:
                              cpu: 100m
                              memory: 128Mi
                            limits:
                              cpu: 120m
                              memory: 154Mi
                        
                          livenessProbe:
                            httpGet:
                              path: /
                              port: 80
                          readinessProbe:
                            httpGet:
                              path: /
                              port: 80
            
                        
                        appName: '{{ .branch_slug }}-{{ .repository }}'
                        ingress:
                          enabled: true
                          ingressClassName: public
                          entries:
                            - name: public
                              hosts:
                              - hostname: '{{ .branch_slug }}-{{ .repository }}.CAPTAIN_DOMAIN_PLACE_HOLDER'
        - repoURL: https://github.com/GITHUB_ORG_NAME_PLACE_HOLDER/{{ .repository }}
          targetRevision: '{{ .branch_slug }}'
          ref: values
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
      project: "playground"
      destination:
        server: https://kubernetes.default.svc
        namespace: TENANT_PROJECT_NAME
```
