# Day 2 CI Pipeline Design

## 1. Events

- **Push to main:** Run CI when application files change, but skip pushes that only change Markdown files or files inside `docs/`.
- **Pull request to main:** Test proposed changes before merging.
- **Workflow dispatch:** Allow manual runs with a selectable Python version and an option to skip packaging.
- **Schedule:** Run every Monday at 07:00 UTC to check the project regularly.

## 2. Jobs and Dependencies

```text
test
  |
  v
package
  |
  v
verify

test + package + verify
  |
  v
report

package
  |
  v
pr-comment (pull requests only)
```

- `test`: Install dependencies and execute pytest.
- `package`: Create and upload a `.tar.gz` archive after successful tests.
- `verify`: Download and test the package without checking out the repository.
- `report`: Always report the results and write a job summary.
- `pr-comment`: Post a comment on pull requests after successful packaging.

## 3. Artifact

The pipeline produces `dist/temperature-<version>.tar.gz`, containing `temperature.py` and `README.md`. It also uploads a JUnit test report.

## 4. Delivery Type

This is Continuous Integration because it automatically tests and packages code but does not deploy it to a production environment.

## 5. Package Job on Test Failure

The `package` job must be skipped when tests fail because an unsuccessful build should not produce a release artifact that could be mistaken for a verified package.