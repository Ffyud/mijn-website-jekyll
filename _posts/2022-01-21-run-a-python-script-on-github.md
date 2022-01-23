---
layout: post
title: "Run a Python script on Github"
categories: [Github, Python]
---

# Run a Python script on Github

Github provides a way for running scripts that exist in your repository, called [Github Actions](https://github.com/features/actions), which enables you to create a CI/CD pipeline. [TravisCI](https://www.travis-ci.com/) offers similiar functionality, but is [not for free anymore](https://blog.travis-ci.com/2020-11-02-travis-ci-new-billing), while Github still is.

## A usecase for Python

Running Python is also possible on Github. This might be handy if you have a Python script that collects data. Let Python collect the data, save it to a file and commit that new file back to your repository automatically.

### 1 Create a workflow

- Create a folder with the name *.github*.
- Inside that folder, create a folder with the name *workflows*.
- Create a .yml file inside *workflows* with a name like "run-python.yml".

### 2 Create the syntax

The yml file is where you put the [Workflow syntax](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions) that instructs Github what to do.

1. Provide a name for this job.

        name: run-python

2. Pick a trigger to start the job. A common trigger is a push to Github.

        on:
          push:
            branches:
              - main

    Triggering the job by a schedule is also usefull, based on a [cron pattern](https://crontab.guru/#0_0_*_*_*).

        on:
          schedule:
            - cron: '0 0 * * *'

3. Configure the actual job you want to run. This example will start a build on the lastest version of Ubuntu. It will checkout your code (*actions/checkout@v2*), install Python (*actions/setup-python@v2*), install the relevant Python modules and run the actual python script. Then it will commit and push the resulting changes to the main branch.
   
        jobs:
          build:
            runs-on: ubuntu-latest
            steps:

              - name: checkout repo content
                uses: actions/checkout@v2

              - name: setup python
                uses: actions/setup-python@v2
                with:
                  python-version: '3.8.2'
                  
              - name: install python packages
                run: |
                  python -m pip install --upgrade pip
                  pip install requests
                  
              - name: execute py script
                run: python3 <the-name-of-your-python-script>
                  
              - name: commit files
                run: |
                  git config --local user.email "action@github.com"
                  git config --local user.name "GitHub Action"
                  git add -A
                  git commit -m "update data" -a
                  
              - name: push changes
                uses: ad-m/github-push-action@v0.6.0
                with:
                  github_token: ${{ secrets.GITHUB_TOKEN }}
                  branch: main  

