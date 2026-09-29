# Task 8: What the prod patch changed

Were the base resource values deleted or merged? Merged. The base Deployment's container never had a resources field at all. patch-resources.yaml only adds name: webapp to find the right container, and the resources block. Everything else from the base, image, ports, volume mount, is still there untouched.

Which environment owns the production-specific resource policy? Only prod. Dev and staging's rendered YAML has no resources field at all, confirmed by staging's diff showing resources: {}. Only prod's kustomization.yaml lists the patch.

Why is a patch better than copying deployment.yaml into prod? If prod had its own full copy of deployment.yaml, every future change to the base, a new image tag, a new volume mount, a renamed port, would need to be manually repeated in prod's copy too, and the two files could drift apart over time. With a patch, prod only states the one thing that's actually different, so it inherits every future base change automatically and can never fall out of sync on anything except the resource values.

# Reflection: a real mistake

When I first ran kubectl apply -k on dev, it failed with "namespaces webapp-dev not found". The lab's expected file list for dev, staging, and prod overlays only has index.html and kustomization.yaml, no namespace.yaml, so Kustomize's namespace: field only stamps that name onto every object, it does not create the namespace itself. I had to create the three namespaces manually with kubectl create namespace before the applies would work. This showed up again at cleanup, kubectl delete -k on dev, staging, and prod removed the Deployment, Service, and ConfigMap but left the namespaces behind, since they were never resources Kustomize actually managed. Only the QA overlay, which I built with its own namespace.yaml as a real resource, deleted its namespace cleanly along with everything else. The rendered output caught this every time, once I actually looked at what apply -k was and was not creating.
