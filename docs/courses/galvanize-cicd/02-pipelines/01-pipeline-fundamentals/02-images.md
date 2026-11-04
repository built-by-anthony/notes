# Images 

## Learning Objectives 

- Identify how to leverage Docker base images for specific jobs defined in the `.gitlab-ci.yml` file. 

- Source GitLab's keyword reference for the `.gitlab-ci.yml` file to understand how to use images in your pipeline. 

In GitLab CI/CD, the **image** keyword is used to specify the Docker image that will be used to execute a job. The image can be thought of as a pre-built environment that contains all the necessary software and dependencies required to run the job. When a job is executed, the Docker client on the GitLab Runner pulls the specified image from a registry, creates a container based on the image, and then runs the job inside the container. This ensure that the job is executed in a consistent and isolated environment, regardless of the underlying host system. 

The **image** keyword helps exert control over the jobs that run in your pipeline. Depending on the type of job needed to be executed, you may need to select a specific image. 

If you run into errors in your pipeline, one of the first places to check is the image being used for the job. If the image supplied does not have the necessary tools to run the job, the job will fail. 

## Setting a Default Image 

Repeating the same `image` in every job gets noisy. Use the `default:` section to set keywords that every job inherits: 

```yaml
default:
  image: amazoncorretto:17-alpine

build-job:
  stage: build
  script: ./mvnw package

test-job:
  stage: test
  image: node:20-alpinw     # overrides the default for this job only
  script: npm test
```

`default:` also accepts `before_script`, `after_script`, `cache`, `artifacts`, `serices`, `tags`, `retry`, and `interruptible`.

Two rules to remember: 

- A job that defines the keyword itself **replaces** the default - the two are not merged.

- Placing these keywords at the ***top level*** of the file, outside `default:`, still works but is deprecated. Always nest them under `default:`. 

