## How to replicate this Github Action workflow
Github actions are used to build and push the docker image. The docker image is pushed to AWS ECR. Inorder to effectively use the actions you will need to add the following secrets to your repository settings. `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`, and and `AWS_REGION` for the IAM user that has access of the AWS ECR. `DOCKER_REGISTRY_URL=<account-id>.dkr.ecr.<region>.amazonaws.com/umesh-mapup-assignement` for the AWS ECR Repository URI. This is to enable the docker login step in the workflow and push the image to the AWS ECR repository.


## How this workflow functioning
This project consists a simple NodeJS application. The workflow builds and and pushes the docker image to the AWS ECR Registry and it is also using caching for the Docker Layers. TO ruduce the build time and computation power.

Lets uderstand the workflow step by step:

**Name:** The name of the workflow.

**On:** The event that triggers the workflow. In this case, the workflow is triggered on every push to the main branch.

**Env:** Environment variables used in the workflow. In this case, we have two environment variables: REGISTRY. The REGISTRY variable is used to specify the ECR registry to push the image to and it has also the image name. 

**Jobs:** The workflow consists of one job called `build`. The job is run on the latest version of Ubuntu.
Inside the `build` we specify the steps to be executed. The first step is to check out the repository. The second step is to set up Docker Buildx. The third step is to log in to AWS ECR. The fourth step is to extract metadata for Docker. The fifth step is to build and push the Docker image.

### Check out the repo
This step checks out the repository. This is a required step for all workflows.

### Configure AWS Credentials
This step setup AWS Credentials which uses to login into AWS ECR registry. The step uses the `AWS_SECRET_ACCESS_KEY`, `AWS_ACCESS_KEY_ID`  and `AWS_REGION` secrets to log in to AWS ECR. The secrets are stored in the repository settings. 

### Set up Docker Buildx
This step sets up Docker Buildx. Docker Buildx is a CLI plugin that extends the Docker command with the full support of the features provided by Moby BuildKit builder toolkit. It provides the same user experience as docker build with many new features like creating scoped builder instances and building against multiple nodes concurrently. You can read more about Docker Buildx here.

### Log in to  AWS ECR
This step logs in to AWS ECR. 

### Extract metadata (tags, labels) for Docker:
This step extracts metadata for Docker. The step uses the docker/metadata-action action to extract the metadata. The action is used to extract metadata from Dockerfiles. The action outputs two variables: tags and labels. The tags variable contains the tags for the Docker image. The labels variable contains the labels for the Docker image.

### Cache Docker layers
This step caches the Docker layers. The step uses the actions/cache action to cache the Docker layers. The action takes in the following parameters:

#### path 
The path to the directory to be cached. In this case, we want to cache the src directory.
#### key
The key to use for restoring and saving the cache. Here we use the runner.os and github.sha variables to create a unique key for the cache.
#### restore-keys
An ordered list of keys to use for restoring the cache if no cache hit occurred for key. Here we use the runner.os variable to create a unique key for the cache.

### Build and push Docker image
This step builds and pushes the Docker image. The step uses the docker/build-push-action action to build and push the Docker image. Here we added the `cache-from` and `cache-to` parameters to the action. The `cache-from` parameter specifies the cache to use for the build. The `cache-to` parameter specifies the cache to use for the push. In this case, we use the `type=local` cache to cache the Docker layers. The `src=/tmp/.buildx-cache` specifies the source of the cache. The `dest=/tmp/.buildx-cache` specifies the destination of the cache.