# Quarkus Coolstore Pipeline
Deploy a production instance of coolstore on OpenShift using pipelines

## Prerequisites
- Red Hat Pipelines Operator installed on your OpenShift cluster

# Setup
There are a couple things pipelines do not have permissions to do by default, so we'll set them up here.

```
export QUARKUS_NS=foo

oc new-project $QUARKUS_NS

oc adm policy add-scc-to-user anyuid system:serviceaccount:$QUARKUS_NS:rabbitmq-server

cat << EOF | oc create -f -
apiVersion: operators.coreos.com/v1
kind: OperatorGroup
metadata:
  generateName: coolstore-
  namespace: $QUARKUS_NS
spec:
  targetNamespaces:
  - $QUARKUS_NS
  upgradeStrategy: Default
EOF

oc create -f quarkus-pipelines.yml
```

# Use
1. Run the deploy-coolstore-prereqs pipeline. You can accept the default options
1. Run the deploy-coolstore pipeline. You should select volumeClaimTemplate for the workspace. No additional configuration is necessary. You may want to change the repo and branch to whatever you're working on. You can rerun this pipeline as many times as you want to redeploy the app. The deployconfig has an image trigger so it will restart automatically when the new image build is pushed.
1. A route will be create and your coolstore instance will be available at https://coolstore-$namspace.apps.$cluster.$domain
1. Quarkus is configured with the user `cooluser` / `c00lp@ss` unless you change the corresponding `COOLSTORE_USER` and `COOLSTORE_PASS` parameters.
