# on: create

For a create event, the most commonly used properties within github.event are:
- github.event.ref (the name of the created branch/tag)
- github.event.ref_type (whether it's a branch or tag)
- github.event.pusher.name (who created it)
- github.event.repository.full_name (the repository it happened in)


```yaml
name: On Create Event Workflow

on:
  create:
    # This workflow will be triggered when a branch or tag is created.
    # For example:
    # - Creating a new branch: git push origin new-feature-branch
    # - Creating a new tag: git push origin v1.0.0

jobs:
  # Define a job named 'log-creation-event'
  log-creation-event:
    runs-on: ubuntu-latest # Specify the runner environment

    steps:
      # Step 1: Checkout the repository code
      - name: Checkout code
        uses: actions/checkout@v4
        # This action checks out your repository under $GITHUB_WORKSPACE,
        # so your workflow can access it.

      # Step 2: Log the created ref
      - name: Log created ref
        run: |
          echo "A new ref was created!"
          echo "Ref Type: ${{ github.event.ref_type }}" # branch or tag
          echo "Ref Name: ${{ github.event.ref }}"     # The name of the branch or tag
          echo "Pusher: ${{ github.event.pusher.name }}" # The user who pushed the ref
          echo "Repository: ${{ github.repository }}"    # The repository name (owner/repo)
          echo "SHA: ${{ github.sha }}"                  # The commit SHA associated with the ref
        # This step uses the 'run' keyword to execute shell commands.
        # It accesses GitHub context variables (github.event, github.repository, github.sha)
        # to get information about the event that triggered the workflow.
```

- `github.event` is a context object in GitHub Actions that contains the full webhook payload of the event that triggered the workflow run.

In the context of the `on: create` event, `github.event` will contain information specific to the creation of a branch or tag. For example:
- `github.event.ref_type:` Indicates whether the created ref is a branch or a tag.
- `github.event.ref:` The name of the branch or tag that was created (e.g., new-feature-branch or v1.0.0).
- `github.event.pusher.name:` The GitHub username of the person who pushed the new ref.

Essentially, it provides all the data that GitHub sends when an event occurs, allowing your workflow to react to specific details of that event.



The github.event object's contents are a direct reflection of the GitHub webhook payload for the specific event that triggered the workflow.

For the create event (which triggers when a Git branch or tag is created), the github.event object typically contains the following properties:

ref: The name of the Git ref (branch or tag) that was created.
Example: my-new-branch or v1.0.0

ref_type: The type of Git ref object created. This will be either branch or tag.

master_branch: The name of the repository's default branch (e.g., main or master).

description: The description of the repository.

pusher: An object containing information about the user who pushed the ref.

pusher.name: The username of the pusher.

pusher.email: The email of the pusher.

repository: An object containing detailed information about the repository where the event occurred. This is a very rich object and can include:
- repository.id: The repository ID.
- repository.node_id: The GraphQL Global Node ID of the repository.
- repository.name: The name of the repository (e.g., my-repo).
- repository.full_name: The full name of the repository (e.g., owner/my-repo).
- repository.private: A boolean indicating if the repository is private.
- repository.owner: An object containing information about the repository owner (e.g., owner.login, owner.id, owner.type).
= repository.html_url: The URL to the repository on GitHub.
- repository.description: The description of the repository.
- repository.fork: A boolean indicating if the repository is a fork.
- repository.url: The API URL for the repository.

Many more properties like stargazers_count, watchers_count, forks_count, open_issues_count, created_at, updated_at, pushed_at, default_branch, etc.

sender: An object containing information about the user who triggered the event (often the same as pusher for create events). It will have similar properties to pusher (e.g., sender.login, sender.id, sender.type).


How to explore github.event for any event:

The best way to understand the full structure of github.event for any given trigger is to:
Refer to the GitHub Webhook Events & Payloads documentation: This is the authoritative source. You can find the create event payload definition here: GitHub Webhook Events and Payloads - CreateEvent

Dump the context in a workflow run: As mentioned before, you can add a step to your workflow to print the entire github context (or specifically github.event) as JSON. This is invaluable for debugging and discovering available properties. To  output the complete JSON structure of github.event for the specific workflow run to your job logs:


```yaml
- name: Dump Create Event Payload
  run: echo "The full github.event payload is:\n${{ toJSON(github.event) }}"
  shell: bash
```


