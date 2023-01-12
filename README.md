# merge-branches-github-action

> Merge all PRs with the specified label into a branch.

## List of input options

See [action.yml](./action.yml)

| input      | description                                                                                                | required | default                       |
| :--------- | :--------------------------------------------------------------------------------------------------------- | :------: | :---------------------------- |
| token      | GitHub token.                                                                                              |   true   |                               |
| base       | The name of the base branch. Merge operations will be performed on the basis of this branch.               |   true   |                               |
| target     | The name of the target branch. This branch will be checked out from the base branch and accept all merges. |   true   |                               |
| label-name | The name of a label to find PRs to merge.                                                                  |   true   |                               |
| email      | The email of the committer.                                                                                |  false   | merge-branches-bot@github.com |
| name       | The name of the committer.                                                                                 |  false   | merge-branches-bot            |

## Example

```yml
name: 'merge-branches'
on:
  workflow_dispatch:
    inputs:
      base:
        description: 'The name of the base branch. Merge operations will be performed on the basis of this branch.'
        required: true
        default: 'main'
        type: string
      target:
        description: 'The name of the target branch. This branch will be checked out from the base branch and accept all merges.'
        required: true
        type: string
      label-name:
        description: 'The name of a label to find PRs to merge.'
        required: true
        type: string

permissions: write-all

jobs:
  merge-branches:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
        with:
          fetch-depth: 0
      - uses: codytseng/merge-branches-github-action@v1
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          base: ${{ inputs.base }}
          target: ${{ inputs.target }}
          label-name: ${{ inputs.label-name }}
```

## How does it work?

1. Get all open PRs.
2. Find PRs containing the specified label.
3. `git config --global user.email ${email}`
4. `git config --global user.name ${name}`
5. `git fetch origin`
6. `git checkout ${base}`
7. `git pull origin ${base}`
8. `git branch ${target} -D`
9. `git checkout -b ${target}`
10. Loop branches `git merge origin/${branchName}` (Abort the merge if an error occurs.)
11. `git push origin ${target} -f`
