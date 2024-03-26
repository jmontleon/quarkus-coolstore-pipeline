# Quarkus Coolstore Pipeline
Deploy a production instance of coolstore on OpenShift using pipelines

## Prerequisites
- Red Hat Pipelines Operator installed on your OpenShift cluster

## Setup
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

## Use
1. Run the deploy-coolstore-prereqs pipeline. You can accept the default options
1. Run the deploy-coolstore pipeline. You should select `VolumeClaimTemplate` for the workspace. No additional configuration is necessary. You may want to change the repo and branch to whatever you're working on. You can rerun this pipeline as many times as you want to redeploy the app. The deployconfig has an image trigger so it will restart automatically when the new image build is pushed.
1. A route will be create and your coolstore instance will be available at https://coolstore-$namspace.apps.$cluster.$domain
1. Quarkus is configured with the user `cooluser` / `c00lp@ss` unless you change the corresponding `COOLSTORE_USER` and `COOLSTORE_PASS` parameters.

## Example:
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
Choose to start the deploy-coolstore-prereqs pipeline. 
<img src="https://github.com/jmontleon/quarkus-coolstore-pipeline/assets/1850500/b86fb33e-99c0-4136-a48d-3baba54c6331" width="800" height="500" />

Change any desired options, start, and wait for it to finish. 
<img src="https://github.com/jmontleon/quarkus-coolstore-pipeline/assets/1850500/14c0d99a-c297-4b54-bdb3-246ba1d07ebd" width="600" height="500" />

Choose to start the deploy-coolstore pipeline.  
<img src="https://github.com/jmontleon/quarkus-coolstore-pipeline/assets/1850500/4344f597-fc6d-4be5-87d0-9272b5c26ecb" width="800" height="500" />

Change the `shared` workspace to use a `VolumeClaimTemplate`, change any desired options, start, and wait for it to finish.  
<img src="https://github.com/jmontleon/quarkus-coolstore-pipeline/assets/1850500/c42ed892-525f-4db3-ad7a-17b38cddeba0" width="600" height="500" />
