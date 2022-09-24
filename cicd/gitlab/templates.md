# Gitlab Templates

## Generic Jobs

In `.gitlab-ci.yml`:

- Define a hidden job (start name with `.`)
- Use variables

To use generic jobs, add `extends` to target jobs and fill the defined variables.

> To prevent job from auto execution, use `when: manual` in the job.

## Job Templates

There are two types of template:

- *Pipeline*
- *Job*: It provide a specific job and can be included to an existing CI/CD workflow.

> **Best Practice**
>
> It is better to have a separate repository for job templates.

### Include Template

- Local file (in same repo)

  ```yml
  include:
    - local: <filename>.yml
  ```

- Project

  ```yml
  include:
    - project: <group/username>/<repo>
      file:
        - file1.yml
        - file2.yml
      ref: main # branch name
  ```

  > If the job template wasn't in a group, use Gitlab instance *username* instead of *group*.

- Remote

  ```yml
  include:
    remote: <url_of_job_template>.yml
  ```

- Template

  ```yml
  include:
    template: <path_in_gitlab_template_repo>.yml
  ```
