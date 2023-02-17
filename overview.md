# The GlueOps Platform

![Alt text](GlueOpsPlatformOverview.jpeg?raw=true "GlueOps Platform Overview")

#### The GlueOps Platform enables a seamless, GitOps driven workflow for deploying production and staging applications.  The platform also creates ephemeral, preview environments that enable developers to explore their running applications before their changes are merged to the main branch or deployed to production.

----

## Platform Components
1. `GlueOps Platform` – Central orchestration point of GlueOps services.  The Platform uses webhooks to monitor repository activity and updates application deployments and infrastructure based on changes within repositories.
2. **Repositories** – Application deployments are managed via a central, `application-stack` repository and those applications can source from any number of repositories containing application code.
  - `application-stack` repository – This repository configures `production` and `non-production` deployments, as well as generates ephemeral, preview environments.
  - `application-repository` – The GlueOps Platform supports any number of application repositories.  Each repository **_must_** contain a `Dockerfile`, which defines the application build, and automation to build new images upon `push` git actions and publishes those images to the container_registry .
3.  `Container Registry` – Any container registry and the destination for builds from `application-repositories`.
4. `Encrypted Secret Store` – An encrypted store for secret values.  GlueOps provides an encrypted secret store with automated backups and can hook into common secret store options, like AWS Secrets Manager.
5. **Kubernetes Clusters** – The GlueOps platform manages kubernetes clusters to deploy applications and services.  While the GlueOps Platform supports an arbitrary number of clusters, GlueOps recommends at least separating workloads by deploying `production` and `non-prod` applications to different clusters.
6. **Metrics**, **Logs**, and **Alerts** – The GlueOps Platform automatically collects logs from Kubernetes and deployed applications as well as offers dashboards for monitoring various services.  In addition, custom alerts can be provisioned to alert for aberrant service behavior that requires developer or operations remediation.
