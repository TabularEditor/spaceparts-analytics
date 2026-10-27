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

## Part 3

Read the article, "Publish with a pipeline: deploy it right (part 3)":
https://tabulareditor.com/blog/deploy-your-semantic-model-from-a-pipeline

Part 3 starts from the `part3` branch of this repository. The branch holds the
model and the report as they are at the end of part 2, plus the pipeline that
part 3 builds: the checks from part 2, then a deploy of the model and the
report to a Fabric workspace.

| Item | What it holds |
| --- | --- |
| `azure-pipelines.yml` | the pipeline for Azure DevOps, with the `Deploy` stage |
| `.github/workflows/invoiced-sales.yml` | the same pipeline for GitHub Actions, with the `deploy-model` and `deploy-report` jobs |
| `tools/te` | the folder for the TE CLI Linux archive, which you add yourself |

### What you need

- A workspace on a Fabric or Premium capacity, with the capacity's XMLA
  endpoint set to read-write.
- The model published to that workspace once from Power BI Desktop, with its
  data source credential set in the model's settings. A deploy from the
  pipeline changes only the model's definition, so the model keeps that
  credential and its data.
- A service principal with a client secret, added to the workspace as
  Contributor, and allowed in the tenant setting "Service principals can call
  Fabric public APIs".

### Start your own copy

Run the same commands as in part 2, with `part3` in place of `part2`:

```
git clone --branch part3 https://github.com/TabularEditor/spaceparts-analytics.git
cd spaceparts-analytics
git branch -m part3 main
git remote set-url origin <your repository URL>
git push -u origin main
```

Then add the TE CLI Linux archive to `tools/te`, as in part 2.

### Change the names

Both pipeline files deploy to a workspace named `Order to Cash`, the
workspace of the article. Replace `Order to Cash` with the name of your
workspace in both files. If you renamed the model or the report, replace
`Invoiced Sales` too. pbir-cli needs the `.Workspace`, `.SemanticModel` and
`.Report` endings to tell which kind of item it handles, so keep those.

### Give the pipeline the service principal's credentials

The deploy steps sign in as the service principal with three values:
`AZURE_TENANT_ID`, `AZURE_CLIENT_ID` and `AZURE_CLIENT_SECRET`.

In Azure DevOps, open Pipelines, then Library, and create a variable group
named `fabric-deploy` with those three variables. Mark `AZURE_CLIENT_SECRET`
as secret with the lock icon. The first run that reaches the `Deploy` stage
waits until someone who manages the variable group clicks Permit; later runs
do not wait.

In GitHub, open Settings, then Secrets and variables, then Actions, and add
the three values as repository secrets with the same names.

### Check which commit is live

The pipeline writes the commit it deploys into the model and the report as the
annotation `SourceCommit`. Read the model's annotation back from the workspace
with the TE CLI:

```
te query -s "Order to Cash" -d "Invoiced Sales" -q 'EVALUATE FILTER(INFO.ANNOTATIONS(), [Name] = "SourceCommit")'
```

This works in PowerShell 7 and in bash. The article shows the quoting for the
command prompt and for Windows PowerShell 5.1.

Read the report's annotation with pbir-cli, from an empty folder:

```
pbir download "Order to Cash.Workspace/Invoiced Sales.Report" -o live
cd live
pbir annotations list "Invoiced Sales.Report"
```

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
