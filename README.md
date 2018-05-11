
<!-- README.md is generated from README.Rmd. Please edit that file -->

# piggyback

`piggyback` is basically a poor soul’s [git
LFS](https://git-lfs.github.com/). GitHub rejects commits containing
files larger than 50 Mb. `git LFS` is not only expensive, it also
[breaks GitHub’s collaborative
model](https://medium.com/@megastep/github-s-large-file-storage-is-no-panacea-for-open-source-quite-the-opposite-12c0e16a9a91).
(Someone wants to submit a PR with a simple edit to your docs, they
cannot fork) Unlike git LFS, `piggyback` doesn’t take over your standard
`git` client, it just perches comfortably on the shoulders of your
existing GitHub API. Data can be versioned by `piggyback`, but relative
to `git LFS` versioning is less strict: uploads can be set as a new
version or allowed to overwrite previously uploaded data. `piggyback`
works with both public and private repositories.

`piggyback` is inspired by
[datastorr](https://github.com/ropenscilabs/datastorr) from
[@richfitz](http://github.com/richfitz). While `datastorr` provides your
data in the R “data package” model of `storr` (your data lives inside an
R package that you install and is automatically loaded into memory),
`piggyback` tries to immitate the LFS model where your data files are
simply stored as part of your repository. This impementation uses R
wrappers to the GitHub API, but an identical client could be implemented
in any language with bindings to the GitHub API.

`piggyback` is designed around two possible workflows: (1) A high-level
workflow that parallels `git lfs`, and (2) a simple interface for
directly uploading and downloading individual data files to a GitHub
release.

## Installation

You can install the development version from
[GitHub](https://github.com/) with:

``` r
# install.packages("devtools")
devtools::install_github("cboettig/piggyback")
```

## LFS-style

`push` or `pull` all data files to/from GitHub:

``` r
# Not implemented yet
pb_pull("data/")
pb_push("data/")
```

By default this uses the latest available release of the currently
active GitHub repository. Specify a particular data release tag with the
optional argument, `tag`. Data files can be specified by giving a path
to a directory where data files are stored on the repo. Alternately,
file paths or types can be specified in a configuration file, see
`pb_watch()`. Identical files will not be transfered.

*Developer note*: GitHub API does not report hashes for files. We may
have to upload a metadata file to the release that provides these
hashes.

## Upload and download data

Alternatively, files can be uploaded and downloaded a la carte from the
attachements to the release of any GitHub repository.

Create a new release and upload data:

``` r
library(piggyback)

readr::write_tsv(mtcars, "mtcars.tsv.gz")

gh_new_release("cboettig/piggyback", "v0.0.4")
pb_upload("cboettig/piggyback", "mtcars.tsv.gz")
```

Note that you can also simply overwrite the a previous version of the
file on an existing release, rather than creating a new release every
time:

``` r
pb_upload("cboettig/piggyback", "mtcars.tsv.gz")
```

This is useful in scripts that may automatically upload their results,
or whenever a previous version of a data file is diposable.

Download the latest version or a specific version of the data:

``` r
pb_download("cboettig/piggyback", "mtcars.tsv.gz")
```

Or a specific version:

``` r
pb_download("cboettig/piggyback", "mtcars.tsv.gz", tag = "v0.0.4")
```

## Data archiving

`piggyback` is not intended as a data archiving solution. Permanent,
published data should be archived in a proper data repository with a
DOI. `piggyback` is meant only to lower the friction of working with
data during the research process.