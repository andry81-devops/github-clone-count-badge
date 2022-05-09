<p align="center">
  <a href="https://github.com/andry81-stats/github-clone-count-badge--gh-stats/commits/master/traffic/views">
    <img src="https://github.com/andry81-cache/andry81-devops--gh-content-cache/raw/master/repo/andry81-devops/github-clone-count-badge/badges/traffic/views/all.svg" valign="middle" alt="GitHub views|any|total" />
    <img src="https://github.com/andry81-cache/andry81-devops--gh-content-cache/raw/master/repo/andry81-devops/github-clone-count-badge/badges/traffic/views/all-14d.svg" valign="middle" alt="GitHub views|any|14d" /></a>
• <a href="https://github.com/andry81-stats/github-clone-count-badge--gh-stats/commits/master/traffic/views">
    <img src="https://github.com/andry81-cache/andry81-devops--gh-content-cache/raw/master/repo/andry81-devops/github-clone-count-badge/badges/traffic/views/unq.svg" valign="middle" alt="GitHub views|unique per day|total" />
    <img src="https://github.com/andry81-cache/andry81-devops--gh-content-cache/raw/master/repo/andry81-devops/github-clone-count-badge/badges/traffic/views/unq-14d.svg" valign="middle" alt="GitHub views|unique per day|14d" /></a>
</p>

<p align="center">
  <a href="https://github.com/andry81-stats/github-clone-count-badge--gh-stats/commits/master/traffic/clones">
    <img src="https://github.com/andry81-cache/andry81-devops--gh-content-cache/raw/master/repo/andry81-devops/github-clone-count-badge/badges/traffic/clones/all.svg" valign="middle" alt="GitHub clones|any|total" />
    <img src="https://github.com/andry81-cache/andry81-devops--gh-content-cache/raw/master/repo/andry81-devops/github-clone-count-badge/badges/traffic/clones/all-14d.svg" valign="middle" alt="GitHub clones|any|14d" /></a>
• <a href="https://github.com/andry81-stats/github-clone-count-badge--gh-stats/commits/master/traffic/clones">
    <img src="https://github.com/andry81-cache/andry81-devops--gh-content-cache/raw/master/repo/andry81-devops/github-clone-count-badge/badges/traffic/clones/unq.svg" valign="middle" alt="GitHub clones|unique per day|total" />
    <img src="https://github.com/andry81-cache/andry81-devops--gh-content-cache/raw/master/repo/andry81-devops/github-clone-count-badge/badges/traffic/clones/unq-14d.svg" valign="middle" alt="GitHub clones|unique per day|14d" /></a>
</p>

<p align="center">
  <a href="https://github.com/andry81/donate"><img src="https://github.com/andry81-cache/andry81--gh-content-cache/raw/master/common/badges/donate/donate.svg" valign="middle" alt="donate" /></a>
</p>

---

> This implementation is based on that: https://github.com/MShawon/github-clone-count-badge

> :warning: Implementation is marked as obsolete, there is a better approach with GitHub composite actions: https://github.com/andry81-devops/github-accum-stats

With some advantages:

1. Repository to track and repository to store traffic statistic are different, and you may directly point the statistic as commits list: `https://github.com/{{REPO_OWNER}}/{{REPO}}--gh-stats/commits/master/traffic/clones`
2. Workflow is used [accum-traffic-clones.sh](https://github.com/andry81-devops/gh-workflow/blob/3ae1a8f3c0adb04884799d733ff336f4e6f18b5d/bash/~obsolete/github/accum-traffic-clones.sh) bash script to accumulate traffic clones
3. The script accumulates statistic both into a single file and into a set of files grouped by year and allocated per day: `traffic/clones/by_year/YYYY/YYYY-MM-DD.json`

You need 3 repositories:

1. Repository which clone statistic you want to track: `myrepo`
2. Repository, where clone statistic will be saved: `myrepo--gh-stats`
3. Repository, where to store github workflow scripts: `gh-workflow`.<br>
   You can fork it from here: https://github.com/andry81-devops/gh-workflow

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

[.github/workflows/myrepo-gh-clone-stats.yml](https://github.com/andry81-devops/github-clone-count-badge/blob/379a1ad31d4828c60406a6c1d0b325bea7ae1915/.github/workflows/myrepo-gh-clone-stats.yml)

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
