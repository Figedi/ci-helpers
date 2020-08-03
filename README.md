# CircleCI orbs/builder-images

This repostiory contains orbs/builder-images for building nodejs-services/npm packages.

## Description

The orbs try to use docker as much as possible. As a result, everything is docker-first. An job (e.g. lint or specs) is executed in an (application) docker-container which is later used
for releasing the package to a specified outlet (npm/github/docker). Right now, a typical CI pipeline looks as follows:

1. Build the builder image and (optionally) a release image. If there is no release image specified, the builder image is used.
  * The builder-/release-images are extracted through labels in a build-phase: `LABEL build=builder` or `LABEL build=release`
  * (!) This job performs a semantic bump. The local checked out repository **will** get a version bump w/ changelog depending on the commit history
  * Job-name: `checkout_bump_and_build`
2. Use another job which uses the image built from 1., e.g. linting. This performs a more docker-exec inside that container with a specified npm command
  * (!) By default, the builder-image is used, since it typically includes dev-dependencies. You can optionally use the release image here
3. For complex usecases- e.g. feature-tests with databases or other service, you can use step 2 with docker-compose.
  * Have a `docker-compose.ci.yml` ready in your project and run a specs job, e.g. `specs`
  * Tests are automatically executed in docker-compose. For maximal determinism, I recommend waiting for dependent services
4. Publish to a specified outlet. In the most minimal case, this can be just the github-commit for the version bump. 
  * To run a github-push, run `push_to_github`
  
 
