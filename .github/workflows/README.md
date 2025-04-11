## Pull Request Quality Gate Workflow

This project includes a GitHub Actions workflow that simulates a quality gate for incoming Pull Requests.

### Behavior

- ✅ If a file named `allow_pull_request.txt` is found in the PR branch:

  - Logs: “Permitiendo Pull Request”
  - Allows the build to succeed.
  - Uploads the file as an artifact.

- ❌ If the file is not found:
  - Logs: “Build fallida. Impidiendo Pull Request”
  - Fails the job using `exit 1`.

### Trigger

This workflow runs automatically on `pull_request` events.

### File Location

- `.github/workflows/pull-request-check.yml`
