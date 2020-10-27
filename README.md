# Packer Validate Action

These is unofficial Packer [GitHub Actions][github-actions] which allows you to run packer validation and inspection on 
pull requests to review Packer template changes and potentially build on pull merge.
Check out the [official Packer documentation][packer-doc] for further reference. 

Runs `packer validate *.json` on pull request to validate the syntax and configuration of a template file in a directory
If the validation fails, it will print out error as pull request comment.
Check out the [packer validate command][packer-validate-doc] for further reference. 

## Usage

### YAML style

To configure the action simply add the following lines to your .github/workflows/packer-validate.yml workflow file:

```
name: Validate packer template file in a directory

on:
  pull_request:
jobs:
  packer_validate:
    runs-on: hashicorp/packer:1.6.4
    steps:
      - name: Packer validate template-y without argument
        uses: dawitnida/packer-github-actions/validate@master
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          TEMPLATE_FILE_NAME: ${{ env.TEMPLATE_FILE_NAME }}
```

### Legacy

To check this in action, please check [Packer actions demo project][packer-actions-demo] with a collection
of sample packer template files. 

Variables 

- `PACKER_ACTION_WORKING_DIR` : Working directory
- `TEMPLATE_FILE_NAME` : Packer template file
- `ACTION_COMMENT` : Enable/Disable PR comment from validate result

```
workflow "packer validate docker-image-template" {
  resolves = "packer-validate-docker-image-template"
  on = "pull_request"
}

action "filter-open-synced-pr" {
  uses = "actions/bin/filter@master"
  args = "action 'opened|synchronize'"
}

# For single template (eg. dockers dir contains *.json template)
action "packer-validate-docker-image-template" {
  uses = "dawitnida/packer-validate-action@master"
  needs = "filter-open-synced-pr"
  secrets = [
    "GITHUB_TOKEN",
  ]
  env = {
    TEMPLATE_FILE_NAME = "*.json"
    PACKER_ACTION_WORKING_DIR = "dockers"
  }
}

workflow "packer validate template-x with var-file" {
  resolves = "packer-validate-template-x"
  on = "pull_request"
}

# For specific template file (eg. packer-template-x.json) with var-file (global-vars.json) arg
action "packer-validate-template-x" {
  uses = "dawitnida/packer-validate-action@master"
  needs = "filter-open-synced-pr"
  secrets = [
    "GITHUB_TOKEN",
  ]
  args = [
    "-var-file=global-vars.json",
  ]
  env = {
    TEMPLATE_FILE_NAME = "packer-template-x.json"
  }
}

workflow "packer validate template-y without arg" {
  resolves = "packer-validate-template-y"
  on = "pull_request"
}

# For specific template file (eg. packer-template-y.json) without any args
action "packer-validate-template-y" {
  uses = "dawitnida/packer-validate-action@master"
  needs = "filter-open-synced-pr"
  secrets = [
    "GITHUB_TOKEN",
  ]
  env = {
    TEMPLATE_FILE_NAME = "packer-template-y.json"
  }
}
```

**Figure 1.** *Packer validate without args failed with a comment*
![failed-validation](assets/packer-template-y.png)

**Figure 2.** *Packer validate success & failed outputs*
![success-failed-output](assets/fail-success-validation.png)

**Figure 3.** *Packer validate complete check list diagram*
![checks-list-diagram](assets/action-results.png)


## License

The Dockerfile and associated scripts and documentation in this project are released under the [Apache-2.0 License](LICENSE).

### Author
[Dawit Nida](https://github.com/dawitnida)

[packer-validate-doc]:  <https://www.packer.io/docs/commands/validate.html>
[packer-actions-demo]:  <https://github.com/dawitnida/packer-actions-demo>
[github-actions]:       <https://github.com/features/actions>
[packer-doc]:           <https://www.packer.io/docs/index.html>
