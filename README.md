<a href="https://github.com/sponsors/jbinfo" target="_blank">
  <img src="https://img.shields.io/badge/Sponsor-jbinfo-orange" alt="Sponsor @jbinfo" />
</a>

# jekyll-responsive-images
A composite action that generate responsive images for your Jekyll website using the jampack tool which generate images and replace html img tag with the appropriate one, check jampack docs to know how it works. This action to be put on the jekyll.yml workflow file after the build step and before upload artifact step.

## Input
The path to the generated Jekyll website, by default it's `_site/`
```yaml
inputs:
  path:
    description: "Path of the directory containing the static assets."
    required: true
    default: "_site/"

```

## Usage
You must first enable **Github Actions** as the method to build and deploy the project, you can do it on the repo settings, see screenshot below:
![Screenshot_13-10-2024_20155_github com](https://github.com/user-attachments/assets/173e5a28-c25d-4899-a25e-d6aca74f4532)

This action to be put on the Jekyll builld and depploy workflow located at `.github/workflows/jekyll.yml` in your repo after the build step and before the upload artifact step, see example below:

```yaml
# This workflow uses actions that are not certified by GitHub.
# They are provided by a third-party and are governed by
# separate terms of service, privacy policy, and support
# documentation.

# Sample workflow for building and deploying a Jekyll site to GitHub Pages
name: Deploy Jekyll site to Pages

on:
  # Runs on pushes targeting the default branch
  push:
    branches: ["main"]

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

# Sets permissions of the GITHUB_TOKEN to allow deployment to GitHub Pages
permissions:
  contents: read
  pages: write
  id-token: write

# Allow only one concurrent deployment, skipping runs queued between the run in-progress and latest queued.
# However, do NOT cancel in-progress runs as we want to allow these production deployments to complete.
concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  # Build job
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      - name: Setup Ruby
        uses: ruby/setup-ruby@8575951200e472d5f2d95c625da0c7bec8217c42 # v1.161.0
        with:
          ruby-version: '3.2.2' # Not needed with a .ruby-version file
          bundler-cache: true # runs 'bundle install' and caches installed gems automatically
          cache-version: 0 # Increment this number if you need to re-download cached gems
      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v5
      - name: Build with Jekyll
        # Outputs to the './_site' directory by default
        run: bundle exec jekyll build --baseurl "${{ steps.pages.outputs.base_path }}"
        env:
          JEKYLL_ENV: production

      - name: Genarate Responsive Images
        uses: jbinfo/jekyll-responsive-images@latest

      - name: Upload artifact
        # Automatically uploads an artifact from the './_site' directory by default
        uses: actions/upload-pages-artifact@v3

  # Deployment job
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```
