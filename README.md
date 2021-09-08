# Portal

<p align="center">
  <img width="460" height="300" src="portal.png">
</p>

```
the file was here
then the file was there

friggin' files flying everywhere
but not one doc build needs repair
```

# Example Workflow

```yml
name: Portal

on: push

jobs:
  copy-file:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout
      uses: actions/checkout@v2.3.4

    - name: Extract branch name
      shell: bash
      run: echo "##[set-output name=branch;]$(echo ${GITHUB_REF#refs/heads/})"
      id: source_branch

    - name: Teleport to wordlake
      uses: elastic/portal@main
      env:
        API_TOKEN_GITHUB: ${{ secrets.API_TOKEN_GITHUB }}
      with:
        source_file: '1 2 3 README.md'
        destination_repo: 'elastic/wordlake'
        user_email: 'portal@elastic.co'
        user_name: 'portal'
        destination_folder: 'docs/'
        destination_branch_create: '${{ steps.source_branch.outputs.branch }}'
```

# Variables

The `API_TOKEN_GITHUB` needs to be set in the `Secrets` section of your repository options. You can retrieve the `API_TOKEN_GITHUB` [here](https://github.com/settings/tokens) (set the `repo` permissions).

* source_file: Add multiple dirs and files, match using `.`. Multiple dirs: `1 2 3`. Multiple files: `file1.mdx file2.mdx`. Mix of dirs and files: `1 2 3 file1.mdx`.
* destination_repo: The repository to place the file or directory in.
* destination_folder: [optional] The folder in the destination repository to place the file in, if not the root directory.
* user_email: The GitHub user email associated with the API token secret.
* user_name: The GitHub username associated with the API token secret.
* destination_branch_create: [optional] Creates a branch if does not exist, switches if it does.
* commit_message: [optional] A custom commit message for the commit. Defaults to `Update from https://github.com/${GITHUB_REPOSITORY}/commit/${GITHUB_SHA}`

# Behavior Notes

The action will create any destination paths if they don't exist. It will also overwrite existing files if they already exist in the locations being copied to. It will not delete the entire destination repository.

# Forked

A GitHub action based on [copy_file_to_another_repo](https://github.com/dmnemec/copy_file_to_another_repo_action).
