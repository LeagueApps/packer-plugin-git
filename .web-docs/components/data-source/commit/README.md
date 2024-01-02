Type: `git-commit`

The commit data source is used to fetch information about a specific commit. 
It needs to be run inside an existing git repo.


## Required

There are no required configuration fields.


## Optional

- `path` (string) - The path to a directory inside your git repo. The plugin will
search for a git repo, starting in this directory and walking up through
parent directories. Defaults to '.' (the directory packer was executed in).

- `commit_ish` (string) - A [Commit-Ish value](https://git-scm.com/docs/gitglossary#Documentation/gitglossary.txt-aiddefcommit-ishacommit-ishalsocommittish)
(e.g. tag) pointing to the target commit object.
See [go-git ResolveRevision](https://pkg.go.dev/github.com/go-git/go-git/v5#Repository.ResolveRevision)
for the list of supported values. Defaults to 'HEAD'.


## Output

- `hash` (string) - The SHA1 checksum or "hash" value of the selected commit.
- `branches` (string) - The short names of branches at the selected commit.
- `author` (string) - The author of the commit, in standard `A U Thor <author@example.com>` format.
- `committer` (string) - The committer of the commit, in same format as author.
- `timestamp` (string) - The timestamp of the commit, in RFC3339 format (e.g. `2024-01-02T09:38:19Z`).
- `pgp_signature` (string) - The PGP signature attached to the commit.
- `message` (string) - The commit message.
- `tree_hash` (string) - The hash of the root tree of the commit.
- `parent_hashes` (list[string]) - The hashes of the parent commits.


## Example Usage

This example shows how a truncated commit sha can be appended
to an image name, with the author in the image description.

```hcl
data "git-commit" "cwd-head" { }

locals {
  truncated_sha = substr(data.git-commit.cwd-head.hash, 0, 8)
  author = data.git-commit.cwd-head.author
}

source "googlecompute" "example" {
  image_name = "image-${local.truncated_sha}"
  image_description = "Built from a commit by ${local.author}"
}

build {
  sources = ["source.googlecompute.example"]
}
```
