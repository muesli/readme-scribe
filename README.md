# readme-scribe

A GitHub Action that automatically generates & updates markdown content (like your README.md)

## Instructions

1. Create a [markscribe](https://github.com/muesli/markscribe) template and
place it anywhere in a repository that you automatically want to update. In this
guide we will use `templates/README.md.tpl`.

You can find an example template that I use to automatically update my GitHub profile
here: https://github.com/muesli/markscribe/blob/master/templates/github-profile.tpl

2. In order to access some of GitHub's API, you need to provide a valid GitHub
token as a secret called `PERSONAL_GITHUB_TOKEN`. You can create a new token by
going to your profile settings:

`Developer settings` > `Personal access tokens` > `Generate new token`

Now create a new secret in your repository's `Settings` and enter that token.

3. Create a new GitHub workflow in your repository: `.github/workflows/readme-scribe.yml`

```yml
name: Update README

on:
  push:
  schedule:
    - cron: "0 */1 * * *"

jobs:
  markscribe:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@master

      - uses: muesli/readme-scribe@master
        env:
          GITHUB_TOKEN: ${{ secrets.PERSONAL_GITHUB_TOKEN }}
        with:
          template: "templates/README.md.tpl"
          writeTo: "README.md"

      - uses: stefanzweifel/git-auto-commit-action@v2
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        with:
          commit_message: Update generated README
          branch: master
```

This action will be triggered once per hour, parses `templates/README.md.tpl`
and generates a new `README.md` for you, and eventually pushes the changes to
the `master` branch. Make sure to adjust the input values `template` and
`writeTo` to suit your needs.

## Example output

![readme-scribe example output](https://github.com/muesli/readme-scribe/raw/master/assets/template_example.png)
