---
layout: post
title: "Run a Python script on Github"
categories: [Github, Python]
---

# Run a Python script on Github

Github provides a way for running scripts that exist in your repository, called [Github Actions](https://github.com/features/actions). It enables you to build, package, publish, deploy, test and do other stuff with your project. [Travis CI](https://www.travis-ci.com/) offers similiar functionality, but is [not for free anymore](https://blog.travis-ci.com/2020-11-02-travis-ci-new-billing), while Github still is.

Running Python is posible on Github. There are workflows for [testing](https://docs.github.com/en/actions/automating-builds-and-tests/building-and-testing-nodejs-or-python?langId=py) or [publishing a Python package](https://github.com/actions/starter-workflows/blob/main/ci/python-publish.yml). You can also just run a Python script that generates something like a file and save that file in the repository, automatically.

## 1 Create a job

For running a job that will be picked up by Github Actions, you'll have to set up the following folder structure in the root of your repository.

- Create a folder with the name *.github*.
- Inside that folder, create a folder with the name *workflows*.
- Create a YAML file inside workflows with a name like *run-python.yml*.
<!-- -->
    .github/workflows/run-python.yml

The YAML file is where you put the [Workflow syntax](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions) that instructs Github what to do.

## 2 Building the actual job

First, you should provide a name.

``` yaml
name: run-python
```

Second, you create a trigger for the job defined by [on](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#on). A common trigger is a push to Github.

``` yaml
on:
  push:
    branches:
      - main
```

Triggering the job on a schedule based on a [cron pattern](https://crontab.guru/#0_0_*_*_*) is also useful.

``` yaml
on:
  schedule:
    - cron: '0 0 * * *'
```

Then you configure the actual job you want to run. This example will start a build on the lastest version of Ubuntu. 

It will checkout your code (actions/checkout@v2), install Python (actions/setup-python@v2), install the relevant Python packages and run your actual Python script. 
    
Lastly it will commit and push the resulting changes to the main branch.
   
``` yaml
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
          github_token: {% raw %}${{ secrets.GITHUB_TOKEN }}{% endraw %}
          branch: main
```

The full run-python.yml should look something like this. Keep in mind that the YAML will not work when the indentation is not correct. The nesting style of a YAML is comparable to Python.

``` yaml
name: get-latest-offers

on:
  schedule:
    - cron: '0 0 * * *'

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
          github_token: {% raw %}${{ secrets.GITHUB_TOKEN }}{% endraw %}
          branch: main  
```

## 3 See it run

Check out the newly created job under the *Actions* tab on the homepage of your repository. If you are using the *on push* trigger in the YAML, you'll see that it starts to run right after you pushed the new workflow job to Github.

