# Gitlab Basic CI/CD Configuration

## Runner

test

## Shared Files

### Artifacts

To access files from a job in a stage to next jobs in different stages, use `artifacts`.

```yml
job-name:
  ...
  artifacts:
    paths:
      - path_to_file
```

### Dependencies

To access files from a job in a stage to next jobs in the same stage, use `dependencies`. In other words, `dependencies` tell Gitlab that job *B* needs artifacts from job *A*.

- All jobs in `dependencies` must also be in `needs`.
- If `needs` and `dependencies` jobs are same, the `dependencies` can be removed.
  > The `needs` automatically downloads all artifacts from its jobs.
- To prevent downloading artifacts from previous jobs, use `dependencies:[]`.

### dotenv File

Use `dotenv` for environment variables in other jobs:

  1. Save vars in *key-value* pair in a `dotenv` file.
  2. Use `artifacts.reports.dotenv:filename.env`.

## Optimization

### Cache

```yml
job-name:
  ...
  cache:
    - key: "$CI_COMMIT_REF_NAME"
      paths:
        - path_to_dir
      policy: pull-push # pull-push, push, pull
```

Note: If the runner is a docker container, because the cache is stored in local filesystem, with removing container after job executing the cache also will be deleted. Solution (in `/etc/gitlab-runner/config.toml`):

```toml
cache_dir="/cache"  # added
```

> **Best Practice**
>
> - Job should never depend on a cache to be available.
> - Caching is an optimization, but it isn't guaranteed to always work.
