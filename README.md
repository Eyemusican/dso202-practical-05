# DSO202 Practical 5: Environment Specific Configuration with Kustomize

This is my work for Practical 5, using Kustomize to manage one shared nginx app across dev, staging, prod, and a qa environment I built myself, without copying the base Deployment and Service for each one.

## Note on the example files

The lab page instructs running commands against examples/webapp, but that folder was never given to me anywhere on VLE, and the page has no link for it. I checked his other pages and searched for it, nothing came up. So the base and all four overlays here are ones I built myself, matching every requirement each task actually states. The app is just plain nginx, since the lab says the learning target is Kustomize, not the app.

## Versions used

- kind cluster, 1 control plane, 2 workers
- kubectl v1.36.0
- kustomize v5.8.1, built into kubectl

## Repo structure

```
dso202-practical-05/
├── README.md
├── examples/
│ └── webapp/
│ ├── base/
│ └── overlays/
│ ├── dev/
│ ├── staging/
│ ├── prod/
│ └── qa/
├── evidence/
└── report/

```


## How to rebuild this

```bash
kubectl create namespace webapp-dev
kubectl apply -k examples/webapp/overlays/dev

kubectl create namespace webapp-staging
kubectl apply -k examples/webapp/overlays/staging

kubectl create namespace webapp-prod
kubectl apply -k examples/webapp/overlays/prod

kubectl apply -k examples/webapp/overlays/qa
```

Dev, staging, and prod need their namespace created first, since the overlay's namespace field only stamps the name onto objects, it doesn't create the namespace itself. QA creates its own namespace automatically since it includes namespace.yaml as an actual resource.

## Cleanup

```bash
kubectl delete -k examples/webapp/overlays/dev
kubectl delete -k examples/webapp/overlays/staging
kubectl delete -k examples/webapp/overlays/prod
kubectl delete -k examples/webapp/overlays/qa
kubectl delete namespace webapp-dev webapp-staging webapp-prod
```

Deleting dev, staging, and prod removes the Deployment, Service, and ConfigMap, but not the namespace, that has to go separately. QA's namespace gets removed automatically with its delete.

## Report

Full writeup with screenshots is in report/practical-05-report.md.
