---
base: "[[Reading List.base]]"
Category:
  - Data Science
  - MLOps
Author: lakeFS
Status: Not started
---
- [Docs](https://doc.dvc.org/)

**Data Version Control** is a [free](https://github.com/treeverse/dvc/blob/main/LICENSE), open-source tool for [data management](https://doc.dvc.org/user-guide/data-management/remote-storage), [ML pipeline](https://doc.dvc.org/user-guide/pipelines) automation, and [experiment management](https://doc.dvc.org/user-guide/experiment-management). This helps data science and machine learning teams manage **large datasets**, make projects **reproducible**, and **collaborate** better.

DVC takes advantage of the existing software engineering toolset your team already knows (Git, your IDE, CI/CD, cloud storage, etc.). Its design follows this set of principles:

1. **Codification**: Define any aspect of your ML project (data and model versions, ML pipelines and experiments) in human-readable [metafiles](https://doc.dvc.org/user-guide/project-structure). This enables using best practices and established engineering toolsets, reducing the gap with data science.
2. **Versioning**: Use Git (or any SCM) to version and share your entire ML project including its source code and configuration, parameters and metrics, as well as data assets and processes by committing DVC metafiles (as placeholders).
3. **Secure collaboration**: Control the access to all aspects of your project and share them with the people and teams you choose.