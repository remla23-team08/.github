# REMLA 2023 - Team 08

Welcome to the "[Release Engineering for Machine Learning Applications](https://se.ewi.tudelft.nl/remla/)" project of team 08! All of the content produced under this organization is part of the implementation for the aforementioned CS4295 course at Delft University of Technology.

## :books: **Table of Contents**

- [REMLA 2023 - Team 08](#remla-2023---team-08)
  - [:books: **Table of Contents**](#books-table-of-contents)
  - [:file\_folder: **Organization Repositories**](#file_folder-organization-repositories)
  - [:twisted\_rightwards\_arrows: **Versioning**](#twisted_rightwards_arrows-versioning)
    - [GitVersion](#gitversion)
  - [:octocat: **GitHub App(s)**](#octocat-github-apps)
    - [General](#general)
    - [remla-team08-app](#remla-team08-app)
    - [Renovate](#renovate)
  - [:rocket: **Release Pipeline(s)**](#rocket-release-pipelines)
    - [General Layout](#general-layout)
    - [Publishing to GitHub Packages](#publishing-to-github-packages)
  - [:busts\_in\_silhouette: **Contributing**](#busts_in_silhouette-contributing)
    - [Commit Messages \& Branching](#commit-messages--branching)
    - [CODEOWNERS \& Pull Requests](#codeowners--pull-requests)
  - [:scroll: **Licensing**](#scroll-licensing)

## :file_folder: **Organization Repositories**

Within this organization you will find our implementation for the following repositories:
* [operation](https://github.com/remla23-team08/operation/tree/main) - Contains the needed Kubernetes & Docker configuration files and the published Helm charts
* [model-training](https://github.com/remla23-team08/model-training/tree/main) - Contains the ML training pipeline
* [model-service](https://github.com/remla23-team08/model-service/tree/main) - Contains the wrapper service for the ML model
* [app](https://github.com/remla23-team08/app/tree/main) - Contains a frontend web application that brings together all pieces
* [lib](https://github.com/remla23-team08/lib/tree/main) - Contains a version-aware library that can be asked for its version

## :twisted_rightwards_arrows: **Versioning**

All of the repositories within this organization are versioned using the [Semantic Versioning](https://semver.org/) (SemVer) format. Version bumps are done automatically when a PR is merged to the `main` branch. To achieve this, we are using the [GitVersion](https://gitversion.net/docs/) tool. For more information on how to use GitVersion, see [this link](https://gitversion.net/docs/).

### GitVersion

All repositories are using the [GitVersion GitHub Action](https://github.com/marketplace/actions/gittools) to automatically determine the next version number based on the git history. This version is then later used to:
1. Create a new git tag
2. Create a new GitHub release
3. Perform a version bump in the files that need to be versioned (e.g. `package.json`, `Chart.yaml`, etc.)
4. Use the version number for any packages that are published to GitHub Packages and are version aware (e.g. NPM lib package, Docker images, helm charts, etc.)

> More specifically, the versioning is configured to use the *[Mainline Development]*(https://gitversion.net/docs/reference/modes/mainline) strategy, which means that the version number is determined based on the number of commits since the last release. Consult the [CODEOWNERS and Pull Requests](#codeowners--pull-requests) section for more information on how the versioning is done w.r.t. to adding new changes.

## :octocat: **GitHub App(s)**

In short, [GitHub Apps](https://docs.github.com/en/apps) extend GitHub's already wide-range of products and corresponding functionalities by allowing developers to build custom applications and integrate them with GitHub's APIs.

### General

* GitHub apps can be installed directly on organizations and user accounts and can be granted access to specific repositories with fine-grained permissions
* GitHub apps can be used to perform actions on behalf of the organization or user account that installed them
* Easier to manage than GitHub Personal Access Tokens (PATs) as they are not tied to a specific user account and can be installed on multiple repositories. In large organizations, this can be a huge advantage as it allows for a more centralized management of the organization's resources, whilst also not tying credentials to a specific user account (i.e. if a user leaves the organization, the app can still be used)

### remla-team08-app

In our case, we created a dedicated GitHub app used solely for authentication purposes and installed it on our organization, within all of the team's repositories. This allows us to perform automatic releases and commits on behalf of the organization, without having to issue and worry about personal access tokens and all the security concerns that come with them (i.e. secret rotation, token revocation, etc.). More specifically, we perform the following actions using the GitHub app:
* Performing `git clone` operations within the CI/CD pipelines
* Performing version bumps and commits within the CI/CD pipelines by allowing only the GitHub app to push to the `main` branch (i.e., bypassing the branch protection rules)
* Perform automatic releases to GitHub Packages using the GitHub app's credentials
* All other git operations, such as `git tag` and invoking any [GitHub CLI](https://cli.github.com/) commands to create releases

### Renovate

We also installed and configured the [Renovate GitHub App](https://github.com/apps/renovate) for performing automatic dependency updates. This app is configured to automatically create pull requests for updating the dependencies of all of the repositories within the organization. The subsequent pull requests are then reviewed by the team members and merged if they pass the CI/CD pipeline validation step.

> **NOTE:** Each repository may have different validation steps in their respective CI/CD pipeline - depending on the package manager used and corresponding codebase.

## :rocket: **Release Pipeline(s)**

Within the organization's repositories, release pipelines are triggered automatically when a new PR is merged to the `main` branch. 

### General Layout

Generally speaking, all release pipelines are configured to perform the following steps:
1. Clone the repository
2. Install & configure GitVersion to determine the next version number
3. Perform a version bump in the files that need to be versioned (e.g. `package.json`, `Chart.yaml`, etc.) if present
4. Build & publish the package/Docker image/helm chart if present
5. Commit the changes and push them to the `main` branch

### Publishing to GitHub Packages

As part of the release pipelines, the organization produces the following packages for public usage:
1. Docker images for the [model-service](https://github.com/remla23-team08/model-service/pkgs/container/model-service) and [app](https://github.com/remla23-team08/app/pkgs/container/app) repositories
2. Helm charts for the [operation](https://remla23-team08.github.io/operation/) repository (published via GitHub Pages and the helm-chart-releaser GitHub Action)
3. NPM packages for the [lib](https://github.com/remla23-team08/lib/pkgs/npm/lib) repository

## :busts_in_silhouette: **Contributing**

### Commit Messages & Branching

Within this organization, we are encouraging (but not forcing) the following commit message format:

```
<type>: <subject>.
```
* Where `<type>` is *optional* and one of the following:
  * **feat**: A new feature
  * **fix**: A bug fix
  * **docs**: Documentation only changes
* Where `<subject>` is a short description of the change

> **NOTE**: Never include the `[skip ci]` tag in the commit message, as this will skip the CI/CD pipeline and the changes will not be tested.

### CODEOWNERS & Pull Requests

All repositories are configured to have the entire team as CODEOWNERS. This means that any pull-request to the `main` branch will require at least one approval from a team member.

Regarding such pull-requests, each batch of changes should ideally be explained in the PR body using our automatic pull request template, which can be found in any repository under `.github/PULL_REQUEST_TEMPLATE.md`. This template is automatically used when creating a new pull request.

> When a PR is approved, the only option for merging that is available is the "Squash and Merge" option. This is done to ensure that the commit history is kept clean in order to avoid incorrect versioning when using `GitVersion`.

## :scroll: **Licensing**

All of the repositories within this organization are licensed under the [MIT License](https://en.wikipedia.org/wiki/MIT_License).
