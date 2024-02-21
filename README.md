# Vetspire DevOps Technical Interview

The purpose of this repo is to hold the technical interview for the role of
DevOps engineer for Vetspire.

This interview will test Terraform and Kubernetes knowledge and ability.

## Interview Setup

You have been given a private repo and added to it. Now, make a commit for each
of the sections in this interview (infra, kubernetes and kustomize).

When you finish, please invite `tomasz-tomczyk` (GH username) to the repo and
let us know you've completed it.

Try your best and have fun!

## Pre-requisites

In order to be able to run this code, you will need the following (a BASH script
has been added to help install these. Alter this and use as necessary):

- Docker
- Kubeconform
- Kubectl
- Kustomize
- Terraform

or, if you would rather not install these and you use VSCode, you can use the
[DevContainer](https://code.visualstudio.com/docs/devcontainers/containers) to
handle this.

## Infra

To test Terraform knowledge, a simple Terraform project has been created which
creates a set of Google Cloud Compute Engine instances running an NGINX
container and a Google Cloud VPC Network for them to be created in.

However, there are some issues with the code that prevent it from being run.
There are also some parts of the code that aren't best practice. The task,
therefore, is to fix this code and make it align with best practices so that it
can be planned and applied.

### Setup

To setup the infra section of this interview, you will need to use Docker to run
a mock API. This mock API will be used by Terraform to plan and apply the code
without needing to have a GCP environment to run against.

The following command will set up this mock API (from the root directory of this
repo):

```bash
docker run -d --mount type=bind,source=$(pwd)/compute-api.json,target=/data,readonly -p 3000:3000 mockoon/cli:latest --data /data
```

### Checking your work

Your solution should allow you to intialize, plan, apply and destroy the
Terraform environment.

```bash
cd terraform
terraform init
terraform plan
terraform apply
terraform destroy
```

## Kubernetes

To test Kubernetes knowledge, some simple Kubernetes manifests have been created
which create an NGINX deployment and an NGINX pod.

However, there are some issues with the manifests that prevent it from being
run. There are also some parts of the code that aren't best practice. The task,
therefore, is to fix the manifests and make it align with best practices so that it
can be planned and applied.

### Setup

No setup is REQUIRED for this part of the interview. If you choose, you can set
up a cluster to test your configurations in whatever way you see fit (some
possible options might include KinD, Minikube or Docker Desktop if on Mac or
Windows).

### Checking your work

Your solution should make these manifests conform to the API spec and therefore
be usable. This can be tested using
[`kubeconform`](https://github.com/yannh/kubeconform):

```bash
kubeconform -strict kubernetes/
```

There are a total of 7 issues with the manifests (and therefore 7 changes you
will need to make). Kubeconform will help you find most of these but there are
some which you might need to figure out using different methods...

## Kustomize

The final part of this technical interview asks you to apply
[`kustomize`](https://kubectl.docs.kubernetes.io/references/kustomize/) to the
Kubernetes resource declaration. We want to add Kustomize to do the following:

1. Change the name and tag of the image used to be nginxinc/nginx-unprivileged:1.25.
2. Add a config map with the following data:
   - env: dev
   - host: localhost
   - port: 8080
3. Add a name suffix to the pod and deployment of "-dev".
4. Change the number of replicas for the deployment to 5.
5. Overwrite the container port to be `8080`.

### Setup

No setup is required for this task. You can initialise the Kustomize code in any
way you like.

### Checking your work

Once your Kustomize code is written, it should build into a manifest that then
passes with `kubeconform` as with the manifests above. This can be checked with
the following commands (from the root of this repo):

```bash
kustomize build kubernetes/ -o kustomized.yaml
kubeconform -strict kustomized.yaml
```

## Teardown

To teardown this interview environment, do the following:

1. If you created any clusters for testing the Kubernetes portion of the
   interview, please ensure you stop it.
2. Stop your Google API mocker. To do this find the Docker container:
   ```bash
   docker ps -a
   ```
   Once you have the name or ID of this container, stop it and remove it:
   ```bash
   docker stop <id>
   docker rm <id>
   ```
3. If you are running in a DevContainer, you can also exit from that and remove
   the container.

Thank you so much for your time!
