# my_gitlab_reusable_pipeline
Reusable pipeline for fully automated gitops CICD

The flow for the whole CI/CD
### start -> caller-repo (triggers) -> revisionism (triggers) -> auto-filler -> end

## The caller repo
This the repo that contains the source code which the developer works on, in this repo we will have a very short pipeline that triggers another pipeline in the revisionism repo.

## The revisionism repo
This repo contains the pipeline that performs the actual pipeline jobs/stages such as dependency installation, artifact build, vulnerability scans, container registry login and mostly importantly image build and container registry image pushes. Once all the jobs are successful the last job of the revisionism pipeline is to trigger the third pipeline which can be found in auto-filler repo.

## The auto-filler repo
This repo what is responsible for three very important functions
1. It contains a script called script.sh that is responsible for printing out new applications and manifest files for new caller-repos. This script is activated within the pipeline by passing 4 arguments that are retrieved from the earlier pipelines (caller-repo and revisionism).
2. It is the repo which the argocd syncs to, to keep track of manifest and application object changes.
3. It contains the pipeline responsible for updating the newly acquired image that is retrieved from the revisionism repo pipeline's dockerize stage into the deployment manifest file for argocd to pick up and make the necessary new deployments.

NB: Note that this very repo we are in my_gitlab_reusable_pipeline is the auto-filler repo just that the name has been changed. Also note that every file in the very folder belongs to the auto-filler repo with the exception of the external-repo folders which houses both the caller-repo repo and the revisionism repo.
