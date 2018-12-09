
Murtaza Jafferji
December 12th, 2018

# Exploring Risk in Top 10 Software Ecosystems

## Introduction

According to Forrester and Gartner, between 80-90% of all software developers use open source components within their applications.<sup>[\[1\]](https://snyk.io/stateofossecurity/)</sup> Incorporating open source components into applications may dramatically speed up development time and reduce costs, but there are large risks involved with having open source dependencies, due to the security, licensing, and the future maintenance of these dependencies. In addition, each new package pulls in a network of additional dependencies of its own, called transitive dependencies.

As a developer, it’s almost impossible to know everything about each of these dependencies. An illustrative example of the risk posed by the inclusion of open source dependencies is an event that took place this year. On September 9, 2018, a popular package called *event-stream*, took *flatmap-stream* as a dependency. On October 5, 2018, an updated was published to the package, *flatmap-stream*, which had some malicious code hiding at the end of its minified build. Due to their dependence on *event-stream*, thousands of applications suddenly took a transitive dependency on the malicious package, *flatmap-stream*. One of these applications, was *copay-dash*, a popular bitcoin platform. 

In this highly targetted attack, the malicious code stole users’ private keys for cryptocurrency wallets and sent those keys along with account details to a remote server controlled by hackers. The malicious code was added in September and was not noticed for two months, only because the attacker mistakenly incldued the deprecated crypto.createDecipher rather than crypto.createDecipheriv, which raised a suspicious deprecation warning in another library that consumed event-stream.<sup>[\[2\]](https://schneid.io/blog/event-stream-vulnerability-explained/)</sup>

Due to the trend in open source communities of including a growing number of packages as dependencies over time, the risks posed by these inclusions is increasing. Thus, it is imperative that developers have a more cautious approach to including open source dependencies. In this analyis, we hope to make developers aware of the the level of risk across various factors within the top 10 most used software ecosystems and identify the most used packages which exhibit risk.

For this analysis, we will consider the following factors as indications of risk: 
* *Number of dependencies* (including transitive dependencies), which indicates the number of points of failure
* *Total open issues for all dependencies*, which can be correlated to number of bugs or vulnerabilities 
* *Unmaintaned or deprecated status*, which indicates the project will not be fixed if vulnerabilities or bugs are found
* *Only one contributor*, which potentially indicates that on maintainer can publish code unchecked 
* *Not updated in the past 12 months*, which potentially indicates an unmaintained project
* *Missing README*, which is indicative of poor documentation and quality
* *Missing LICENSE*, which can produce a legal risk

**Human-centered design inspiration**

The decision to pursue this project was inspired by the desire to bring attention to the risks involved in software development that affect the lives of all technology users worldwide. Data breaches that involve personal information are becoming increasingly common, and if developers do not take more care in auditing the dependency trees of their applications, the risk for breaches involving personal data will continue to grow.

## Related Work

A study published in October, 2017 titled, An Empirical Comparison of Dependency Network Evolution in Seven Software Packaging Ecosystems<sup>[\[3\]](https://arxiv.org/pdf/1710.04936.pdf)</sup>, assesses the quality of different package dependency networks, but does not explore the network of millions of end user applications that depend on these packages.
    
In an attempt to try and provide a global view of open source software's security health, Synk, a network security company that helps developers stay secure, created the 2017 State of Open Source Security Report<sup>[\[1\]](https://snyk.io/stateofossecurity/pdf/The%20State%20of%20Open%20Source.pdf)</sup>. Here are a few key findings of the report:
> * The number of open source packages indexed has exploded in the past year: Rubygems 10%, Python 32%, Maven 28%, npm 57%. 6.3 billion Python packages have been downloaded using PyPi and over 87.3 billion Node packages using npm.
> * 79.5% of open-source maintainers say that they have no public-facing disclosure policy in place, and those that do are more than three times as likely to have a vulnerability disclosed to them privately.
> * Of 433,000 sites tested, 77% run at least one client-side JavaScript library with a known security vulnerability.
> * There’s no guarantee of the security knowledge of an open source maintainer. Only 16.8% of maintainers consider their security  know-how to be high, and 43.7%  have never conducted a security audit on their code.
> * The National Vulnerability Database (NVD) has seen a large increase in vulnerabilities, but coverage can be an issue, with npm having 11% coverage.

A notable limitation of Synk's approach is that it looks at the adoption rates of open source software in terms of number of downloads and the number of packages published, but these numbers are not a good indicators of adoption<sup>[\[4\]](https://blog.tidelift.com/dont-believe-the-download-numbers-when-evaluating-open-source-projects)</sup>.

## Research Questions

We will instead use the number of dependencies across various ecosystems as our indicator of adoption and answer the following questions:

### Finding the top 10 software ecosystems
1. What languages have the highest number of repositories?
2. Are the number of average number of dependencies per repository increasing over time?

### Ranking ecosystems by individual risk factors
1. What languages have the highest average number of dependencies per repository?
2. What languages have the highest average number of total open issues for all dependencies per repository?
3. What languages have the highest proportion of repositories with at least one dependency with an unmaintained/deprecated status?
4. What languages have the highest proportion of repositories with at least one dependency with only one contributor?
5. What languages have the highest proportion of repositories with at least one dependency that has not been updated in the past 12 months?
6. What languages have the highest proportion of repositories with at least one dependency with a missing or non-compliant license?
7. What languages have the highest proportion of repositories with at least one dependency with a missing README?

### Identifying most used projects that exhibit risk
1. What are the most used projects that have an unmaintained/deprecated status per language?
2. What are the most used projects that have only one contributor per language?
3. What are the most used projects that have not been updated in the past 12 months per language?
4. What are the most used projects that have a missing or non-compliant licenses per language?
5. What are the most used projects that have a missing README per language?

## Dataset
### Datset used in this analysis
>[Libraries.io Open Source Repository and Dependency Metadata](https://libraries.io/data)
>
>Libraries.io gathers data from 36 package managers and 3 source code repositories. We track over 2.7m unique open source packages, 33m repositories and 235m interdependencies between them. This gives Libraries.io a unique understanding of open source software. An understanding that we want to share with you. 
>
>In this release you will find data about software distributed and/or crafted publicly on the Internet. You will find information about its development, its distribution and its relationship with other software included as a dependency. You will not find any information about the individuals who create and maintain these projects.
>
>This public dataset is hosted in Google BigQuery and is included in BigQuery's 1TB/mo of free tier processing. This means that each user receives 1TB of free BigQuery processing every month, which can be used to run queries on this public dataset. Watch this short video to learn how to get started quickly using BigQuery to access public datasets.
>
>[Source](https://console.cloud.google.com/marketplace/details/libraries-io/librariesio)

### What is BigQuery?
>BigQuery is Google's fully managed, NoOps, low cost analytics database. With BigQuery you can query terabytes of data without needing a database administrator or any infrastructure to manage. BigQuery uses familiar SQL and a pay-only-for-what-you-use charging model. BigQuery allows you to focus on analyzing data to find meaningful insights.
>
>[Source](https://codelabs.developers.google.com/codelabs/bigquery-github/index.html?index=..%2F..%2Findex#0)


### License
>This dataset is released under the [Creative Commons Attribution-ShareAlike 4.0 International Licence](https://creativecommons.org/licenses/by-sa/4.0/).
>
>This licence provides the user with the freedom to use, adapt and redistribute this data. In return the user must publish any derivative work under a similarly open licence, attributing Libraries.io as a data source. The full text of the licence is included in the data.
>
> [Source](https://zenodo.org/record/1196312#.W_v3OHdFzZs)


### Tables

#### projects

A project is a piece of software available on any one of the 33 package managers supported by Libraries.io.

|Field name|Type|Mode|Description|
|--- |--- |--- |--- |
|id|INTEGER|NULLABLE|The unique primary key of the project in the Libraries.io database.|
|platform|STRING|NULLABLE|The name of the Package manager the project is available on.|
|name|STRING|NULLABLE|The name of the project, unique by Platform (case sensitive).|
|created_timestamp|TIMESTAMP|NULLABLE|The timestamp of when Libraries.io first discovered the project.|
|updated_timestamp|TIMESTAMP|NULLABLE|The timestamp of when Libraries.io last saved a change to the project.|
|description|STRING|NULLABLE|Description provided by the package manager, falling back to description from repository if empty.|
|keywords|STRING|NULLABLE|Comma separated array of keywords if supported by package manager.|
|homepage_url|STRING|NULLABLE|URL of webpage or repository as provided by package managers that support it.|
|licenses|STRING|NULLABLE|Comma separated array of SPDX identifiers for licenses declared in package manager meta data or submitted manually by Libraries.io user via "project suggection" feature.|
|repository_url|STRING|NULLABLE|URL of source code repository declared in package manager metadata or submitted manually by Libraries.io user via "project suggection" feature.|
|versions_count|INTEGER|NULLABLE|Number of published versions of the project found by Libraries.io.|
|sourcerank|INTEGER|NULLABLE|Libraries.io defined score based on quality, popularity and community metrics.|
|latest_release_publish_timestamp|TIMESTAMP|NULLABLE|Time of the latest release detected by Libraries.io (ordered by semver, falling back to publish date for invalid semver).|
|latest_release_number|STRING|NULLABLE|Version number of the latest release detected by Libraries.io (ordered by semver, falling back to publish date for invalid semver).|
|package_manager_id|INTEGER|NULLABLE|Unique ID of project from package manager API, only currently used by PlatformIO.|
|dependent_projects_count|INTEGER|NULLABLE|Number of other projects that declare the project as a dependency in one or more of their versions.|
|language|STRING|NULLABLE|Primary programming language the project is written in, pulled from the repository if source is hosted on GitHub.|
|status|STRING|NULLABLE|Either Active, Deprecated, Unmaintained, Help Wanted, Removed, no value also means active. Updated when detected by Libraries.io or submitted manually by Libraries.io user via "project suggection" feature.|
|last_synced_timestamp|TIMESTAMP|NULLABLE|Timestamp of when Libraries.io last synced the project from it's package manager API.|
|dependent_repositories_count|INTEGER|NULLABLE|The total count of open source repositories that list the project as a dependency as detected by Libraries.io.|
|repository_id|INTEGER|NULLABLE|The unique primary key of the repository for this project in the Libraries.io database.|

#### repositories

A Libraries.io repository represents a publicly accessible source code repository from either github.com, gitlab.com or bitbucket.org. Repositories are distinct from Projects, they are not distributed via a package manager and typically an application for end users rather than component to build upon.

|Field name|Type|Mode|Description|
|--- |--- |--- |--- |
|id|INTEGER|NULLABLE|The unique primary key of the repository in the Libraries.io database.|
|host_type|STRING|NULLABLE|Which website the repository is hosted on, either GitHub, GitLab or Bitbucket.|
|name_with_owner|STRING|NULLABLE|The repository name and owner seperated by a slash, also maps to the url slug on the given repository host e.g. librariesio/libraries.io.|
|description|STRING|NULLABLE|Description of repository.|
|fork|BOOLEAN|NULLABLE|Is the repository a fork of another.|
|created_timestamp|TIMESTAMP|NULLABLE|Timestamp of when the repository was created on the host.|
|updated_timestamp|TIMESTAMP|NULLABLE|Timestamp of when the repository was last saved by Libraries.io.|
|last_pushed_timestamp|TIMESTAMP|NULLABLE|Timestamp of when the repository was last pushed to, only available for GitHub repositories.|
|homepage_url|STRING|NULLABLE|URL of a declared homepage or other website for the repository.|
|size|INTEGER|NULLABLE|Size of the repository in kilobytes, only available for GitHub and Bitbucket.|
|stars_count|INTEGER|NULLABLE|Number of stars on the repository, only available for GitHub and GitLab.|
|language|STRING|NULLABLE|Primary programming language the project is written in, only available for GitHub and Bitbucket.|
|issues_enabled|BOOLEAN|NULLABLE|Is the bug tracker enabled for this repository?.|
|wiki_enabled|BOOLEAN|NULLABLE|Is the wiki enabled for this repository?.|
|pages_enabled|BOOLEAN|NULLABLE|Is GitHub pages enabled for this repository? only possible for GitHub.|
|forks_count|INTEGER|NULLABLE|Number of forks of this repository.|
|mirror_url|STRING|NULLABLE|URL of the repositroy of which this is a mirror of, only present if this repository is a mirror of another.|
|open_issues_count|INTEGER|NULLABLE|Number of open issues on the repository bug tracker, only available for GitHub and GitLab.|
|default_branch|STRING|NULLABLE|Primary branch of the repository.|
|watchers_count|INTEGER|NULLABLE|Number of subscribers to all notifications for the repository, only available for GitHub and Bitbucket.|
|uuid|STRING|NULLABLE|ID of the repository on the remote host, not unique between GitLab and GitHub repositories.|
|fork_source_name_with_owner|STRING|NULLABLE|If the repository is a fork, the repository name and owner seperated by a slash of the repository if was forked from.|
|license|STRING|NULLABLE|SPDX identifier of the license of the repository, only available for GitHub repositories.|
|contributors_count|INTEGER|NULLABLE|Number of unique contributors that have committed to the default branch.|
|readme_filename|STRING|NULLABLE|If a readme file has been detected, the full name of the readme file, e.g README.md.|
|changelog_filename|STRING|NULLABLE|If a changelog file has been detected, the full name of the changelog file, e.g changelog.txt.|
|contributing_guidelines_filename|STRING|NULLABLE|If a contributing guidelines file has been detected, the full name of the contributing guidelines file, e.g contributing.md.|
|license_filename|STRING|NULLABLE|If a license file has been detected, the full name of the license file, e.g LICENSE.|
|code_of_conduct_filename|STRING|NULLABLE|If a code of conduct file has been detected, the full name of the code of conduct file, e.g code_of_conduct.md.|
|security_threat_model_filename|STRING|NULLABLE|If a Security Threat Model file has been detected, the full name of the Security Threat Model file, e.g threatmodel.md.|
|security_audit_filename|STRING|NULLABLE|If a Security Audit file has been detected, the full name of the Security Audit file, e.g security.md.|
|status|STRING|NULLABLE|Either Active, Deprecated, Unmaintained, Help Wanted, Removed, no value also means active. Updated when detected by Libraries.io or su. manually by Libraries.io user via "repo suggection" feature.|
|last_synced_timestamp|TIMESTAMP|NULLABLE|Timestamp of when Libraries.io last synced the repository from the host API.|
|sourcerank|INTEGER|NULLABLE|Libraries.io defined score based on quality, popularity and community metrics.|
|display_name|STRING|NULLABLE|Display name for the repository, only available for GitLab repositories.|
|scm_type|STRING|NULLABLE|Type of source control repository uses, always "git" for GitHub and GitLab.|
|pull_requests_enabled|STRING|NULLABLE|Are pull requests enabled for this repository? Only available for GitLab repositories.|
|logo_url|STRING|NULLABLE|Custom logo url for repository, only available for GitLab repositories.|
|keywords|STRING|NULLABLE|Comma separated array of keywords, called "topics" on GitHub, only available for GitHub and GitLab.|
|an|STRING|NULLABLE||

#### projects_with_repository_fields

This is an alternative projects export that denormalizes a projects related source code repository inline to reduce the need to join between two data sets.

|Field name|Type|Mode|Description|
|--- |--- |--- |--- |
|id|INTEGER|NULLABLE|The unique primary key of the project in the Libraries.io database.|
|platform|STRING|NULLABLE|The name of the Package manager the project is available on.|
|name|STRING|NULLABLE|The name of the project, unique by Platform (case sensitive).|
|created_timestamp|TIMESTAMP|NULLABLE|The timestamp of when Libraries.io first discovered the project.|
|updated_timestamp|TIMESTAMP|NULLABLE|The timestamp of when Libraries.io last saved a change to the project.|
|description|STRING|NULLABLE|Description provided by the package manager, falling back to description from repository if empty.|
|keywords|STRING|NULLABLE|Comma separated array of keywords if supported by package manager.|
|homepage_url|STRING|NULLABLE|URL of webpage or repository as provided by package managers that support it.|
|licenses|STRING|NULLABLE|Comma separated array of SPDX identifiers for licenses declared in package manager meta data or submitted manually by Libraries.io user via "project suggection" feature.|
|repository_url|STRING|NULLABLE|URL of source code repository declared in package manager metadata or submitted manually by Libraries.io user via "project suggection" feature.|
|versions_count|INTEGER|NULLABLE|Number of published versions of the project found by Libraries.io.|
|sourcerank|INTEGER|NULLABLE|Libraries.io defined score based on quality, popularity and community metrics.|
|latest_release_publish_timestamp|TIMESTAMP|NULLABLE|Time of the latest release detected by Libraries.io (ordered by semver, falling back to publish date for invalid semver).|
|latest_release_number|STRING|NULLABLE|Version number of the latest release detected by Libraries.io (ordered by semver, falling back to publish date for invalid semver).|
|package_manager_id|INTEGER|NULLABLE|Unique ID of project from package manager API, only currently used by PlatformIO.|
|dependent_projects_count|INTEGER|NULLABLE|Number of other projects that declare the project as a dependency in one or more of their versions.|
|language|STRING|NULLABLE|Primary programming language the project is written in, pulled from the repository if source is hosted on GitHub.|
|status|STRING|NULLABLE|Either Active, Deprecated, Unmaintained, Help Wanted, Removed, no value also means active. Updated when detected by Libraries.io or submitted manually by Libraries.io user via "project suggection" feature.|
|last_synced_timestamp|TIMESTAMP|NULLABLE|Timestamp of when Libraries.io last synced the project from it's package manager API.|
|dependent_repositories_count|INTEGER|NULLABLE|The total count of open source repositories that list the project as a dependency as detected by Libraries.io.|
|repository_id|INTEGER|NULLABLE|The unique primary key of the repository for this project in the Libraries.io database.|
|repository_host_type|STRING|NULLABLE|Which website the repository is hosted on, either GitHub, GitLab or Bitbucket.|
|repository_name_with_owner|STRING|NULLABLE|The repository name and owner seperated by a slash, also maps to the url slug on the given repository host e.g. librariesio/libraries.io.|
|repository_description|STRING|NULLABLE|Description of repository.|
|repository_fork|BOOLEAN|NULLABLE|Is the repository a fork of another.|
|repository_created_timestamp|TIMESTAMP|NULLABLE|Timestamp of when the repository was created on the host.|
|repository_updated_timestamp|TIMESTAMP|NULLABLE|Timestamp of when the repository was last saved by Libraries.io.|
|repository_last_pushed_timestamp|TIMESTAMP|NULLABLE|Timestamp of when the repository was last pushed to, only available for GitHub repositories.|
|repository_homepage_url|STRING|NULLABLE|URL of a declared homepage or other website for the repository.|
|repository_size|INTEGER|NULLABLE|Size of the repository in kilobytes, only available for GitHub and Bitbucket.|
|repository_stars_count|INTEGER|NULLABLE|Number of stars on the repository, only available for GitHub and GitLab.|
|repository_language|STRING|NULLABLE|Primary programming language the project is written in, only available for GitHub and Bitbucket.|
|repository_issues_enabled|BOOLEAN|NULLABLE|Is the bug tracker enabled for this repository?.|
|repository_wiki_enabled|BOOLEAN|NULLABLE|Is the wiki enabled for this repository?.|
|repository_pages_enabled|BOOLEAN|NULLABLE|Is GitHub pages enabled for this repository? only possible for GitHub.|
|repository_forks_count|INTEGER|NULLABLE|Number of forks of this repository.|
|repository_mirror_url|STRING|NULLABLE|URL of the repositroy of which this is a mirror of, only present if this repository is a mirror of another.|
|repository_open_issues_count|INTEGER|NULLABLE|Number of open issues on the repository bug tracker, only available for GitHub and GitLab.|
|repository_default_branch|STRING|NULLABLE|Primary branch of the repository.|
|repository_watchers_count|INTEGER|NULLABLE|Number of subscribers to all notifications for the repository, only available for GitHub and Bitbucket.|
|repository_uuid|STRING|NULLABLE|ID of the repository on the remote host, not unique between GitLab and GitHub repositories.|
|repository_fork_source_name_with_owner|STRING|NULLABLE|If the repository is a fork, the repository name and owner seperated by a slash of the repository if was forked from.|
|repository_license|STRING|NULLABLE|SPDX identifier of the license of the repository, only available for GitHub repositories.|
|repository_contributors_count|INTEGER|NULLABLE|Number of unique contributors that have committed to the default branch.|
|repository_readme_filename|STRING|NULLABLE|If a readme file has been detected, the full name of the readme file, e.g README.md.|
|repository_changelog_filename|STRING|NULLABLE|If a changelog file has been detected, the full name of the changelog file, e.g changelog.txt.|
|repository_contributing_guidelines_filename|STRING|NULLABLE|If a contributing guidelines file has been detected, the full name of the contributing guidelines file, e.g contributing.md.|
|repository_license_filename|STRING|NULLABLE|If a license file has been detected, the full name of the license file, e.g LICENSE.|
|repository_code_of_conduct_filename|STRING|NULLABLE|If a code of conduct file has been detected, the full name of the code of conduct file, e.g code_of_conduct.md.|
|repository_security_threat_model_filename|STRING|NULLABLE|If a Security Threat Model file has been detected, the full name of the Security Threat Model file, e.g threatmodel.md.|
|repository_security_audit_filename|STRING|NULLABLE|If a Security Audit file has been detected, the full name of the Security Audit file, e.g security.md.|
|repository_status|STRING|NULLABLE|Either Active, Deprecated, Unmaintained, Help Wanted, Removed, no value also means active. Updated when detected by Libraries.io or su. manually by Libraries.io user via "repo suggection" feature.|
|repository_last_synced_timestamp|STRING|NULLABLE|Timestamp of when Libraries.io last synced the repository from the host API.|
|repository_sourcerank|INTEGER|NULLABLE|Libraries.io defined score based on quality, popularity and community metrics.|
|repository_display_name|STRING|NULLABLE|Display name for the repository, only available for GitLab repositories.|
|repository_scm_type|STRING|NULLABLE|Type of source control repository uses, always "git" for GitHub and GitLab.|
|repository_pull_requests_enabled|STRING|NULLABLE|Are pull requests enabled for this repository? Only available for GitLab repositories.|
|repository_logo_url|STRING|NULLABLE|Custom logo url for repository, only available for GitLab repositories.|
|repository_keywords|STRING|NULLABLE|Comma separated array of keywords, called "topics" on GitHub, only available for GitHub and GitLab.|
|bh|STRING|NULLABLE||

#### dependencies

Dependencies describe the relationship between a project and the software it builds upon. Dependencies belong to Version. Each Version can have different sets of dependencies. Dependencies point at a specific Version or range of versions of other projects.

|Field name|Type|Mode|Description|
|--- |--- |--- |--- |
|id|INTEGER|NULLABLE|The unique primary key of the dependency in the Libraries.io database.|
|platform|STRING|NULLABLE|The name of the Package manager the dependency is available on.|
|project_name|STRING|NULLABLE|The name of the project the dependency belongs to.|
|project_id|INTEGER|NULLABLE|The unique primary key of the project for this dependency in the Libraries.io database.|
|version_number|STRING|NULLABLE|The number of the version that the dependency belongs to.|
|version_id|INTEGER|NULLABLE|The unique primary key of the version for this dependency in the Libraries.io database.|
|dependency_name|STRING|NULLABLE|The name of the project that the dependency specifies.|
|dependency_platform|STRING|NULLABLE|The name of the package manager that the project that the dependency specifies is available from (only different for Atom).|
|dependency_kind|STRING|NULLABLE|The type of dependency, often declared for the phase of usage, e.g. runtime, test, development, build.|
|optional_dependency|STRING|NULLABLE|Is the dependency optional?.|
|dependency_requirements|STRING|NULLABLE|The version or range of versions that the dependency specifies, resolution of that to a particular version is package manager specific.|
|dependency_project_id|INTEGER|NULLABLE|The unique primary key of the project for this dependency in the Libraries.io database.|

#### repository_dependencies

A repository dependency is a dependency upon a Version from a package manager has been specified in a manifest file, either as a manually added dependency committed by a user or listed as a generated dependency listed in a lockfile that has been automatically generated by a package manager and committed.


|Field name|Type|Mode|Description|
|--- |--- |--- |--- |
|id|INTEGER|NULLABLE|The unique primary key of the repository dependency in the Libraries.io database.|
|host_type|STRING|NULLABLE|Which website the dependencys repository is hosted on, either GitHub, GitLab or Bitbucket.|
|repository_name_with_owner|STRING|NULLABLE|The repository name and owner seperated by a slash, also maps to the url slug on the given repository host e.g. librariesio/libraries.io.|
|repository_id|INTEGER|NULLABLE|The unique primary key of the repository for this dependency in the Libraries.io database.|
|manifest_platform|STRING|NULLABLE|Which package manager the dependency listed in the manifest should use.|
|manifest_filepath|STRING|NULLABLE|Path to the file where the dependency is declared within the repository.|
|git_branch|STRING|NULLABLE|Which branch was the manifest loaded from the repository.|
|manifest_kind|STRING|NULLABLE|Either manifest or lockfile, manifests are written by humans, lockfiles contain full resolved dependency tree.|
|optional|STRING|NULLABLE|Is the dependency optional?.|
|dependency_project_name|STRING|NULLABLE|The name of the project that the dependency specifies.|
|dependency_requirements|STRING|NULLABLE|The version or range of versions that the dependency specifies, resolution of that to a particular version is package manager specific.|
|dependency_kind|STRING|NULLABLE|The type of dependency, often declared for the phase of usage, e.g. runtime, test, development, build.|
|dependency_project_id|INTEGER|NULLABLE|The unique primary key of the project for this dependency in the Libraries.io database.|

## Analysis

### Setup
1. Install required packages via a terminal window
```bash
pip install -e git+https://github.com/SohierDane/BigQuery_Helper#egg=bq_helper
pip install plotly
pip install cufflinks
npm install -g electron@1.8.4 orca
pip install psutil
```
2. Restart the notebook Kernel (Kernel -> Restart)

We will perform the following analysis with Google BigQuery. Since this dataset receives continuous updates, in order to enable reproducibility of this report, results of all queries have been saved as csv files in the /data directory. If REPRODUCIBILITY_MODE is set to True, data will be read from these csv files and the Google BigQuery setup steps can be ignored. If REPRODUCIBILITY_MODE is set to False, queries will be run against the live BigQuery tables.


```python
REPRODUCIBILITY_MODE = True
```

### BigQuery client setup
Note: The following steps only need to be performed if REPRODUCIBILITY_MODE is set to False
> 1. In the GCP Console, go to the Create service account key page.
> [Go to the Create Service Account Key page](https://console.cloud.google.com/apis/credentials/serviceaccountkey?_ga=2.216172310.-16877527.1543232590&project=api-8220795080820646267-783724&folder&organizationId)
> 2. From the Service account drop-down list, select New service account.
> 3. In the Service account name field, enter a name .
> 4. From the Role drop-down list, select Project > Owner. 
> 5. Click Create. A JSON file that contains your key downloads to your computer.
> 6. Provide authentication credentials to your application code by setting the environment variable GOOGLE_APPLICATION_CREDENTIALS using the command below. Replace [PATH] with the file path of the JSON file that contains your service account key. This variable only applies to your current shell session, so if you open a new session, set the variable again. 
>
> [Source](https://cloud.google.com/bigquery/docs/reference/libraries#client-libraries-install-python)


```python
if not REPRODUCIBILITY_MODE: 
    # Example: %env GOOGLE_APPLICATION_CREDENTIALS=C:/Users/murtjaf/Downloads/Google Play Android Developer-c5edbdb9185f.json
    %env GOOGLE_APPLICATION_CREDENTIALS=[PATH] # set [PATH] with the file path of the JSON file that contains your service account key
```

    env: GOOGLE_APPLICATION_CREDENTIALS=C:/Users/murtjaf/Downloads/Google Play Android Developer-c5edbdb9185f.json
    

Use the [bq_helper](https://www.kaggle.com/sohier/introduction-to-the-bq-helper-package) package to create a bq_assistant, which will allow us to run read-only BigQuery SQL queries from within this Python environment.


```python
if not REPRODUCIBILITY_MODE:
    import bq_helper
    from bq_helper import BigQueryHelper
    bq_assistant = bq_helper.BigQueryHelper(active_project="bigquery-public-data",
                                       dataset_name="libraries_io")
```

Run the following code to create the /images and /data directories to save the outputs of the the BigQuery queries and matplotlib/plotly graphs.


```python
import os
if not os.path.exists('images'):
    os.makedirs('images')
if not os.path.exists('data'):
    os.makedirs('data')
```

Run the following code to define the helper functions that will be used to generate charts


```python
import plotly.offline as offline
import plotly.io as pio
import plotly.plotly as py
import pandas as pd
import cufflinks as cf
import plotly.graph_objs as go
from ast import literal_eval
import matplotlib.pyplot as plt
import re
import numpy as np

offline.init_notebook_mode(connected=True)
   
    
def run_query(query, question):
    """If REPRODUCIBILITY_MODE is True, load the data for the question from the csv file. 
       If REPRODUCIBILITY_MODE is False, run the query in Google BigQuery and save the results to a csv file
    
    Arguments:

        query -- SQL Query that will be run in Google BigQuery
        question -- Question for which the query will be run
    """
        
    filename = 'data/{0}.csv'.format(get_filename(question))
    if REPRODUCIBILITY_MODE:
        if not os.path.isfile(filename):
            raise Exception('File does not exist. Cannot run in REPRODUCIBILITY_MODE')
        response = pd.read_csv(filename) 
        if (len(response) > 0):
            for column in response:
                value = response[column][0]
                if isinstance(value, str):
                    if value.startswith('[') and value.endswith(']'):
                        response[column] = response[column].apply(literal_eval)
    else:
        print("{0:.2f} GB".format(bq_assistant.estimate_query_size(query)))
        response = bq_assistant.query_to_pandas(query)
        response.to_csv(filename, index=False)

    print("Data: {0}".format(filename))
    return response

def generate_chart(data, pivot_column, pivot_value_column, question, kind='bar', index=None, sum_values=True, grouped_by_column=None, list_column=None):
    """Generate Plotly chart and save to image file
    
    Arguments:

        data -- DataFrame that will be plotted
        pivot_column -- Column to use to make new frame’s columns
        pivot_value_column -- Column to use for populating new frame’s values
        question -- Question for which the chart is being generated
        kind -- Kind of chart (scatter, bar)
        index -- Column to use to make new frame’s index
        sum_values -- If True, sum the pivoted DataFrame
        grouped_by_column -- Column that data was grouped by
        list_column -- Column that contains values in a list 
    """
        
    def generate_figure(data):
        pivoted = data.pivot(index=index, columns=pivot_column, values=pivot_value_column)
        sorted_pivoted = pivoted.sum().sort_values(ascending=False) if sum_values else pivoted
        return sorted_pivoted.iplot(asFigure=True, kind=kind)
    figure = None
    if list_column is not None:
        plotly_data = []
        def merge_column(row):
            for column, value in row.iteritems():
                if column != list_column:
                    for obj in row[list_column]:
                        obj[column] = row[column]
        data.apply(lambda x: merge_column(x), axis=1)
        for i, row in data.iterrows():
            v =  pd.DataFrame(row[list_column])
            fig = generate_figure(v)
            bar = fig.data[0]
            bar.name = row[grouped_by_column]
            bar.marker.color = None
            plotly_data.append(bar)
        figure = go.Figure(data=plotly_data)
    else:
        figure = generate_figure(data)
        
    json = figure.to_plotly_json()

    if kind == 'bar':
        v = np.vectorize(format_value)
        for bar in json['data']:
            bar['text'] = v(bar['y'])
            bar['textposition'] = 'outside'
            bar['x'] = v(bar['x'])
    
    json['layout'] = {'title': question, 'xaxis': {'title': pivot_column, 'automargin': True, 'tickangle': -45},'yaxis': {'title': pivot_value_column, 'automargin': True}}
    offline.iplot(json)

    filename = 'images/{0}.png'.format(get_filename(question))
    
    json['layout']['width'] = 1280 
    json['layout']['height'] = 720 
    pio.write_image(json, filename)
    print("Chart image: {0}".format(filename))

# Reference: https://stackoverflow.com/questions/295135/turn-a-string-into-a-valid-filename
def get_filename(s):
    """Generate a filename given a string by stripping invalid characters, removing stopwords, and replacing spaces with _
    
    Arguments:

        s -- Input string
    """
        
    s = str(s).strip().lower()
    s = re.sub(r'(?u)[^-\s\w.]', '', s)
    stopwords = ['what','have', 'the', 'of', 'in', 'are', 'has', 'with', 'that', 'a', 'an', 'br']
    querywords = s.split()
    resultwords  = [word for word in querywords if word.lower() not in stopwords]
    result = '_'.join(resultwords)
    return result

def format_value(x):
    """Format floats to two decimal places, add thousands separator for numbers, and returns value after last : or / for strings
    
    Arguments:

        x -- Input value that is formatted differently depending on type
    """
        
    if isinstance(x, float):
        return '{:,.2f}'.format(x).replace('.00','')
    elif isinstance(x, int):
        return '{:,}'.format(x)
    elif isinstance(x, str):
        return re.split('[:/]', x)[-1]
    else:
        return x
```


<script type="text/javascript">window.PlotlyConfig = {MathJaxConfig: 'local'};</script><script type="text/javascript">if (window.MathJax) {MathJax.Hub.Config({SVG: {font: "STIX-Web"}});}</script><script>requirejs.config({paths: { 'plotly': ['https://cdn.plot.ly/plotly-latest.min']},});if(!window._Plotly) {require(['plotly'],function(plotly) {window._Plotly=plotly;});}</script>


## Finding the top 10 software ecosystems

### What languages have the highest number of repositories?
We first want to find the top 10 software ecosystems by finding the languages that have the largest number of repositories.


```python
query = """
SELECT
    language,
    COUNT(*) AS repositories_count
FROM
    `bigquery-public-data.libraries_io.repositories`
WHERE 
    language <> ''
GROUP BY
    language
ORDER BY
    repositories_count DESC
LIMIT
    10;
        """

question = 'What languages have the highest number of repositories?'
response = run_query(query, question)
generate_chart(response, 'language', 'repositories_count', question)
```

    Data: data/languages_highest_number_repositories.csv
    


<div id="dcdc751b-7a13-4433-a3d3-1ee88551cde3" style="height: 525px; width: 100%;" class="plotly-graph-div"></div><script type="text/javascript">require(["plotly"], function(Plotly) { window.PLOTLYENV=window.PLOTLYENV || {};window.PLOTLYENV.BASE_URL="https://plot.ly";Plotly.newPlot("dcdc751b-7a13-4433-a3d3-1ee88551cde3", [{"marker": {"color": "rgba(226, 74, 51, 0.6)", "line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "None", "orientation": "v", "text": ["3,273,655", "2,399,320", "1,595,189", "1,366,043", "1,091,562", "1,090,512", "787,519", "728,982", "642,223", "610,950"], "textposition": "outside", "uid": "e43292c7-56eb-4a9b-917d-8c1ff5b9bd04", "x": ["JavaScript", "Java", "Python", "HTML", "PHP", "Ruby", "CSS", "C++", "C#", "C"], "y": [3273655.0, 2399320.0, 1595189.0, 1366043.0, 1091562.0, 1090512.0, 787519.0, 728982.0, 642223.0, 610950.0], "type": "bar"}], {"title": "What languages have the highest number of repositories?", "xaxis": {"automargin": true, "tickangle": -45, "title": "language"}, "yaxis": {"automargin": true, "title": "repositories_count"}}, {"showLink": true, "linkText": "Export to plot.ly", "plotlyServerURL": "https://plot.ly"})});</script><script type="text/javascript">window.addEventListener("resize", function(){window._Plotly.Plots.resize(document.getElementById("dcdc751b-7a13-4433-a3d3-1ee88551cde3"));});</script>


    Chart image: images/languages_highest_number_repositories.png
    

Run the following command to set the langugages string that will be used in the queries through the rest of the analysis using the results of the query above. In order to perform the analysis against another set of languages, set the languages string to 
REPRODUCIBILITY_MODE to False.


```python
languages = "'{0}'".format("', '".join(pd.read_csv('data/languages_highest_number_repositories.csv')['language']))
# Set manually by uncommenting the lines below
# languages = "'R', 'Python', 'Go'"
# REPRODUCIBILITY_MODE = False
print(languages)
```

    'JavaScript', 'Java', 'Python', 'HTML', 'PHP', 'Ruby', 'CSS', 'C++', 'C#', 'C'
    

### Are the number of average number of dependencies per repository increasing over time?
We want to verify our claim that there is a trend in open source communities to include a growing number of packages as dependencies over time. Note: We determine the date of the project by the last_pushed_timestamp.


```python
query = """
SELECT 
    language,
    AVG(count) AS average_dependency_count,
    FORMAT_TIMESTAMP('%Y', last_pushed_timestamp) as last_updated_year
FROM
    (SELECT
      repositories.language AS language,
      repositories.id,
      repositories.last_pushed_timestamp AS last_pushed_timestamp,
      COUNT(*) AS count
    FROM
      `bigquery-public-data.libraries_io.repositories` AS repositories
    JOIN
      `bigquery-public-data.libraries_io.repository_dependencies` AS repository_dependencies
    ON 
        repositories.id = repository_dependencies.repository_id
    WHERE 
        repositories.language IN ({0})
    AND
        repositories.last_pushed_timestamp IS NOT NULL
    GROUP BY
        repositories.language,
        repositories.id,
        repositories.last_pushed_timestamp) AS T1
GROUP BY
    language,
    last_updated_year
ORDER BY
    last_updated_year ASC;
""".format(languages)

question = 'Are the number of average number of dependencies per repository increasing over time?'
response = run_query(query, question)
generate_chart(response, 'language', 'average_dependency_count', question, kind='scatter', index='last_updated_year', sum_values=False)
```

    Data: data/number_average_number_dependencies_per_repository_increasing_over_time.csv
    


<div id="8e507a04-abf9-44c7-985d-ee4837510a0d" style="height: 525px; width: 100%;" class="plotly-graph-div"></div><script type="text/javascript">require(["plotly"], function(Plotly) { window.PLOTLYENV=window.PLOTLYENV || {};window.PLOTLYENV.BASE_URL="https://plot.ly";Plotly.newPlot("8e507a04-abf9-44c7-985d-ee4837510a0d", [{"line": {"color": "rgba(226, 74, 51, 1.0)", "dash": "solid", "shape": "linear", "width": 1.3}, "mode": "lines", "name": "C", "text": "", "uid": "62493ef1-83b2-4c21-a73c-5e06d2b78800", "x": [2008, 2009, 2010, 2011, 2012, 2013, 2014, 2015, 2016, 2017, 2018], "y": [25.000000000000004, 27.765957446808514, 30.770491803278688, 20.109243697478988, 33.53100775193799, 52.183639398998345, 24.719424460431647, 26.494454713493532, 34.575888717156104, 38.180584016393425, 52.021915584415595], "type": "scatter"}, {"line": {"color": "rgba(62, 111, 176, 1.0)", "dash": "solid", "shape": "linear", "width": 1.3}, "mode": "lines", "name": "C#", "text": "", "uid": "5e94ddb1-fc45-4573-a569-9349080857a8", "x": [2008, 2009, 2010, 2011, 2012, 2013, 2014, 2015, 2016, 2017, 2018], "y": ["", 25.33333333333333, 44.121951219512205, 8.019145802650955, 8.281469115191983, 8.959959671611696, 13.20936170212765, 18.30468377877745, 14.073172895641562, 27.414592862756194, 40.20285403468356], "type": "scatter"}, {"line": {"color": "rgba(132, 118, 202, 1.0)", "dash": "solid", "shape": "linear", "width": 1.3}, "mode": "lines", "name": "C++", "text": "", "uid": "cd1ee817-3322-4c73-bbfd-b0d37e55c12e", "x": [2008, 2009, 2010, 2011, 2012, 2013, 2014, 2015, 2016, 2017, 2018], "y": ["", 62.80000000000001, 24.62903225806452, 43.267080745341616, 13.26148409893993, 10.880452342487885, 11.800172265288547, 37.0938857333779, 13.037906137184114, 32.37483521312437, 49.15557778889443], "type": "scatter"}, {"line": {"color": "rgba(101, 101, 101, 1.0)", "dash": "solid", "shape": "linear", "width": 1.3}, "mode": "lines", "name": "CSS", "text": "", "uid": "2714f40b-3383-4589-bef6-c258a3723ab3", "x": [2008, 2009, 2010, 2011, 2012, 2013, 2014, 2015, 2016, 2017, 2018], "y": ["", "", 33.666666666666664, 28.235294117647058, 14.023255813953488, 24.553425357873213, 26.53426173216331, 26.233031674208174, 25.790665829862533, 78.19220926926377, 204.96501618345852], "type": "scatter"}, {"line": {"color": "rgba(251, 193, 94, 1.0)", "dash": "solid", "shape": "linear", "width": 1.3}, "mode": "lines", "name": "HTML", "text": "", "uid": "54b12469-08ba-452e-a720-f815ec97189b", "x": [2008, 2009, 2010, 2011, 2012, 2013, 2014, 2015, 2016, 2017, 2018], "y": [7.0, 31.0, 32.42857142857143, 14.26086956521739, 28.155172413793107, 17.44642857142857, 18.199312714776628, 21.961842433026103, 23.93699365622154, 65.27065941002029, 122.12608981380056], "type": "scatter"}, {"line": {"color": "rgba(142, 186, 66, 1.0)", "dash": "solid", "shape": "linear", "width": 1.3}, "mode": "lines", "name": "Java", "text": "", "uid": "cec1ca4b-a307-4b9b-89f7-66ba77036c74", "x": [2008, 2009, 2010, 2011, 2012, 2013, 2014, 2015, 2016, 2017, 2018], "y": [8.936170212765957, 15.295336787564766, 13.838466803559207, 12.721354166666671, 13.259647271631, 12.338942126311018, 12.19002251165783, 11.586373233853278, 12.238042230493624, 12.73621198700916, 19.329290715832947], "type": "scatter"}, {"line": {"color": "rgba(255, 181, 184, 1.0)", "dash": "solid", "shape": "linear", "width": 1.3}, "mode": "lines", "name": "JavaScript", "text": "", "uid": "3b35b957-5b31-435d-a082-934b1d09cff8", "x": [2008, 2009, 2010, 2011, 2012, 2013, 2014, 2015, 2016, 2017, 2018], "y": [26.43589743589743, 20.18232044198895, 25.023731587561368, 20.683942480527264, 14.366010292178798, 15.498098456037221, 17.26540389642128, 20.846668578199466, 25.431928459012767, 104.60217519947749, 235.39963316098232], "type": "scatter"}, {"line": {"color": "rgba(255, 181, 184, 0.8999999999999999)", "dash": "solid", "shape": "linear", "width": 1.3}, "mode": "lines", "name": "PHP", "text": "", "uid": "1f018c53-5658-432c-bde5-e847366c4980", "x": [2008, 2009, 2010, 2011, 2012, 2013, 2014, 2015, 2016, 2017, 2018], "y": [9.0, 41.066666666666656, 45.54838709677418, 69.9055944055944, 7.683805989928437, 9.020093062605751, 10.543117875524947, 18.203274402201192, 25.756365663322182, 53.90566678272982, 93.40880425754463], "type": "scatter"}, {"line": {"color": "rgba(226, 74, 51, 0.8999999999999999)", "dash": "solid", "shape": "linear", "width": 1.3}, "mode": "lines", "name": "Python", "text": "", "uid": "8da20389-93e8-480e-90a4-61d0e09b7a83", "x": [2008, 2009, 2010, 2011, 2012, 2013, 2014, 2015, 2016, 2017, 2018], "y": [9.722222222222225, 12.640000000000004, 10.009508716323298, 8.806269149186896, 6.465240641711232, 7.8741165340308195, 9.834022174112716, 11.688775773417484, 12.408582064659035, 16.515988962357447, 20.474196919135736], "type": "scatter"}, {"line": {"color": "rgba(62, 111, 176, 0.8999999999999999)", "dash": "solid", "shape": "linear", "width": 1.3}, "mode": "lines", "name": "Ruby", "text": "", "uid": "7623f1b0-d4cb-4b9b-acdc-c0d311933da7", "x": [2008, 2009, 2010, 2011, 2012, 2013, 2014, 2015, 2016, 2017, 2018], "y": [6.407874015748033, 6.074665515753126, 15.261073146038965, 27.17475035663337, 36.800276985059575, 42.40142491645142, 45.82266222996014, 52.625478240076525, 55.72049117017167, 60.73715089706924, 75.75723515355344], "type": "scatter"}], {"title": "Are the number of average number of dependencies per repository increasing over time?", "xaxis": {"automargin": true, "tickangle": -45, "title": "language"}, "yaxis": {"automargin": true, "title": "average_dependency_count"}}, {"showLink": true, "linkText": "Export to plot.ly", "plotlyServerURL": "https://plot.ly"})});</script><script type="text/javascript">window.addEventListener("resize", function(){window._Plotly.Plots.resize(document.getElementById("8e507a04-abf9-44c7-985d-ee4837510a0d"));});</script>


    Chart image: images/number_average_number_dependencies_per_repository_increasing_over_time.png
    

### What languages have the highest average number of dependencies per repository?
We group the repository_dependencies by repository id and language and then get the average number of rows after grouping by language. The repository_dependencies contains the transitive dependencies. Note: This approach ignores repositories that do not have any dependencies.


```python
query = """
SELECT
  language,
  AVG(count) AS average_dependency_count
FROM
    (SELECT
      repositories.language AS language,
      repositories.id,
      COUNT(*) AS count
    FROM
      `bigquery-public-data.libraries_io.repositories` AS repositories
    JOIN
      `bigquery-public-data.libraries_io.repository_dependencies` AS repository_dependencies
    ON 
        repositories.id = repository_dependencies.repository_id
    WHERE 
        language IN ({0})
    GROUP BY
        repositories.language,
        repositories.id) AS T1
GROUP BY
    language
ORDER BY
  average_dependency_count DESC
LIMIT
  10;
""".format(languages)

question = 'What languages have the highest average number of dependencies per repository?'
response = run_query(query, question)
generate_chart(response, 'language', 'average_dependency_count', question)
```

    Data: data/languages_highest_average_number_dependencies_per_repository.csv
    


<div id="f2a97e00-9e4a-419f-af5e-4009c671f246" style="height: 525px; width: 100%;" class="plotly-graph-div"></div><script type="text/javascript">require(["plotly"], function(Plotly) { window.PLOTLYENV=window.PLOTLYENV || {};window.PLOTLYENV.BASE_URL="https://plot.ly";Plotly.newPlot("f2a97e00-9e4a-419f-af5e-4009c671f246", [{"marker": {"color": "rgba(226, 74, 51, 0.6)", "line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "None", "orientation": "v", "text": ["66.40", "54.93", "50.15", "50.09", "37.42", "35.56", "27.61", "20.72", "13.43", "13.10"], "textposition": "outside", "uid": "96affa8d-1a2a-4a02-a50d-d383323c3024", "x": ["JavaScript", "CSS", "HTML", "Ruby", "PHP", "C", "C++", "C#", "Python", "Java"], "y": [66.3973757386726, 54.92597379149126, 50.15397030492733, 50.08822596884814, 37.41954883416525, 35.55582149474984, 27.610998387180718, 20.723415906422165, 13.426534283392312, 13.097393580123205], "type": "bar"}], {"title": "What languages have the highest average number of dependencies per repository?", "xaxis": {"automargin": true, "tickangle": -45, "title": "language"}, "yaxis": {"automargin": true, "title": "average_dependency_count"}}, {"showLink": true, "linkText": "Export to plot.ly", "plotlyServerURL": "https://plot.ly"})});</script><script type="text/javascript">window.addEventListener("resize", function(){window._Plotly.Plots.resize(document.getElementById("f2a97e00-9e4a-419f-af5e-4009c671f246"));});</script>


    Chart image: images/languages_highest_average_number_dependencies_per_repository.png
    

### What languages have the highest average number of total open issues for all dependencies per repository?

We group the repository_dependencies by repository id and language and then get the average number of open issues after grouping by language.


```python
query = """
SELECT 
    language,
    AVG(open_issues) AS tot_issues_repo
FROM
    (SELECT 
        repositories.language AS language,
        repositories.id AS id,
        SUM(projects_with_repository_fields.repository_open_issues_count) AS open_issues
    FROM
        `bigquery-public-data.libraries_io.repository_dependencies` AS repository_dependencies
    JOIN
        `bigquery-public-data.libraries_io.projects_with_repository_fields` AS projects_with_repository_fields
    ON 
        repository_dependencies.dependency_project_id = projects_with_repository_fields.id
    JOIN
        `bigquery-public-data.libraries_io.repositories` AS repositories
    ON 
        repositories.id = repository_dependencies.repository_id
    WHERE 
        repositories.language IN ({0})
    GROUP BY
        repositories.language,
        repositories.id) AS T1
GROUP BY
    language
ORDER BY
    tot_issues_repo DESC;
""".format(languages)

question = 'What languages have the highest average number of total open issues for all dependencies per repository?'
response = run_query(query, question)
generate_chart(response, 'language', 'tot_issues_repo', question)
```

    Data: data/languages_highest_average_number_total_open_issues_for_all_dependencies_per_repository.csv
    


<div id="24de2cd1-bc87-4dfc-882a-50588e90c6af" style="height: 525px; width: 100%;" class="plotly-graph-div"></div><script type="text/javascript">require(["plotly"], function(Plotly) { window.PLOTLYENV=window.PLOTLYENV || {};window.PLOTLYENV.BASE_URL="https://plot.ly";Plotly.newPlot("24de2cd1-bc87-4dfc-882a-50588e90c6af", [{"marker": {"color": "rgba(226, 74, 51, 0.6)", "line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "None", "orientation": "v", "text": ["7,405.60", "6,446.85", "5,302.76", "4,478.14", "3,413", "2,571.03", "2,509.02", "2,508.46", "1,726.14", "1,614.41"], "textposition": "outside", "uid": "b670ec42-b61d-465f-8d3a-547574de1e18", "x": ["Ruby", "JavaScript", "CSS", "HTML", "C++", "C", "C#", "PHP", "Java", "Python"], "y": [7405.602470368151, 6446.848302787453, 5302.763917728475, 4478.135219393515, 3413.001464128848, 2571.02736801242, 2509.017669788705, 2508.462031160925, 1726.136179273793, 1614.4055481774624], "type": "bar"}], {"title": "What languages have the highest average number of total open issues for all dependencies per repository?", "xaxis": {"automargin": true, "tickangle": -45, "title": "language"}, "yaxis": {"automargin": true, "title": "tot_issues_repo"}}, {"showLink": true, "linkText": "Export to plot.ly", "plotlyServerURL": "https://plot.ly"})});</script><script type="text/javascript">window.addEventListener("resize", function(){window._Plotly.Plots.resize(document.getElementById("24de2cd1-bc87-4dfc-882a-50588e90c6af"));});</script>


    Chart image: images/languages_highest_average_number_total_open_issues_for_all_dependencies_per_repository.png
    

### What languages have the highest proportion of repositories with at least one dependency with an unmaintained/deprecated status?
We find the number of repositories per language that have at least one dependency that has an unmaintained or deprecated status and divide by the total number of repositories per language.


```python
query = """
SELECT 
    T1.language,
    T1.count / T2.count AS prop_deprecated  
FROM
    (SELECT 
        language,
        COUNT(*) AS count
    FROM
        (SELECT 
            repositories.language AS language,
            repositories.id AS id
        FROM
            `bigquery-public-data.libraries_io.repository_dependencies` AS repository_dependencies
        JOIN
            `bigquery-public-data.libraries_io.projects` AS projects
        ON 
            repository_dependencies.dependency_project_id = projects.id
        JOIN
            `bigquery-public-data.libraries_io.repositories` AS repositories
        ON 
            repositories.id = repository_dependencies.repository_id
        WHERE 
            repositories.language IN ({0})
        AND
            projects.status IN ('Deprecated', 'Unmaintained')
        GROUP BY
            repositories.language,
            repositories.id) AS T1
    GROUP BY
        language) AS T1
JOIN 
    (SELECT
        language,
        COUNT(*) AS count
    FROM
        `bigquery-public-data.libraries_io.repositories`
    WHERE 
        language IN ({0})
    GROUP BY
        language) AS T2
ON
    T1.language = T2.language
ORDER BY
    prop_deprecated DESC;
""".format(languages)

question = 'What languages have the highest proportion of repositories with at least one <br> dependency with an unmaintained/deprecated status?'
response = run_query(query, question)
generate_chart(response, 'language', 'prop_deprecated', question)
```

    Data: data/languages_highest_proportion_repositories_at_least_one_dependency_unmaintaineddeprecated_status.csv
    


<div id="dd9f38a0-0da5-43f6-8f7d-bc97f79575ed" style="height: 525px; width: 100%;" class="plotly-graph-div"></div><script type="text/javascript">require(["plotly"], function(Plotly) { window.PLOTLYENV=window.PLOTLYENV || {};window.PLOTLYENV.BASE_URL="https://plot.ly";Plotly.newPlot("dd9f38a0-0da5-43f6-8f7d-bc97f79575ed", [{"marker": {"color": "rgba(226, 74, 51, 0.6)", "line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "None", "orientation": "v", "text": ["0.05", "0.05", "0.03", "0.02", "0.01", "0.01", "0", "0", "0", "0"], "textposition": "outside", "uid": "3a1d15f8-2634-44a6-bc38-95b5e82b990d", "x": ["Ruby", "JavaScript", "CSS", "PHP", "HTML", "Java", "Python", "C#", "C", "C++"], "y": [0.05228919993544317, 0.04985345126471788, 0.028984697512060027, 0.01722485758939941, 0.011868586860003676, 0.005411950052514879, 0.002385297290791248, 0.0012565728726626106, 0.0008003928308372207, 0.000670798455928953], "type": "bar"}], {"title": "What languages have the highest proportion of repositories with at least one <br> dependency with an unmaintained/deprecated status?", "xaxis": {"automargin": true, "tickangle": -45, "title": "language"}, "yaxis": {"automargin": true, "title": "prop_deprecated"}}, {"showLink": true, "linkText": "Export to plot.ly", "plotlyServerURL": "https://plot.ly"})});</script><script type="text/javascript">window.addEventListener("resize", function(){window._Plotly.Plots.resize(document.getElementById("dd9f38a0-0da5-43f6-8f7d-bc97f79575ed"));});</script>


    Chart image: images/languages_highest_proportion_repositories_at_least_one_dependency_unmaintaineddeprecated_status.png
    

### What languages have the highest proportion of repositories with at least one dependency with only one contributor?
We find the number of repositories per language that have at least one dependency that has exactly one contributor and divide by the total number of repositories per language.


```python
query = """
SELECT 
    T1.language,
    T1.count / T2.count AS prop_one_contrib   
FROM
    (SELECT 
        language,
        COUNT(*) AS count
    FROM
        (SELECT 
            repositories.language AS language,
            repositories.id AS id
        FROM
            `bigquery-public-data.libraries_io.repository_dependencies` AS repository_dependencies
        JOIN
            `bigquery-public-data.libraries_io.projects_with_repository_fields` AS projects_with_repository_fields
        ON 
            repository_dependencies.dependency_project_id = projects_with_repository_fields.id
        JOIN
            `bigquery-public-data.libraries_io.repositories` AS repositories
        ON 
            repositories.id = repository_dependencies.repository_id
        WHERE 
            repositories.language IN ({0})
        AND
            projects_with_repository_fields.repository_contributors_count = 1
        GROUP BY
            repositories.language,
            repositories.id) AS T1
    GROUP BY
        language) AS T1
JOIN 
    (SELECT
        language,
        COUNT(*) AS count
    FROM
        `bigquery-public-data.libraries_io.repositories`
    WHERE 
        language IN ({0})
    GROUP BY
        language) AS T2
ON
    T1.language = T2.language
ORDER BY
    prop_one_contrib DESC;
""".format(languages)


question = 'What languages have the highest proportion of repositories with at least <br> one dependency with only one contributor?'
response = run_query(query, question)
generate_chart(response, 'language', 'prop_one_contrib', question)
```

    Data: data/languages_highest_proportion_repositories_at_least_one_dependency_only_one_contributor.csv
    


<div id="6d090a3c-f80f-4534-a68c-6e9a1018e3a1" style="height: 525px; width: 100%;" class="plotly-graph-div"></div><script type="text/javascript">require(["plotly"], function(Plotly) { window.PLOTLYENV=window.PLOTLYENV || {};window.PLOTLYENV.BASE_URL="https://plot.ly";Plotly.newPlot("6d090a3c-f80f-4534-a68c-6e9a1018e3a1", [{"marker": {"color": "rgba(226, 74, 51, 0.6)", "line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "None", "orientation": "v", "text": ["0.34", "0.16", "0.08", "0.07", "0.05", "0.04", "0.04", "0.01", "0.01", "0"], "textposition": "outside", "uid": "2cccfdd2-a5ab-40d0-b2de-eb0a4ec3fc14", "x": ["Ruby", "JavaScript", "CSS", "PHP", "C#", "HTML", "Python", "Java", "C++", "C"], "y": [0.34113975820532005, 0.15792592683101914, 0.08040313947980937, 0.07180993841852318, 0.05444214859947402, 0.042637750056184184, 0.03952509702612041, 0.01001908874181018, 0.005415771582837436, 0.003564939847778051], "type": "bar"}], {"title": "What languages have the highest proportion of repositories with at least <br> one dependency with only one contributor?", "xaxis": {"automargin": true, "tickangle": -45, "title": "language"}, "yaxis": {"automargin": true, "title": "prop_one_contrib"}}, {"showLink": true, "linkText": "Export to plot.ly", "plotlyServerURL": "https://plot.ly"})});</script><script type="text/javascript">window.addEventListener("resize", function(){window._Plotly.Plots.resize(document.getElementById("6d090a3c-f80f-4534-a68c-6e9a1018e3a1"));});</script>


    Chart image: images/languages_highest_proportion_repositories_at_least_one_dependency_only_one_contributor.png
    

### What languages have the highest proportion of repositories with at least one dependency that has not been updated in the past 12 months?
We find the number of repositories per language that have at least one dependency that has not been pushed to in the past 12 months (8760 hours, since hours is the largest unit that TIMESTAMP_ADD takes as a parameter) and divide by the total number of repositories per language.


```python
query = """
SELECT 
    T1.language,
    T1.count / T2.count AS prop_not_updated_12
FROM
    (SELECT 
        language,
        COUNT(*) AS count
    FROM
        (SELECT 
            repositories.language AS language,
            repositories.id AS id
        FROM
            `bigquery-public-data.libraries_io.repository_dependencies` AS repository_dependencies
        JOIN
            `bigquery-public-data.libraries_io.projects_with_repository_fields` AS projects_with_repository_fields
        ON 
            repository_dependencies.dependency_project_id = projects_with_repository_fields.id
        JOIN
            `bigquery-public-data.libraries_io.repositories` AS repositories
        ON 
            repositories.id = repository_dependencies.repository_id
        WHERE 
            repositories.language IN ({0})
        AND
            projects_with_repository_fields.repository_last_pushed_timestamp <= TIMESTAMP_ADD(CURRENT_TIMESTAMP(), INTERVAL -8760 HOUR)
        GROUP BY
            repositories.language,
            repositories.id) AS T1
    GROUP BY
        language) AS T1
JOIN 
    (SELECT
        language,
        COUNT(*) AS count
    FROM
        `bigquery-public-data.libraries_io.repositories`
    WHERE 
        language IN ({0})
    GROUP BY
        language) AS T2
ON
    T1.language = T2.language
ORDER BY
    prop_not_updated_12 DESC;
""".format(languages)

question = 'What languages have the highest proportion of repositories with at least <br> one dependency that has not been updated in the past 12 months?'
response = run_query(query, question)
generate_chart(response, 'language', 'prop_not_updated_12', question)
```

    Data: data/languages_highest_proportion_repositories_at_least_one_dependency_not_been_updated_past_12_months.csv
    


<div id="e77c1f4e-69d4-426c-a64d-bcce988c6be6" style="height: 525px; width: 100%;" class="plotly-graph-div"></div><script type="text/javascript">require(["plotly"], function(Plotly) { window.PLOTLYENV=window.PLOTLYENV || {};window.PLOTLYENV.BASE_URL="https://plot.ly";Plotly.newPlot("e77c1f4e-69d4-426c-a64d-bcce988c6be6", [{"marker": {"color": "rgba(226, 74, 51, 0.6)", "line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "None", "orientation": "v", "text": ["0.54", "0.37", "0.22", "0.21", "0.11", "0.11", "0.09", "0.07", "0.01", "0.01"], "textposition": "outside", "uid": "cc80ab2c-553e-4d3e-89b0-21d1000b4f88", "x": ["Ruby", "JavaScript", "CSS", "PHP", "C#", "HTML", "Python", "Java", "C++", "C"], "y": [0.5439215707850991, 0.3697646819838987, 0.21902963611036685, 0.2062988634635504, 0.1105192433157953, 0.10596884578303904, 0.09209567016823712, 0.06978185485887667, 0.012398111338825924, 0.010310172681888862], "type": "bar"}], {"title": "What languages have the highest proportion of repositories with at least <br> one dependency that has not been updated in the past 12 months?", "xaxis": {"automargin": true, "tickangle": -45, "title": "language"}, "yaxis": {"automargin": true, "title": "prop_not_updated_12"}}, {"showLink": true, "linkText": "Export to plot.ly", "plotlyServerURL": "https://plot.ly"})});</script><script type="text/javascript">window.addEventListener("resize", function(){window._Plotly.Plots.resize(document.getElementById("e77c1f4e-69d4-426c-a64d-bcce988c6be6"));});</script>


    Chart image: images/languages_highest_proportion_repositories_at_least_one_dependency_not_been_updated_past_12_months.png
    

### What languages have the highest proportion of repositories with at least one dependency with a missing or non-compliant license?
We find the number of repositories per language that have at least one dependency that has a missing license and divide by the total number of repositories per language. Note: Licenses which are non standard will be classified as non-compliant.


```python
query = """
SELECT 
    T1.language,
    T1.count / T2.count AS prop_no_license   
FROM
    (SELECT 
        language,
        COUNT(*) AS count
    FROM
        (SELECT 
            repositories.language AS language,
            repositories.id AS id
        FROM
            `bigquery-public-data.libraries_io.repository_dependencies` AS repository_dependencies
        JOIN
            `bigquery-public-data.libraries_io.projects` AS projects
        ON 
            repository_dependencies.dependency_project_id = projects.id
        JOIN
            `bigquery-public-data.libraries_io.repositories` AS repositories
        ON 
            repositories.id = repository_dependencies.repository_id
        WHERE 
            repositories.language IN ({0})
        AND
            projects.licenses IS NULL
        GROUP BY
            repositories.language,
            repositories.id) AS T1
    GROUP BY
        language) AS T1
JOIN 
    (SELECT
        language,
        COUNT(*) AS count
    FROM
        `bigquery-public-data.libraries_io.repositories`
    WHERE 
        language IN ({0})
    GROUP BY
        language) AS T2
ON
    T1.language = T2.language
ORDER BY
    prop_no_license DESC;
""".format(languages)

question = 'What languages have the highest proportion of repositories with at least one <br> dependency with a missing or non-compliant license?'
response = run_query(query, question)
generate_chart(response, 'language', 'prop_no_license', question)
```

    Data: data/languages_highest_proportion_repositories_at_least_one_dependency_missing_or_non-compliant_license.csv
    


<div id="d35f2904-1a12-432d-9f8d-ea9e5ce2fac1" style="height: 525px; width: 100%;" class="plotly-graph-div"></div><script type="text/javascript">require(["plotly"], function(Plotly) { window.PLOTLYENV=window.PLOTLYENV || {};window.PLOTLYENV.BASE_URL="https://plot.ly";Plotly.newPlot("d35f2904-1a12-432d-9f8d-ea9e5ce2fac1", [{"marker": {"color": "rgba(226, 74, 51, 0.6)", "line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "None", "orientation": "v", "text": ["0.17", "0.08", "0.07", "0.04", "0.03", "0.02", "0.01", "0.01", "0", "0"], "textposition": "outside", "uid": "d7e852ff-d140-4e57-85e6-70d0980eb502", "x": ["C#", "Java", "Ruby", "JavaScript", "CSS", "HTML", "PHP", "Python", "C++", "C"], "y": [0.17351760992676998, 0.07558433222746445, 0.06702264624323254, 0.044435653726492254, 0.026594913900490017, 0.017028746532869024, 0.014900665285160163, 0.012206704033189795, 0.004471989706193021, 0.0028578443407807517], "type": "bar"}], {"title": "What languages have the highest proportion of repositories with at least one <br> dependency with a missing or non-compliant license?", "xaxis": {"automargin": true, "tickangle": -45, "title": "language"}, "yaxis": {"automargin": true, "title": "prop_no_license"}}, {"showLink": true, "linkText": "Export to plot.ly", "plotlyServerURL": "https://plot.ly"})});</script><script type="text/javascript">window.addEventListener("resize", function(){window._Plotly.Plots.resize(document.getElementById("d35f2904-1a12-432d-9f8d-ea9e5ce2fac1"));});</script>


    Chart image: images/languages_highest_proportion_repositories_at_least_one_dependency_missing_or_non-compliant_license.png
    

### What languages have the highest proportion of repositories with at least one dependency with a missing README
We find the number of repositories per language that have at least one dependency that has a missing README and divide by the total number of repositories per language.


```python
query = """
SELECT 
    T1.language,
    T1.count / T2.count AS prop_no_readme
FROM
    (SELECT 
        language,
        COUNT(*) AS count
    FROM
        (SELECT 
            repositories.language AS language,
            repositories.id AS id
        FROM
            `bigquery-public-data.libraries_io.repository_dependencies` AS repository_dependencies
        JOIN
            `bigquery-public-data.libraries_io.projects_with_repository_fields` AS projects_with_repository_fields
        ON 
            repository_dependencies.dependency_project_id = projects_with_repository_fields.id
        JOIN
            `bigquery-public-data.libraries_io.repositories` AS repositories
        ON 
            repositories.id = repository_dependencies.repository_id
        WHERE 
            repositories.language IN ({0})
        AND
            projects_with_repository_fields.repository_readme_filename = ''
        GROUP BY
            repositories.language,
            repositories.id) AS T1
    GROUP BY
        language) AS T1
JOIN 
    (SELECT
        language,
        COUNT(*) AS count
    FROM
        `bigquery-public-data.libraries_io.repositories`
    WHERE 
        language IN ({0})
    GROUP BY
        language) AS T2
ON
    T1.language = T2.language
ORDER BY
    prop_no_readme DESC;
""".format(languages)

question = 'What languages have the highest proportion of repositories with at <br> least one dependency with a missing README?'
response = run_query(query, question)
generate_chart(response, 'language', 'prop_no_readme', question)
```

    Data: data/languages_highest_proportion_repositories_at_least_one_dependency_missing_readme.csv
    


<div id="db352457-c7a8-45aa-9755-9b3cb02dbcf0" style="height: 525px; width: 100%;" class="plotly-graph-div"></div><script type="text/javascript">require(["plotly"], function(Plotly) { window.PLOTLYENV=window.PLOTLYENV || {};window.PLOTLYENV.BASE_URL="https://plot.ly";Plotly.newPlot("db352457-c7a8-45aa-9755-9b3cb02dbcf0", [{"marker": {"color": "rgba(226, 74, 51, 0.6)", "line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "None", "orientation": "v", "text": ["0.18", "0.15", "0.13", "0.13", "0.12", "0.07", "0.07", "0.05", "0.01", "0.01"], "textposition": "outside", "uid": "b8dbe193-719e-4e06-9c97-bf19383e467b", "x": ["C#", "Java", "Ruby", "Python", "JavaScript", "PHP", "CSS", "HTML", "C++", "C"], "y": [0.176493211859432, 0.14893719887301402, 0.13164641929662396, 0.13163581243351102, 0.11546085338864358, 0.07457111918516768, 0.07390297884876429, 0.045519796961003424, 0.0120359624791833, 0.008174155004501188], "type": "bar"}], {"title": "What languages have the highest proportion of repositories with at <br> least one dependency with a missing README?", "xaxis": {"automargin": true, "tickangle": -45, "title": "language"}, "yaxis": {"automargin": true, "title": "prop_no_readme"}}, {"showLink": true, "linkText": "Export to plot.ly", "plotlyServerURL": "https://plot.ly"})});</script><script type="text/javascript">window.addEventListener("resize", function(){window._Plotly.Plots.resize(document.getElementById("db352457-c7a8-45aa-9755-9b3cb02dbcf0"));});</script>


    Chart image: images/languages_highest_proportion_repositories_at_least_one_dependency_missing_readme.png
    

A parallel coordinates plot is a multi-dimensional feature visualization where the vertical axis is duplicated horizontally for each feature. Instances are displayed as a single line segment drawn from each vertical axes to the location representing their value for that feature. This allows many dimensions to be visualized at once. Its strength is that the values for each feature can be completely different: different ranges and even different units. This will allow us to plot the features for all languages on the same visualization and get an idea of the overall risk rank of each language<sup>[\[6\]](http://www.scikit-yb.org/en/latest/api/features/pcoords.html)</sup>. The following normalised_coordinates function will normalize the values before plotting them, so that they scaled equally.


```python
# Source: https://python.g-node.org/python-summerschool-2013/_media/wiki/datavis/olympics_vis.py
# License: normalised_coordinates by Bartosz Telenczuk is licensed under CC SA 3.0 Unported

def normalised_coordinates(coordinates, values, labels, title=None):
    """Plot 2d array `values` using K parallel coordinates.
    
    Arguments:

        coordinates -- list or array of K elements containg coordinate
            names,
        values -- (K,N)-shaped array of N data points with K
            coordinates, 
        labels -- list or array of one string per data point
            describing its class membership (category)
    """
    
    K = len(coordinates)
    
    # normalise data
    vmin, vmax = values.min(1), values.max(1)
    normed_values = (values - vmin[:, None])*1. / (vmax[:, None] -
                                                   vmin[:, None])

    # create independent, attached axes
    fig, axes = plt.subplots(1, K-1)
    fig.subplots_adjust(wspace=0)
    
    dpi = fig.get_dpi()
    fig.set_size_inches(1280.0/float(dpi),720.0/float(dpi))

    fig.suptitle(title, fontsize=16)
    fig.subplots_adjust(top=.95)
    
    # find names and number of different classes
    ulabels = np.unique(labels)
    n_labels = len(ulabels)
    
    # obtain colors
    cmap = plt.get_cmap('tab10')
    colors = cmap(np.linspace(0, 1, n_labels))

    # change the label strings to indices into class names array
    class_id = np.searchsorted(ulabels, labels) 
    
    for i in range(K-1):
        ax = axes[i]

        lines = ax.plot(normed_values[:,:], 'k', linewidth=7.0)
 
        # set line colors
        [ l.set_color(colors[c]) for c,l in zip(class_id, lines) ]
    
        # configure axes
        ax.spines['top'].set_visible(False)
        ax.spines['bottom'].set_position(('outward', 5))
        ax.spines['bottom'].set_visible(False)
        ax.yaxis.set_ticks_position('left')
        ax.xaxis.set_ticks_position('none')

        # set limit to show only single line segment
        ax.set_xlim((i, i+1))
        ax.set_xticks([i])
        ax.set_xticklabels([coordinates[i]], fontsize = 9)

        # set the scale
        ax.set_yticks([0, 1])
        ax.set_yticklabels([format_value(vmin[i]), format_value(vmax[i])])

    # we have to deal with rightmost axis separately
    axes[-1].set_xticks([K-2, K-1])
    axes[-1].set_xticklabels(coordinates[-2:])
    
    ax = axes[-1].twinx()
    ax.tick_params(axis='y', direction='out')
    ax.xaxis.set_ticks_position('none')
    ax.set_yticks([0, 1])
    ax.set_yticklabels([format_value(vmin[-1]), format_value(vmax[-1])])
    
    # add legend in figure coordinates
    leg_handlers = [ lines[np.where(class_id==id)[0][0]] 
                    for id in range(n_labels)]
    plt.legend(leg_handlers, ulabels, frameon=False, loc='upper left',
               ncol=len(labels),
               bbox_to_anchor=(0.1, 0.06, 0.9, 0.02),
               bbox_transform=fig.transFigure)
    
    # set leftmost axis as the default for labelling
    plt.sca(axes[0])
    fig.savefig('images/{0}.png'.format(get_filename(title)))
```

We join the results of all the prior queries into a single DataFrame and plot them all with the normalised_coordinates to get a visual of the overal risk ranking of each language.


```python
query_ids = ['languages_highest_average_number_dependencies_per_repository',
'languages_highest_average_number_total_open_issues_for_all_dependencies_per_repository',
'languages_highest_proportion_repositories_at_least_one_dependency_unmaintaineddeprecated_status',
'languages_highest_proportion_repositories_at_least_one_dependency_only_one_contributor',
'languages_highest_proportion_repositories_at_least_one_dependency_not_been_updated_past_12_months',
'languages_highest_proportion_repositories_at_least_one_dependency_missing_or_non-compliant_license',
'languages_highest_proportion_repositories_at_least_one_dependency_missing_readme']

data = None
for query_id in query_ids:
    data2 = pd.read_csv('data/{0}.csv'.format(query_id))
    if data is None:
        data = data2
    else:
        data = pd.merge(data, data2, on='language')
        
normalised_coordinates(data.drop('language', axis=1).columns.values, np.array(data.drop('language', axis=1)).T, data['language'].values, title="Risk factor comparison for languages")

```


![png](hcds-a4-final-project-plan_files/hcds-a4-final-project-plan_42_0.png)


From this plot, we can see that Ruby, JavaScript, and CSS rank highest for most of the risk factors, while C++, Python, and Java rank among the lowest.

## Identifying most used projects that exhibit risk
We will now analyze the data to see if we can find packages in each ecosystem that currently pose a risk to the largest number of repositories.

### What are the most used projects that have an unmaintained/deprecated status per language?
We find the top projects that have an unmaintained or deprecated status per language using the ARRAY_AGG function and ordering by the descending dependent_repositories_count.


```python
query = """
SELECT
    language, 
    ARRAY_AGG(STRUCT(name, dependent_repositories_count, repository_stars_count) ORDER BY dependent_repositories_count DESC LIMIT 3) projects
FROM
    `bigquery-public-data.libraries_io.projects_with_repository_fields`
WHERE 
    language IN ({0})
AND
    status IN ('Deprecated', 'Unmaintained')
GROUP BY 
    language
ORDER BY
    language;
""".format(languages)

question = 'What are the most used projects that have an unmaintained/deprecated status per language?'
response = run_query(query, question)
generate_chart(response, 'name', 'dependent_repositories_count', question, grouped_by_column='language', list_column='projects')

```

    Data: data/most_used_projects_unmaintaineddeprecated_status_per_language.csv
    


<div id="1f24712f-cd23-4169-8525-ad7973ec7155" style="height: 525px; width: 100%;" class="plotly-graph-div"></div><script type="text/javascript">require(["plotly"], function(Plotly) { window.PLOTLYENV=window.PLOTLYENV || {};window.PLOTLYENV.BASE_URL="https://plot.ly";Plotly.newPlot("1f24712f-cd23-4169-8525-ad7973ec7155", [{"marker": {"line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "C", "orientation": "v", "text": ["11", "3", "2"], "textposition": "outside", "uid": "18535352-42ea-463f-bc94-31d255189174", "x": ["node-core-audio", "gccxml_gem", "node-retro"], "y": [11.0, 3.0, 2.0], "type": "bar"}, {"marker": {"line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "C#", "orientation": "v", "text": ["44", "17", "10"], "textposition": "outside", "uid": "5efdc7aa-c689-4506-8b1e-7e70b425fefe", "x": ["RedditSharp", "GithubSharp", "GithubSharp.Core"], "y": [44.0, 17.0, 10.0], "type": "bar"}, {"marker": {"line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "C++", "orientation": "v", "text": ["232", "184", "23"], "textposition": "outside", "uid": "e482160d-c7ff-4133-baca-c5c181e2c119", "x": ["zlib", "mapbox-android-sdk", "appjs"], "y": [232.0, 184.0, 23.0], "type": "bar"}, {"marker": {"line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "CSS", "orientation": "v", "text": ["99", "44", "33"], "textposition": "outside", "uid": "09e3cf33-1b68-4067-8793-90c93884f2a1", "x": ["grunt-combine-mq", "polymer-ui-elements", "jquery-ui-bootstrap"], "y": [99.0, 44.0, 33.0], "type": "bar"}, {"marker": {"line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "HTML", "orientation": "v", "text": ["60", "34", "23"], "textposition": "outside", "uid": "3161a349-5e56-4653-9d66-e460c9b6d55b", "x": ["blueimp-bootstrap-image-gallery", "angular-bootstrap-select", "jquery-infinite-scroll"], "y": [60.0, 34.0, 23.0], "type": "bar"}, {"marker": {"line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "Java", "orientation": "v", "text": ["4,990", "1,748", "1,197"], "textposition": "outside", "uid": "705c3128-fd57-4a48-8c80-221f7e8faac8", "x": ["library", "bonecp", "floatingactionbutton"], "y": [4990.0, 1748.0, 1197.0], "type": "bar"}, {"marker": {"line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "JavaScript", "orientation": "v", "text": ["98,835", "34,875", "18,562"], "textposition": "outside", "uid": "e6f108bd-df08-4848-a925-e8ea78aaf1b3", "x": ["jade", "grunt-usemin", "jscs"], "y": [98835.0, 34875.0, 18562.0], "type": "bar"}, {"marker": {"line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "PHP", "orientation": "v", "text": ["8,994", "2,826", "1,176"], "textposition": "outside", "uid": "edb49888-5437-47ff-bea6-77a3eeedf95b", "x": ["guzzle", "icu", "sentry"], "y": [8994.0, 2826.0, 1176.0], "type": "bar"}, {"marker": {"line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "Python", "orientation": "v", "text": ["1,541", "320", "222"], "textposition": "outside", "uid": "51b3bec3-8244-46a5-88ac-c1347d67bece", "x": ["cssmin", "nodejs", "trollius"], "y": [1541.0, 320.0, 222.0], "type": "bar"}, {"marker": {"line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "Ruby", "orientation": "v", "text": ["40,017", "10,373", "5,207"], "textposition": "outside", "uid": "c2e518b3-91d1-4b9d-b1fa-3d18ba763eb6", "x": ["atomic", "colored", "ruby-hmac"], "y": [40017.0, 10373.0, 5207.0], "type": "bar"}], {"title": "What are the most used projects that have an unmaintained/deprecated status per language?", "xaxis": {"automargin": true, "tickangle": -45, "title": "name"}, "yaxis": {"automargin": true, "title": "dependent_repositories_count"}}, {"showLink": true, "linkText": "Export to plot.ly", "plotlyServerURL": "https://plot.ly"})});</script><script type="text/javascript">window.addEventListener("resize", function(){window._Plotly.Plots.resize(document.getElementById("1f24712f-cd23-4169-8525-ad7973ec7155"));});</script>


    Chart image: images/most_used_projects_unmaintaineddeprecated_status_per_language.png
    

### What are the most used projects that have only one contributor per language?
We find the top projects that have only one contributor per language using the ARRAY_AGG function and ordering by the descending dependent_repositories_count.


```python
query = """
SELECT
    language, 
    ARRAY_AGG(STRUCT(name, dependent_repositories_count, repository_stars_count) ORDER BY dependent_repositories_count DESC LIMIT 3) projects
FROM
    `bigquery-public-data.libraries_io.projects_with_repository_fields`
WHERE 
    language IN ({0})
AND
    repository_contributors_count = 1
GROUP BY 
    language
ORDER BY
    language;
""".format(languages)

question = 'What are the most used projects that have only one contributor per language?'
response = run_query(query, question)
generate_chart(response, 'name', 'dependent_repositories_count', question, grouped_by_column='language', list_column='projects')
```

    Data: data/most_used_projects_only_one_contributor_per_language.csv
    


<div id="2aa23145-0bae-4ed5-9485-172ca20d61ab" style="height: 525px; width: 100%;" class="plotly-graph-div"></div><script type="text/javascript">require(["plotly"], function(Plotly) { window.PLOTLYENV=window.PLOTLYENV || {};window.PLOTLYENV.BASE_URL="https://plot.ly";Plotly.newPlot("2aa23145-0bae-4ed5-9485-172ca20d61ab", [{"marker": {"line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "C", "orientation": "v", "text": ["986", "630", "465"], "textposition": "outside", "uid": "be9fcc76-9a6c-42ee-ae8e-aa2395ea97de", "x": ["libressl-pnacl-sys", "unicode", "boost"], "y": [986.0, 630.0, 465.0], "type": "bar"}, {"marker": {"line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "C#", "orientation": "v", "text": ["28,174", "9,619", "720"], "textposition": "outside", "uid": "5d5ce938-c0dd-4dc0-ad71-dd76af12376d", "x": ["Owin", "log4net", "MahApps.Metro.Resources"], "y": [28174.0, 9619.0, 720.0], "type": "bar"}, {"marker": {"line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "C++", "orientation": "v", "text": ["232", "187", "108"], "textposition": "outside", "uid": "f6c8e4a6-2878-4f1e-b9da-e39e15662bec", "x": ["zlib", "windows-mutex", "epoll"], "y": [232.0, 187.0, 108.0], "type": "bar"}, {"marker": {"line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "CSS", "orientation": "v", "text": ["1,010", "647", "641"], "textposition": "outside", "uid": "bf8a132a-6cb2-4637-9bfd-bd775d2cc45a", "x": ["colorbrewer", "grunt-combine-media-queries", "datatables.net-bs"], "y": [1010.0, 647.0, 641.0], "type": "bar"}, {"marker": {"line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "HTML", "orientation": "v", "text": ["1,418", "764", "668"], "textposition": "outside", "uid": "249f09c6-4035-42e2-9d70-86e72d61327d", "x": ["app-route", "paper-card", "octopress-date-format"], "y": [1418.0, 764.0, 668.0], "type": "bar"}, {"marker": {"line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "Java", "orientation": "v", "text": ["21,132", "2,424", "1,161"], "textposition": "outside", "uid": "09bfe512-5acc-428c-b244-c01fa9ac7366", "x": ["bindex", "cache-api", "jsf-api"], "y": [21132.0, 2424.0, 1161.0], "type": "bar"}, {"marker": {"line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "JavaScript", "orientation": "v", "text": ["235,444", "227,865", "180,289"], "textposition": "outside", "uid": "7329851d-6477-4696-b3a6-5c7aaaeadb1b", "x": ["concat-map", "asynckit", "depd"], "y": [235444.0, 227865.0, 180289.0], "type": "bar"}, {"marker": {"line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "PHP", "orientation": "v", "text": ["8,691", "8,078", "1,918"], "textposition": "outside", "uid": "d6237997-4af1-4a95-bef1-1e5009f1f678", "x": ["resource-operations", "code-unit-reverse-lookup", "link"], "y": [8691.0, 8078.0, 1918.0], "type": "bar"}, {"marker": {"line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "Python", "orientation": "v", "text": ["37,771", "23,370", "3,560"], "textposition": "outside", "uid": "f264c2d8-e653-483a-b4a1-68d9c6d5380f", "x": ["PyYAML", "argparse", "appnope"], "y": [37771.0, 23370.0, 3560.0], "type": "bar"}, {"marker": {"line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "Ruby", "orientation": "v", "text": ["380,637", "16,771", "11,708"], "textposition": "outside", "uid": "6cd748d1-588a-4b25-be84-00bf590df17d", "x": ["erubis", "sexp_processor", "guard-compat"], "y": [380637.0, 16771.0, 11708.0], "type": "bar"}], {"title": "What are the most used projects that have only one contributor per language?", "xaxis": {"automargin": true, "tickangle": -45, "title": "name"}, "yaxis": {"automargin": true, "title": "dependent_repositories_count"}}, {"showLink": true, "linkText": "Export to plot.ly", "plotlyServerURL": "https://plot.ly"})});</script><script type="text/javascript">window.addEventListener("resize", function(){window._Plotly.Plots.resize(document.getElementById("2aa23145-0bae-4ed5-9485-172ca20d61ab"));});</script>


    Chart image: images/most_used_projects_only_one_contributor_per_language.png
    

### What are the most used projects that have not been updated in the past 12 months  per language?
We find the top projects that have not been updated in the past 12 months (8760 hours, since hours is the largest unit that TIMESTAMP_ADD takes as a parameter) per language using the ARRAY_AGG function and ordering by the descending dependent_repositories_count.


```python
query = """
SELECT
    language, 
    ARRAY_AGG(STRUCT(name, dependent_repositories_count, repository_stars_count) ORDER BY dependent_repositories_count DESC LIMIT 3) projects
FROM
  `bigquery-public-data.libraries_io.projects_with_repository_fields`
WHERE 
    language IN ({0})
AND
    repository_last_pushed_timestamp <= TIMESTAMP_ADD(CURRENT_TIMESTAMP(), INTERVAL -8760 HOUR)
GROUP BY 
    language
ORDER BY
    language;
""".format(languages)

question = 'What are the most used projects that have not been updated in the past 12 months per language?'
response = run_query(query, question)
generate_chart(response, 'name', 'dependent_repositories_count', question, grouped_by_column='language', list_column='projects')
```

    Data: data/most_used_projects_not_been_updated_past_12_months_per_language.csv
    


<div id="0d77c3db-308b-440f-9a95-53879a106104" style="height: 525px; width: 100%;" class="plotly-graph-div"></div><script type="text/javascript">require(["plotly"], function(Plotly) { window.PLOTLYENV=window.PLOTLYENV || {};window.PLOTLYENV.BASE_URL="https://plot.ly";Plotly.newPlot("0d77c3db-308b-440f-9a95-53879a106104", [{"marker": {"line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "C", "orientation": "v", "text": ["18,594", "12,885", "10,740"], "textposition": "outside", "uid": "b4e7b4f2-96b4-4fdd-9b1d-7566b97ae25c", "x": ["rdiscount", "debugger", "debugger-ruby_core_source"], "y": [18594.0, 12885.0, 10740.0], "type": "bar"}, {"marker": {"line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "C#", "orientation": "v", "text": ["46,695", "28,174", "9,619"], "textposition": "outside", "uid": "834aa6c3-9f9c-4814-b64b-c5ec96277e9b", "x": ["Antlr", "Owin", "log4net"], "y": [46695.0, 28174.0, 9619.0], "type": "bar"}, {"marker": {"line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "C++", "orientation": "v", "text": ["46,461", "3,177", "2,142"], "textposition": "outside", "uid": "0724a02b-b42a-42d8-8ced-b51ebc4fcb71", "x": ["therubyracer", "kerberos", "weak"], "y": [46461.0, 3177.0, 2142.0], "type": "bar"}, {"marker": {"line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "CSS", "orientation": "v", "text": ["27,176", "25,540", "11,647"], "textposition": "outside", "uid": "675c25ce-f453-4c46-b70f-a2287fd8214c", "x": ["compass", "css", "compass-import-once"], "y": [27176.0, 25540.0, 11647.0], "type": "bar"}, {"marker": {"line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "HTML", "orientation": "v", "text": ["84,597", "51,010", "12,116"], "textposition": "outside", "uid": "c8122340-7e3b-4d7d-af16-1c7607031e94", "x": ["cssesc", "is-plain-object", "fssm"], "y": [84597.0, 51010.0, 12116.0], "type": "bar"}, {"marker": {"line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "Java", "orientation": "v", "text": ["336,322", "21,132", "19,747"], "textposition": "outside", "uid": "dea323bb-3036-4604-989f-2aeede2ce39e", "x": ["thread_safe", "bindex", "commons-collections"], "y": [336322.0, 21132.0, 19747.0], "type": "bar"}, {"marker": {"line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "JavaScript", "orientation": "v", "text": ["296,978", "260,969", "249,079"], "textposition": "outside", "uid": "cf444a4f-8859-4e32-8f29-fae009049801", "x": ["ansi-regex", "balanced-match", "arr-diff"], "y": [296978.0, 260969.0, 249079.0], "type": "bar"}, {"marker": {"line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "PHP", "orientation": "v", "text": ["67,366", "59,766", "54,031"], "textposition": "outside", "uid": "6e2c64a8-3c76-41f1-a036-317a54f12bd0", "x": ["finder", "css-selector", "random_compat"], "y": [67366.0, 59766.0, 54031.0], "type": "bar"}, {"marker": {"line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "Python", "orientation": "v", "text": ["47,304", "33,120", "27,615"], "textposition": "outside", "uid": "deec580f-d1a1-4e0a-bc1c-76f122c7032e", "x": ["MarkupSafe", "itsdangerous", "crc"], "y": [47304.0, 33120.0, 27615.0], "type": "bar"}, {"marker": {"line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "Ruby", "orientation": "v", "text": ["418,087", "406,753", "382,725"], "textposition": "outside", "uid": "6cb99e29-e9bf-4c23-bed4-7a1d10dc28b2", "x": ["builder", "mime-types", "sass-rails"], "y": [418087.0, 406753.0, 382725.0], "type": "bar"}], {"title": "What are the most used projects that have not been updated in the past 12 months per language?", "xaxis": {"automargin": true, "tickangle": -45, "title": "name"}, "yaxis": {"automargin": true, "title": "dependent_repositories_count"}}, {"showLink": true, "linkText": "Export to plot.ly", "plotlyServerURL": "https://plot.ly"})});</script><script type="text/javascript">window.addEventListener("resize", function(){window._Plotly.Plots.resize(document.getElementById("0d77c3db-308b-440f-9a95-53879a106104"));});</script>


    Chart image: images/most_used_projects_not_been_updated_past_12_months_per_language.png
    

### What are the most used projects that have a missing or non-compliant licenses per language?
We find the top projects that have a missing license per language using the ARRAY_AGG function and ordering by the descending dependent_repositories_count.


```python
query = """
SELECT
    language, 
    ARRAY_AGG(STRUCT(name, dependent_repositories_count, repository_stars_count) ORDER BY dependent_repositories_count DESC LIMIT 3) projects
FROM
    `bigquery-public-data.libraries_io.projects_with_repository_fields`
WHERE 
    language IN ({0})
AND
    licenses = ''
GROUP BY 
    language
ORDER BY
    language;
""".format(languages)

question = 'What are the most used projects that have a missing or non-compliant licenses per language?'
response = run_query(query, question)
generate_chart(response, 'name', 'dependent_repositories_count', question, grouped_by_column='language', list_column='projects')
```

    Data: data/most_used_projects_missing_or_non-compliant_licenses_per_language.csv
    


<div id="e444fb5a-0b25-4e39-beb2-4211cee04490" style="height: 525px; width: 100%;" class="plotly-graph-div"></div><script type="text/javascript">require(["plotly"], function(Plotly) { window.PLOTLYENV=window.PLOTLYENV || {};window.PLOTLYENV.BASE_URL="https://plot.ly";Plotly.newPlot("e444fb5a-0b25-4e39-beb2-4211cee04490", [{"marker": {"line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "C", "orientation": "v", "text": ["207", "123", "107"], "textposition": "outside", "uid": "a323171e-cb43-40bd-bb93-9d597f8ae085", "x": ["multi-hashing", "ethash", "zlib"], "y": [207.0, 123.0, 107.0], "type": "bar"}, {"marker": {"line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "C#", "orientation": "v", "text": ["793", "665", "394"], "textposition": "outside", "uid": "a5219dde-719e-4eea-8b30-470f78cec5d0", "x": ["ClientDependency", "Examine", "ClientDependency-Mvc"], "y": [793.0, 665.0, 394.0], "type": "bar"}, {"marker": {"line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "C++", "orientation": "v", "text": ["362", "113", "25"], "textposition": "outside", "uid": "420f2119-6b55-4502-8aac-454670828dc2", "x": ["execsync", "segfault-handler", "discount"], "y": [362.0, 113.0, 25.0], "type": "bar"}, {"marker": {"line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "CSS", "orientation": "v", "text": ["826", "444", "348"], "textposition": "outside", "uid": "1a8b80c1-2268-4f52-9d8c-3695b3a472f3", "x": ["anjlab-bootstrap-rails", "sassy-math", "compass-normalize"], "y": [826.0, 444.0, 348.0], "type": "bar"}, {"marker": {"line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "HTML", "orientation": "v", "text": ["1,418", "764", "327"], "textposition": "outside", "uid": "41747b69-4e1f-4106-8f29-cc6deaff192a", "x": ["app-route", "paper-card", "foundation-icon-fonts"], "y": [1418.0, 764.0, 327.0], "type": "bar"}, {"marker": {"line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "Java", "orientation": "v", "text": ["127", "86", "60"], "textposition": "outside", "uid": "27c148c0-70f9-457f-9e3e-a726c38a0e1b", "x": ["bottomsheet", "barchart-udt-bundle", "jgeoip"], "y": [127.0, 86.0, 60.0], "type": "bar"}, {"marker": {"line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "JavaScript", "orientation": "v", "text": ["2,137", "1,445", "1,374"], "textposition": "outside", "uid": "ab5a5f2b-619b-4939-970a-ba9d216a15ed", "x": ["tinycolor", "jQuery-Storage-API", "only"], "y": [2137.0, 1445.0, 1374.0], "type": "bar"}, {"marker": {"line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "PHP", "orientation": "v", "text": ["2,335", "2,185", "2,090"], "textposition": "outside", "uid": "3652bb91-8057-483c-a45d-7376da00de35", "x": ["queue", "encryption", "hashing"], "y": [2335.0, 2185.0, 2090.0], "type": "bar"}, {"marker": {"line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "Python", "orientation": "v", "text": ["55", "46", "43"], "textposition": "outside", "uid": "76578da7-7c3e-4595-b35e-ab634bde7eac", "x": ["devpi-client", "vega-datasets", "create"], "y": [55.0, 46.0, 43.0], "type": "bar"}, {"marker": {"line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "Ruby", "orientation": "v", "text": ["5,207", "2,374", "1,892"], "textposition": "outside", "uid": "b219d7a6-7d43-4089-8a57-13da289bbf40", "x": ["ruby-hmac", "gherkin-ruby", "MACAddr"], "y": [5207.0, 2374.0, 1892.0], "type": "bar"}], {"title": "What are the most used projects that have a missing or non-compliant licenses per language?", "xaxis": {"automargin": true, "tickangle": -45, "title": "name"}, "yaxis": {"automargin": true, "title": "dependent_repositories_count"}}, {"showLink": true, "linkText": "Export to plot.ly", "plotlyServerURL": "https://plot.ly"})});</script><script type="text/javascript">window.addEventListener("resize", function(){window._Plotly.Plots.resize(document.getElementById("e444fb5a-0b25-4e39-beb2-4211cee04490"));});</script>


    Chart image: images/most_used_projects_missing_or_non-compliant_licenses_per_language.png
    

### What are the most used projects that have a missing README per language?
We find the top projects that have a missing README per language using the ARRAY_AGG function and ordering by the descending dependent_repositories_count.


```python
query = """
SELECT
    language, 
    ARRAY_AGG(STRUCT(name, dependent_repositories_count, repository_stars_count) ORDER BY dependent_repositories_count DESC LIMIT 3) projects
FROM
    `bigquery-public-data.libraries_io.projects_with_repository_fields`
WHERE 
    language IN ({0})
AND
    repository_readme_filename = ''
GROUP BY 
    language
ORDER BY
    language;
""".format(languages)

question = 'What are the most used projects that have a missing README per language?'
response = run_query(query, question)
generate_chart(response, 'name', 'dependent_repositories_count', question, grouped_by_column='language', list_column='projects')
```

    Data: data/most_used_projects_missing_readme_per_language.csv
    


<div id="4b43940f-ecd6-4a5a-b7bf-354a9ddaff11" style="height: 525px; width: 100%;" class="plotly-graph-div"></div><script type="text/javascript">require(["plotly"], function(Plotly) { window.PLOTLYENV=window.PLOTLYENV || {};window.PLOTLYENV.BASE_URL="https://plot.ly";Plotly.newPlot("4b43940f-ecd6-4a5a-b7bf-354a9ddaff11", [{"marker": {"line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "C", "orientation": "v", "text": ["986", "387", "386"], "textposition": "outside", "uid": "dc86e3ff-3bbf-47c9-97a6-c073c159fb3c", "x": ["libressl-pnacl-sys", "python-Levenshtein", "yaml"], "y": [986.0, 387.0, 386.0], "type": "bar"}, {"marker": {"line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "C#", "orientation": "v", "text": ["392", "341", "168"], "textposition": "outside", "uid": "d8d313b0-a6b3-41c3-8433-16bf6b74519d", "x": ["reactiveui", "Acr.Support", "ncrontab"], "y": [392.0, 341.0, 168.0], "type": "bar"}, {"marker": {"line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "C++", "orientation": "v", "text": ["59", "17", "16"], "textposition": "outside", "uid": "3a63e429-0f56-4641-9ee4-6c434ba8e21f", "x": ["sasl", "libxmljs-mt", "mecab-python3"], "y": [59.0, 17.0, 16.0], "type": "bar"}, {"marker": {"line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "CSS", "orientation": "v", "text": ["3,054", "274", "180"], "textposition": "outside", "uid": "3b7ac50c-a45f-4f0c-a6dc-d4d63f234aee", "x": ["jekyll-theme-primer", "bootstrap-colorpicker", "basscss-type-scale"], "y": [3054.0, 274.0, 180.0], "type": "bar"}, {"marker": {"line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "HTML", "orientation": "v", "text": ["811", "311", "103"], "textposition": "outside", "uid": "27c860a7-9ff9-4af7-86e7-981f1585c7e4", "x": ["chromedriver-helper", "docs", "shrinkwrap-descriptors-impl-javaee"], "y": [811.0, 311.0, 103.0], "type": "bar"}, {"marker": {"line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "Java", "orientation": "v", "text": ["85,282", "7,625", "3,789"], "textposition": "outside", "uid": "436ca81c-0886-4dc4-8daf-f34764d369d1", "x": ["log4j", "velocity", "liquibase-core"], "y": [85282.0, 7625.0, 3789.0], "type": "bar"}, {"marker": {"line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "JavaScript", "orientation": "v", "text": ["91,717", "33,610", "19,997"], "textposition": "outside", "uid": "092414a3-dc21-4ef4-8243-2ddc65b352ca", "x": ["dom-serializer", "base", "eslint-scope"], "y": [91717.0, 33610.0, 19997.0], "type": "bar"}, {"marker": {"line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "PHP", "orientation": "v", "text": ["24,130", "23,394", "11,104"], "textposition": "outside", "uid": "8187821a-6bc9-4fd7-b7b5-8aead2e70ddb", "x": ["swiftmailer-bundle", "support", "simple-cache"], "y": [24130.0, 23394.0, 11104.0], "type": "bar"}, {"marker": {"line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "Python", "orientation": "v", "text": ["23,125", "20,304", "1,063"], "textposition": "outside", "uid": "a88f2193-2f70-4c42-8849-19d07b00ee5b", "x": ["wheel", "Pygments", "polib"], "y": [23125.0, 20304.0, 1063.0], "type": "bar"}, {"marker": {"line": {"color": "rgba(226, 74, 51, 1.0)", "width": 1}}, "name": "Ruby", "orientation": "v", "text": ["28,665", "15,487", "10,016"], "textposition": "outside", "uid": "391d6df7-a1b7-43cc-b83a-eeb850ab97f0", "x": ["rack-mount", "shellany", "jekyll-seo-tag"], "y": [28665.0, 15487.0, 10016.0], "type": "bar"}], {"title": "What are the most used projects that have a missing README per language?", "xaxis": {"automargin": true, "tickangle": -45, "title": "name"}, "yaxis": {"automargin": true, "title": "dependent_repositories_count"}}, {"showLink": true, "linkText": "Export to plot.ly", "plotlyServerURL": "https://plot.ly"})});</script><script type="text/javascript">window.addEventListener("resize", function(){window._Plotly.Plots.resize(document.getElementById("4b43940f-ecd6-4a5a-b7bf-354a9ddaff11"));});</script>


    Chart image: images/most_used_projects_missing_readme_per_language.png
    

## Findings

* Ruby, JavaScript, and CSS rank highest for most of the risk factors, while C++, Python, and Java rank among the lowest.
* The languages with the highest number of repositories are JavaScript, Java, Python, HTML, PHP, Ruby, CSS, C++, C#, and C. JavaScript tops the list at 3,273,555 repositories.
* The number of dependencies per repository are increasing each year, with the average number of dependencies for JavaScript repositories growing 225% in the past year, to 235 dependencies per repository.
* JavaScript, CSS, and HTML have the highest average number of dependencies per respository. JavaScript tops the list at 66 dependencies per respository.
* Ruby, JavaScript, and CSS have the highest total open issues for all dependencies per repository. Ruby tops the list at 7,405 open issues for dependencies per repository.
* Ruby, Javascript, and CSS have the highest total proportion of repositories with at least one dependency with an unmaintained/deprecated status. Ruby tops the list at 5%.
* Ruby, Javascript, and CSS have the highest total proportion of repositories with at least one dependency with only one contributor. Ruby tops the list at 34%.
* Ruby, Javascript, and CSS have the highest total proportion of repositories with at least one dependency that has not been updated in the past 12 months. Ruby tops the list at 54%.
* C#, Java, and Ruby have the highest total proportion of repositories with a missing or non-compliant license. Ruby tops the list at 17%.
* C#, Java, and Ruby have the highest total proportion of repositories with a missing README. Ruby tops the list at 18%.
* Jade (JavaScript), atomic (Ruby), and (grunt-usemin) are the the most used projects that have an unmaintained/deprecated status.
* erubis (Ruby), concat-map (JavaScript), and asynckit (Javascript) are the most used projects that have only one contributor per language.
* builder (Ruby), mime-types (Ruby), and sass-rails (Ruby) are the most used projects that have not been updated in the past 12 months.
* ruby-hmac (Ruby), gherkin-ruby (Ruby), and queue (PHP) are the most used projects that have a missing or non-compliant licenses per language.
* dom-serializer (JavaScript), log4j (Java), and base (JavaScript) are the most used projects that have a missing README.

## Limitations
* Repositories are classified by the dominant language contained within the repository, but there are often multiple languages included in a single repository.
* The dataset did not contain information about the number of identified security vulnerabilities, which would be the best metric for indicating risk.
* The dataset did not contain information about the total number of issues created for the repositories, only currently open issues.
* We used means in the analysis, when it may have been more appropirate to use medians, since a median function is not build into Google BigQuery.
 

## Future work
We hope that a future study can evaluate how much the risk factors provided here contribute to the predictive power of future bugs and vulnerabilities. In addition, we want to evaluate the correlations between the risk factors and other factors, such as issues opened, security vulnerabilities, or dependencies that are not on latest version. 

Another analysis can look into identifying traits of contributors of open source projects, which can help developers evaluate the risk in accepting pull requests from them or turn on gated check in based on the risk score of a pull request.



## Conclusion
The number of dependencies being included in open source software ecosystems is increasing over time and is showing no signs of slowing. Since developers have no control over the transitive dependencies that are included by the direct depdendencies that are included in their applications, the risks must be carefully weighed against the benefits. 

The Ruby, JavaScript, and CSS ecosystems rank highest in most of the risk factors, while C++, Python, and Java rank among the lowest.

Data privacy is a big concern of Human-Centered Data Science. Data scientists could go to great lengths to make sure that a user's anonymity is protected. However, by carelessly including a malicious dependency or even a dependency that has a transitive dependency on a malicious dependency, an application developer can unknowingly expose their users to massive data leaks. As sophisticated multi-stage targeted attacks become more common, a culture shift is due among some of the top software ecosystems.

## References
1. [2017 State of Open Source Security Report](https://snyk.io/stateofossecurity/). Snyk. October, 2017.
2. Schneider, Zach (November 27, 2018). [event-stream vulnerability explained](https://schneid.io/blog/event-stream-vulnerability-explained/)
3. Decan, Alexandre; Mens, Tom; Grosjean, Philippe (Octover 13th, 2017).
[An Empirical Comparison of Dependency Network Evolution in Seven Software Packaging Ecosystems](https://arxiv.org/pdf/1710.04936.pdf)
4. Katz, Jeremy (October 11, 2018). [Don’t believe the download numbers when evaluating open source projects](https://blog.tidelift.com/dont-believe-the-download-numbers-when-evaluating-open-source-projects)
5. [Parallel Coordinates](http://www.scikit-yb.org/en/latest/api/features/pcoords.html).  District Data Labs.
