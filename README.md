> This implementation is based on that: https://github.com/MShawon/github-clone-count-badge

With some advantages:

1. Repository to track and repository to store traffic statistic are different
2. Workflow is used [accum-traffic-clones.sh](https://github.com/andry81/gh-workflow/blob/master/bash/accum-traffic-clones.sh) bash script to accumulate traffic clones

You need 3 repositories:

1. Repository which clone statistic you want to track: `myrepo`
2. Repository, where clone statistic will be saved: `myrepo--gh-stats`
3. Repository, where to store github workflow scripts: `gh-workflow`.<br>
   You can fork it from here: https://github.com/andry81/gh-workflow

You need to attach a personal access token (PAT) into 2 repositories, one in each for the push permission (`repo`->`public_repo`):

* `myrepo` -> `SECRET_TOKEN`
* `myrepo--gh-stats` -> `SECRET_TOKEN`

To generate PAT: https://docs.github.com/en/github/authenticating-to-github/creating-a-personal-access-token

To attach PAT: https://docs.github.com/en/actions/reference/encrypted-secrets#creating-encrypted-secrets-for-a-repository

The `myrepo--gh-stats` repository should contain 1 file:

`traffic/clones-accum.json`:

```
{
  "timestamp" : "0000-00-00T00:00:00Z",
  "count" : 0,
  "uniques" : 0
}
```

The `myrepo` repository should contain 1 file:

`.github/workflows/myrepo-gh-clone-stats.yml`:

(The actual example can be taken here: [tacklebar-gh-clone-stats.yml](https://github.com/andry81/tacklebar/blob/trunk/.github/workflows/tacklebar-gh-clone-stats.yml))

```yaml
name: GitHub clones counter for 14 days at every 8 hours and clones accumulator

on:
  schedule:
    - cron: "0 */8 * * *"
  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

jobs:
  traffic-clones:
    runs-on: ubuntu-latest

    env:
      traffic_clones_json: traffic/clones.json
      traffic_clones_accum_json: traffic/clones-accum.json

    steps:
      - name: allocate directories
        run: |
          mkdir $GITHUB_WORKSPACE/gh-workflow $GITHUB_WORKSPACE/gh-stats

      - name: checkout gh-workflow
        uses: actions/checkout@v2
        with:
          repository: '{{REPO_OWNER}}/gh-workflow'
          path: gh-workflow

      - name: checkout gh-stats
        uses: actions/checkout@v2
        with:
          repository: '{{REPO_OWNER}}/{{REPO}}--gh-stats'
          path: gh-stats
          token: ${{ secrets.SECRET_TOKEN }}

      - name: request traffic clones json
        run: |
          curl --user "${{ github.actor }}:${{ secrets.SECRET_TOKEN }}" \
            -H "Accept: application/vnd.github.v3+json" \
            https://api.github.com/repos/{{REPO_OWNER}}/{{REPO}}/traffic/clones \
            > "$GITHUB_WORKSPACE/gh-stats/$traffic_clones_json"

      - name: accumulate traffic clones
        shell: bash
        run: |
          cd $GITHUB_WORKSPACE/gh-stats
          chmod ug+x $GITHUB_WORKSPACE/gh-workflow/bash/accum-traffic-clones.sh
          $GITHUB_WORKSPACE/gh-workflow/bash/accum-traffic-clones.sh

      - name: commit gh-stats
        run: |
          cd $GITHUB_WORKSPACE/gh-stats
          git add .
          git config --global user.name "GitHub Action"
          git config --global user.email "action@github.com"
          git commit -m "Automated traffic/clones update"

      - name: push gh-stats
        uses: ad-m/github-push-action@master
        with:
          repository: '{{REPO_OWNER}}/{{REPO}}--gh-stats'
          directory: gh-stats
          github_token: ${{ secrets.SECRET_TOKEN }}
```

:warning: You must replace all placeholder into respective values:

* `{{REPO_OWNER}}` -> repository owner
* `{{REPO}}` -> `myrepo`

After the github workflow yaml file is commited and pushed, you can run the action from the `Actions` tab in the `myrepo` repository.

After that all badges must start to work:

```
<p align="center">
  <a href="#"><img src="https://img.shields.io/badge/dynamic/json?color=success&label=Github%20clones|all&query=count&url=https://github.com/{{REPO_OWNER}}/{{REPO}}--gh-stats/raw/master/traffic/clones-accum.json?raw=True&logo=github" valign="middle" alt="GitHub clones|all" /></a>
• <a href="#"><img src="https://img.shields.io/badge/dynamic/json?color=success&label=Github%20clones|unq&query=uniques&url=https://github.com/{{REPO_OWNER}}/{{REPO}}--gh-stats/raw/master/traffic/clones-accum.json?raw=True&logo=github" valign="middle" alt="GitHub clones|unique per day" /></a>
</p>
```
