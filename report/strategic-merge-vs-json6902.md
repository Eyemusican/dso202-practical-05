# Strategic merge vs JSON 6902

Prod's patch-resources.yaml is a strategic merge patch. It is written as a partial version of the real object, apiVersion, kind, metadata.name to identify which object, then only the fields being changed, resources in this case. Kustomize merges it into the base by matching the shape of the object, so it understands which container inside the Deployment to touch just from the structure.

QA's patch-annotation.yaml is a JSON 6902 patch. It is a list of exact operations, op: add, an exact path like /metadata/annotations, and a value. It does not look like the object at all, it is instructions for editing the object. Because it does not identify the target by shape, the kustomization.yaml has to name the exact target itself under patchesJson6902, group, version, kind, and name.

The difference in practice: strategic merge is easier to read and write for simple field changes, since it looks like the real object. JSON 6902 is more precise, useful for things a merge cannot express cleanly, like adding one specific entry into a map, or when the exact path matters more than the shape.
