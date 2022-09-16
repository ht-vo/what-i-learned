# OpenShift - Tips and tricks

Here is a list of tips for OpenShift.

## Tip 01 - How to delete a project stuck in "Terminating" state ?

**Step 1**: Export the project object to a `.json` file

```sh
$ oc get project <project_name> -o json >  <project_name>.json
```

**Step 2**: Replace values in `<project_name>.json` file

```sh
$ jq '.apiVersion="v1" | .kind="Namespace" | .spec.finalizers=[]' \
  <project_name>.json > <project_name>_updated.json
```

**Step 3**: Run a proxy to the API server, in the background

```sh
$ oc proxy --port=8080 &
```

**Step 4**: Call the `finalize` API method to delete the project

```sh
$ curl -k -X PUT \
  -H "Content-Type: application/json" \
  --data-binary @<project_name>_updated.json \
  localhost:8080/api/v1/namespaces/<project_name>/finalize
```
