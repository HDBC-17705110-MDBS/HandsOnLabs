# Deploying your personal website

## Objectives

The goal of this hands-on activity is to introduce you to the basics of cloud computing through the deployment of a personal website using Jekyll and GitHub Pages. Additionally, you will explore and write a blog entry on a cloud application in the field of biomedicine. 

## Introduction

Github pages hides the complexity of setting up a web server and allows you to host a website for free. This is a PaaS (Platform as a Service) that allows you to host static websites directly from your GitHub repository. You are responsible for creating the website and pushing it to your repository, and GitHub takes care of the rest.


## Prerequisites

- GitHub account (create one at [https://github.com](https://github.com) if you don't have one)

## Tools

- Jekyll (a static site generator) - [https://jekyllrb.com](https://jekyllrb.com)
- GitHub Pages (a static site hosting service) - [https://pages.github.com](https://pages.github.com)

## Task 1: Setting up your personal website

1. Fork [https://github.com/daattali/beautiful-jekyll](https://github.com/daattali/beautiful-jekyll)to your GitHub account by clicking on the "Fork" button at the top right corner of this page. You are free to choose any other Jekyll theme you like.
2. Rename the repository to `username.github.io`, where `username` is your GitHub username.
3. Test your website by visiting `https://username.github.io` in your web browser. It may take a few minutes for the website to be available. To check if your website is ready, wait for a green checkmark to appear next to your repository name on the GitHub Pages section of your repository settings. It may take a few minutes for the website to be available.
3. Edit the `_config.yml` file to customize your website. You can use the GitHub web interface to edit the file directly. Personalize to your liking.
4. Commit the changes (use master branch).

## Task 2: Writing a Blog Entry

1. Explore the cloud application in the field of biomedicine that you are interested in.
2. Write a blog entry on your personal website about the cloud application you explored. You can use the `/_posts/` directory to create a new blog entry. The file name should follow the format `YYYY-MM-DD-title.md`. Check the existing blog entries for examples.
3. You can add a new blog entry by navigating to the `/_posts/` directory and clicking on the "Add file -> Create a new file" button.
4. Commit the changes (use master branch).

## Task 3: Sharing your personal website
1. Make a Pull Request to course repository with the link to your personal website.

## Optional Task: Local Deployment

### Prerequisites
- Docker - [https://www.docker.com](https://www.docker.com)

1. Install Docker in your computer.
2. Use the `jekyll/jekyll` Docker image to build and serve your website locally.
3. Access your website at `http://localhost:4000` in your web browser.
4. Customize your website and check the changes locally.
