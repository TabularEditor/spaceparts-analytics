# SpaceParts Analytics

Sample repository for the Tabular Editor blog series on source control and
CI/CD for Power BI semantic models. SpaceParts is a fictional company and
all data in the model is generated.

The repository holds one Power BI project, saved as text files instead of one
binary file:

| Item | What it holds |
| --- | --- |
| `Invoiced Sales.pbip` | the project file you open in Power BI Desktop |
| `Invoiced Sales.SemanticModel` | the semantic model, in TMDL |
| `Invoiced Sales.Report` | the report, in PBIR |

## Part 1

Read the article, "Source control for semantic models: deploy it right (part 1)":
https://tabulareditor.com/blog/source-control-for-semantic-models

### Read the history

The article starts with a number that changed overnight, and one question: did
the calculation change, or did the data change? The commit history answers the
question in seconds. Open the history and read the newest change:

https://github.com/TabularEditor/spaceparts-analytics/commits/main

You need nothing installed to do this.

### Follow along

You need Power BI Desktop, Git, and a GitHub or Azure DevOps account.

1. Download the report file from the Part 1 release:
   https://github.com/TabularEditor/spaceparts-analytics/releases/tag/part1
2. Open the file in Power BI Desktop. The file holds the model and the report,
   but no data, so the visuals stay empty. Part 1 does not need data.
3. Follow the article. You save the file as a Power BI project (PBIP) named
   Invoiced Sales, create your own empty repository, then commit and push the
   project folder.

You end with your own copy of this repository. You can
then compare your history with the history here.

### Azure DevOps

Azure DevOps imports a public Git repository directly. In your project, open
Repos, choose Import repository, and paste this URL:

```
https://github.com/TabularEditor/spaceparts-analytics.git
```

## Part 2

Read the article, "Validate every model change: deploy it right (part 2)":
https://tabulareditor.com/blog/validate-every-model-change-with-a-pipeline

Part 2 starts from the `part2` branch of this repository. The branch holds the
model and the report as they are at the start of the article, plus the
pipeline that the article builds:

| Item | What it holds |
| --- | --- |
| `azure-pipelines.yml` | the pipeline for Azure DevOps |
| `.github/workflows/invoiced-sales.yml` | the same pipeline for GitHub Actions |
| `tools/te` | the folder for the TE CLI Linux archive, which you add yourself |

### Start your own copy

You need Git, the TE CLI, Python 3.10 or later, and pbir-cli. The article
lists where to get each one. The checks read the project files, so you need no
data, no Power BI Desktop and no database connection.

Create your own empty repository on GitHub or in Azure DevOps. Then run these
commands, with the URL of your repository in the fourth line:

```
git clone --branch part2 https://github.com/TabularEditor/spaceparts-analytics.git
cd spaceparts-analytics
git branch -m part2 main
git remote set-url origin <your repository URL>
git push -u origin main
```

### Run the checks on your machine

Run the three checks from the repository folder:

```
te validate --model "Invoiced Sales.SemanticModel" --errors-only
te bpa run --model "Invoiced Sales.SemanticModel"
pbir validate "Invoiced Sales.Report" --fields --strict
```

At the start of part 2, `te validate` and `pbir validate` pass, and
`te bpa run` reports four violations on the `Product Key` column, one of them
at error level. The article shows how to fix them.

### Set up the pipeline

Both pipelines run on every push to `main`, and both need the TE CLI Linux
archive in your repository:

1. Download the Linux archive, `te-<version>-linux-x64.tar.gz`, from
   https://tabulareditor.com/download-tabular-editor-cli
2. Put the file in `tools/te`, then commit and push it.

Until the archive is in `tools/te`, every run fails at the step that sets up
the TE CLI.

In Azure DevOps, open Pipelines, choose New pipeline, pick your repository,
choose Existing Azure Pipelines YAML file, and select `/azure-pipelines.yml`.

In GitHub, there is nothing to set up: GitHub Actions runs
`.github/workflows/invoiced-sales.yml` on every push to `main`. You find the
runs on the Actions tab of your repository.

## Later parts

Parts 3 to 5 each start from their own branch in this repository, so
you can start at any part without doing the parts before it. Each branch
appears here when that part of the series publishes.

## Refreshing the model

The model reads from a SQL database that is not part of this sample, and the
download holds no data. Part 1 needs none: you save the file as a project, then
commit the text files. If you refresh the model in Power BI Desktop, the
refresh asks for database credentials that the sample does not publish, and the
refresh fails. Nothing else in the project breaks.

## License

This sample is released under the MIT License; see LICENSE. It bundles DAX
functions from two daxlib packages that carry their own copyright notice; see
NOTICE. Keep both files with any copy you publish.
