# CICD FLow

1. Export API from API Manager

2. Generate deployment repo using apictl.

3. Commit both source repo and deployment repo.

## Source Repo

└── Online-Store-v1.0.0
    └── Meta-information
        ├── api.yaml
        └── swagger.yaml

## Deployment Repo

└── Online-Store-v1.0.0
    ├── api-meta.yaml
    ├── certs
    └── params.yaml


# Jenkins Flow (VM)

1. Adding new entry to the deployment

- Identify Online-Store-v1.0.0 is added/updated newly (Based on commit artifacts)
- Read api-meta.yaml - It has relevant versions, url of the artifact
- Execute following commands to deploy

apictl import api --from-file=online_store_v1_r1.zip --params params.yaml --certs ./certs

2. Updating an existing entry

- Identify Online-Store-v1.0.0 is added/updated newly (Based on commit artifacts)
- Read api-meta.yaml - It has relevant versions, url of the artifact
- Execute following commands to deploy

apictl import api --from-file=online_store_v1_r1.zip --params params.yaml --certs ./certs --update


## Deployment Repo (K8s)

└── Online-Store-v1.0.0_k8s
    ├── api-meta.yaml
    ├── api_crd.yaml
    ├── certs
    └── params_cm.yaml


# Jenkins Flow (K8s)

1. Adding new entry to the deployment

- Identify Online-Store-v1.0.0 is added/updated newly (Based on commit artifacts)
- Read api-meta.yaml - It has relevant versions, url of the artifact
- Get source zip from remote location
- Read configmap name from api_crd.yaml and then create a configmap with that name
- Deploy params_cm configmap
- Deploy certs configmaps
- Deploy api_crd.yaml

2. Updating an existing entry

- Identify Online-Store-v1.0.0 is added/updated newly (Based on commit artifacts)

## Update different source zip / params/ certs : Option 1 - update timestamp

- Commit to source repo
- Change api-meta.yaml and api_crd.yaml (update timestamp), commit to deployment repo
- Read api-meta.yaml - It has relevant versions, url of the artifact
- Get source zip from remote location
- Read configmap name from api_crd.yaml and then delete that cm, create a new configmap with that name
- Deploy params_cm configmap
- Deploy certs configmaps
- Deploy api_crd.yaml

## Update different source zip / params/ certs : Option 2 - API Operator watcher

- Commit to source repo
- Change api-meta.yaml and commit to deployment repo
- Read api-meta.yaml - It has relevant versions, url of the artifact
- Get source zip from remote location
- Read configmap name from api_crd.yaml and then delete that cm, create a new configmap with that name
- API operator watches the configmaps and do the rolling update

## Update different source zip / params/ certs : Option 3 - New names for configmaps

- Commit to source repo
- Change api-meta.yaml and commit to deployment repo
- Read api-meta.yaml - It has relevant versions, url of the artifact
- Get source zip from remote location
- Use kubectl get api foo and identify the configmap names used.
- Create new configmaps with a different name for artifacts
- Update api-crd.yaml with those names and reapply
- Delete previous identified configmap names

## Update different source zip / params/ certs : Option 4 - kustomize

- Commit to source repo
- Change api-meta.yaml and commit to deployment repo
- Read api-meta.yaml - It has relevant versions, url of the artifact
- Get source zip from remote location
- Use kubectl get api foo and identify the configmap names used.
- Create new configmaps with a different name for artifacts
- Update api-crd.yaml with those names and reapply
- Delete previous identified configmap names


