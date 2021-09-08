# Portal

<p align="center">
  <img width="460" height="300" src="portal.png">
</p>

```
a file was here
a file was there

friggin' files are everywhere
yet not one doc build needs repair
```

# Example Workflow

```yml
name: Portal

on:
  pull_request:
    types: [closed]

jobs:
  copy-file:
    # PRs must close into a merge, and not just close
    if: github.event.pull_request.merged == true
    runs-on: ubuntu-latest
    steps:
    - name: Checkout
      uses: actions/checkout@v2.3.4

    - name: Pull branch name
      shell: bash
      run: echo "##[set-output name=branch;]$(echo ${GITHUB_REF#refs/heads/})"
      id: source_branch

    - name: Teleport to wordlake
      uses: elastic/portal@main
      env:
        API_TOKEN_GITHUB: ${{ secrets.API_TOKEN_GITHUB }}
      with:
        source_file: 'source-dir/.'
        destination_repo: 'elastic/wordlake'
        user_email: 'portal@elastic.co'
        user_name: 'portal'
        destination_folder: 'zerp'
        destination_branch: ${{ steps.source_branch.outputs.branch }}
        destination_branch_create: ''
```

# Variables

The `API_TOKEN_GITHUB` needs to be set in the `Secrets` section of your repository options. You can retrieve the `API_TOKEN_GITHUB` [here](https://github.com/settings/tokens) (set the `repo` permissions).

* source_file: The file or directory to be moved. Uses the same syntax as the `cp` command. Incude the path for any files not in the repositories root directory.
* destination_repo: The repository to place the file or directory in.
* destination_folder: [optional] The folder in the destination repository to place the file in, if not the root directory.
* user_email: The GitHub user email associated with the API token secret.
* user_name: The GitHub username associated with the API token secret.
* destination_branch: [optional] The branch of the source repo to update, if not "main" branch is used.
* destination_branch_create: [optional] A branch to be created with this commit, defaults to commiting in `destination_branch`
* commit_message: [optional] A custom commit message for the commit. Defaults to `Update from https://github.com/${GITHUB_REPOSITORY}/commit/${GITHUB_SHA}`

# Behavior Notes

The action will create any destination paths if they don't exist. It will also overwrite existing files if they already exist in the locations being copied to. It will not delete the entire destination repository.

# Forked

A GitHub action based on [copy_file_to_another_repo](https://github.com/dmnemec/copy_file_to_another_repo_action).
