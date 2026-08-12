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

Read the article, "You're deploying it right this time (part 1)":
https://tabulareditor.com/blog/youre-deploying-it-right-this-time-part-1

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

## Later parts

Parts 2 to 5 each start from their own branch in this repository, so
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
