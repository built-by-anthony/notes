# Allowing Failure 

## Learning Objectives 

- Demonstrate the ability to prevent a defined job from blocking your pipeline.

In GitLab CI/CD pipelies, allowing failures means that if a specific job within the pipeline fails, the pipeline as a whole will continue to execute instead of halting all jobs immediately. 

This is useful in situations where a specific job may fail for non-critical reasons, such as a unit test that is known to be flaky or a deployment to a non-critical environment that can be retried later. 

By allowing failures, the pipeline can continue to execute and other jobs that are not dependent on the failed job can continue to run.

To allow a job to fail without halting the pipeline, you can use the **allow_failure** keyword in the job definition in your GitLab CI/CD configuration file: 

```yaml
job:
  script:
    - some_command
  allow_failure: true
```

This can be particularily usefuyl in specific situations, such as linting, where the outcome of the job does not critically affect the application's performance. Unless yourteam requires a linting pass to be successful every time, consider using the `allow_failure` keyword.