# Additional Topics 

## Learning Objectives

- Understand how `needs` optimizes job execution across stages. 
- Use `rules` to control when jobs run. 
- Apply `before_script` and `after_script`, both per job and via `default:`.
- Manage how artifacts and cache behave in advanced pipelines.

## Job Execution with `needs`

### Default behavior with `stages`

Pipelines are ususally structured with sequential **stages**, meaning jobs in one stage must complete before the next begins. 

```yaml
stages:
  - build
  - test
  - deploy

build-job:
  stage: build
  script: echo "Building..."

test-job:
  stage: test
  script: echo "Testing..."
```

This works well for simple flows but can cause delays when later jobs could safely start eariler. 

### `needs` for Directed Job Dependencies 

`needs` allows jobs in later stages to **start earlier** if their dependencies are ready. It also transfers artifacts automatically. 

```yaml
test-job:
  stage: test
  needs: 
    - job: build-job
  script: echo "Testing with build artifacts"
```

Key benefits: 

- Speeds up pipelines by parallelizing job execution. 
- Makes artifact sharing explicit and efficient. 
- Supports cross-stage job execution. 

## `rules` for Conditional Job Execution

`rules` define when a job should run, replacing the older `only`/`except` syntax with a more expressive system. 

```yaml
lint-job:
  script: run-linter
  rules:
    - if: `"CI_COMMIT_BRANCH == "Main"'
      when: always
    - if: '$CI_COMMIT_BRANCH =~ /^feature\//'
      when: manual
```

Common use cases: 

- Run jobs only on certain branches.
- Make jobs manual.
- Skip jobs entirely based on context (e.g., files changes, variables). 

## `before_script` and `after_script`

Use these to run setup or cleanup steps outside the main script logic.

- `before_script`: runs before `script`
- `after_script`: runs after `script`, even on failure

Define them per job, or in the `default:` section so every job inherits them.

```yaml
default:
  before_script:
    - echo "Setting up..."
  after_script:
    - echo "Cleaning up..."

build:
  script:
    - echo "Building app"
```

A job that defines its own `before_script` **replaces** the default rather than appending to it. 

Placing `before_script` or `after_script` at the top level of the file, outside `default:`, sitll works but is deprecated. 

## Artifacts and Cache with `needs`, `rules`, and Scripts

### Artifacts

Artifacts are files passed between jobs. 

- Jobs must define `artifacts` in order to share them. 
- Downstream jobs must use `needs` to get those artifacts. 

```yaml
build:
  stage: build
  script: ./build.sh
  artifacts:
    paths:
      - target/

test:
  stage: test
  needs: [build]
  script: ./test.sh
```

If `needs` is not used, jobs in later stages cannot access the artifacts early. 

### Cache

Cache is used to **reuse resources across jobs**, such as dependencies or compiled output. 

```yaml
default: 
  cache:
    key: maven
    paths:
      - .m2/repository
```

Notes: 

- Cache is shared by jobs with the same `key`. 
- Can be reused across stages. 
- Unlike artifacts, cache is not tied to job dependencies. 
- Decalring `cache` in `default:` applies it to every job that doesn't define its own. 

## Sumamry 

| **Feature** | **Purpose** | **Key Benefit** | 
|-------------|-------------|-----------------|
| `stages` | Defines job exeuction order | Simple, linear pipelines| 
| `needs`  | Defines job dependencies and speeds up jobs | Enables parallel and faster pipelines | 
| `rules` | Controls job execution conditions | Flexible control over job flow |
| `before_script`/`after_script` | Pre-/post job lifecycle | Consistent setup and teardown | 
| `artifacts` | Share job output across pipeline | Enable reuse of files across jobs. | 
| `cache` | Share dependencies | Avoid re-downloading or rebuilding |