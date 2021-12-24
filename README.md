<!-- -->
<p align="center">
  <a href="#"><img src="https://hits.seeyoufarm.com/api/count/incr/badge.svg?url=https%3A%2F%2Fgithub.com%2Fandry81%2Fgithub-clone-count-badge&count_bg=%2379C83D&title_bg=%23555555&icon=&icon_color=%23E7E7E7&title=hits&edge_flat=false" valign="middle" alt="hits" /></a>
</p>
<!-- -->

---

> This implementation is based on that: https://github.com/MShawon/github-clone-count-badge

> :warning: Implementation is marked as obsolete, there is a better approach with GitHub composite actions: https://github.com/andry81/github-accum-stats

With some advantages:

1. Repository to track and repository to store traffic statistic are different, and you may directly point the statistic as commits list: `https://github.com/{{REPO_OWNER}}/{{REPO}}--gh-stats/commits/master/traffic/clones`
2. Workflow is used [accum-traffic-clones.sh](https://github.com/andry81/gh-workflow/blob/3ae1a8f3c0adb04884799d733ff336f4e6f18b5d/bash/~obsolete/github/accum-traffic-clones.sh) bash script to accumulate traffic clones
3. The script accumulates statistic both into a single file and into a set of files grouped by year and allocated per day: `traffic/clones/by_year/YYYY/YYYY-MM-DD.json`

You need 3 repositories:

1. Repository which clone statistic you want to track: `myrepo`
2. Repository, where clone statistic will be saved: `myrepo--gh-stats`
3. Repository, where to store github workflow scripts: `gh-workflow`.<br>
   You can fork it from here: https://github.com/andry81/gh-workflow

You need to attach a personal access token (PAT) into the repository being requested for statistic and obtain the push permission (`repo`->`public_repo`):

* `myrepo` -> `READ_STATS_TOKEN`

> :information_source: The `myrepo--gh-stats` repository does not require a separate PAT token as long as it is owned by the same repository owner.

To generate PAT: https://docs.github.com/en/github/authenticating-to-github/creating-a-personal-access-token

To attach PAT: https://docs.github.com/en/actions/reference/encrypted-secrets#creating-encrypted-secrets-for-a-repository

The `myrepo--gh-stats` repository should contain 1 file:

`traffic/clones/latest-accum.json`:

```
{
  "count_outdated" : 0,
  "uniques_outdated" : 0,
  "count" : 0,
  "uniques" : 0,
  "clones" : []
}
```

The `myrepo` repository should contain 1 file:

[.github/workflows/myrepo-gh-clone-stats.yml](https://github.com/andry81/github-clone-count-badge/blob/master/.github/workflows/myrepo-gh-clone-stats.yml)

> :warning: You must replace all placeholder into respective values:

* `{{REPO_OWNER}}` -> repository owner
* `{{REPO}}` -> `myrepo`

After the github workflow yaml file is commited and pushed, you can run the action from the `Actions` tab in the `myrepo` repository.

After that you can add badges to reference a repository statistic:

```
<p align="center">
  <a href="https://github.com/{{REPO_OWNER}}/{{REPO}}--gh-stats/commits/master/traffic/clones">
    <img src="https://img.shields.io/badge/dynamic/json?color=success&label=Github%20clones|all&query=count&url=https://github.com/{{REPO_OWNER}}/{{REPO}}--gh-stats/raw/master/traffic/clones/latest-accum.json?raw=True&logo=github" valign="middle" alt="GitHub clones|all" />
  </a>
• <a href="https://github.com/{{REPO_OWNER}}/{{REPO}}--gh-stats/commits/master/traffic/clones">
    <img src="https://img.shields.io/badge/dynamic/json?color=success&label=Github%20clones|unq&query=uniques&url=https://github.com/{{REPO_OWNER}}/{{REPO}}--gh-stats/raw/master/traffic/clones/latest-accum.json?raw=True&logo=github" valign="middle" alt="GitHub clones|unique per day" />
  </a>
</p>
```
