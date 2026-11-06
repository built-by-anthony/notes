# Publishing 

## Publishing Components to the CI/CD Catalog 

## Learning Objectives 

- Identify the project-level requirements for publishing a component to the GitLab CI/CD Catalog

- Set up your project with the correct structure nad metadata to ensure visibility

## Catalog Publishing Requirements 

To make your componenet discoverable in the GitLab CI/CD Catalog, your **project** must meet the following criteria: 

| **Requirement** | **Description** | 
|-----------------|-----------------|
| `README.md` file| A `README.md` must exist at the root if the project | 
| Project description | The GitLab project must have a description | 
| `templates/` foler | Components must be stored in a folder named `templates` at the project root | 
|Catalog visibility enabled | In **Settings > General**, click "Expand" on the "Visibility, project features, and permissions" section. Ensure that the "CI/CD Catalog project" swtich is turned **on**. | 

> ⚠️ If any of these requirements are missing, your project may not appear in the catalog

## File Structures Examples 

Here is what a valid component project might look like with single-file components: 

```text
my-component-project/
├── README.md
├── templates/
│   ├── docker-build.yml 
│   └── lint.yml 
```

You may also split a component into two files using a subfolder under `templates/`: 

```text
my-component-project/
├── README.md
├── templates/
│   └── docker-build/
│       ├── component.yml
│       └── .gitlab-ci-yml
```

This two-file structure is helpful when speculating the `spec:` block (`component.yml`) from job definitions (`.gitlab-ci.yml`). 

> 💡 You may use an additional folder under `templates/` to organize components with multiple files.

## Semantic Versioning 

A component project only appears in the catalog once it has a **release**. A tag on its own is not enough: each release becomes one **version** of the component in the catalog, and the tag it points at fixes which commit consumers get. 

Name every tag with a **semantic version**, written as three numbers: 

- `1.0.0`, `1.2.3`, `2.0.0` - each one identifies a stable version that consumers can pin to 

- Avoid mutable names like `lastest`, especially for production

> Tip: Increase the patch number (1.0.1) for backware-compatible fixes, the mimintor number (`1.1.0`), for new features that don't break anyone, and the major number (`2.0.0`) for breaking changes. 

## Releasing a Component 

- **From a pipeline.**  You add a release job once, and every new tag releases automatically. This is how the componenets in our catalog are released. 

### Release From a Pipeline

Add a `release` stage and a release job to the component project's own `.gitlab-ci.yml`: 

```yaml
stages: 
  # ...your existing stages, such as test...
  - release

create-release:
  stage: release
  image: registry.gitlab.com/gitlab-org/release-cli:latest
  rules:
    - if: $CI_COMMIT_TAG =~ /^\d+\.\d+\.\d+/
  script: echo "Creating release $CI_COMMIT_TAG"
  release:
    tag_name: $CI_COMMIT_TAG
    description: "Release $CI_COMMIT_TAG of components in $CI_PROJECT_PATH"
```

#### How it works: 

- The `rules:` entry runs the job only in a pipeline for a tag that starts with a semantic version. Ordinary pushes to a branch skio it. 

- The `release:` keyword tells the runner to create a release for the tag. The job image provides the tool that does it, so there is nothing for you to install. 

- The `script:` line is required on every job. Here it only prints a message.

- Put this job in a **later stage** than any jobs that test your components, so a faiing test stops the release. 

Commit and push this file. Then **create a tag**, as decribed in **Creating the Tag** below. The tag starts a pipeline, the `create-release` jobs creates the release, and the new version appears in the catalog. 

#### Creating the Tag 

You need a tag to start the release pipeline. You can do this in two ways.

**In the GitLab UI**: go to **Code > Tags**, select **New Tag**, enter the version, choose the branch to tag (usually `main`), and select **create tag**.

**From the Git command line**: tag the commit you want to release, then push the tag. Commit and push your changes first, because the tag points at the commit: 

```bash
git checkout main
git pull 
git tag -a 1.0.0 -m "Release 1.0.0"
git push origin 1.0.0
```

`git push` on its own does not send tags. You have to push the tag by name, as shown, or push all of your tags with `git push --tags`. 

