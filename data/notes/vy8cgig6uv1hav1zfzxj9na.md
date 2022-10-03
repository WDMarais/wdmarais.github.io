# Summary:
- Do the initial `GitHub Actions` setup
- Make a commit to your repo's `main` branch
- GitHub Actions will automatically publish the updates to your git pages.

# Prerequisites:
- Install [git](https://git-scm.com/download)
- Initializes a npm repo at the root of your workspace (same directory as `dendron.yml`)
```
npm init -y
```
Install [dendron-cli](https://wiki.dendron.so/notes/RjBkTbGuKCXJNuE4dyV6G#setup)

```
npm install @dendronhq/dendron-cli@latest
```

# Steps:
## Preparation
- Create GitHub repo
- `npx dendron publish init`
- `>Dendron: Configure (yaml)`, and make the following modification under `publishing`, where `WEBSITE_URL` is (in this case) `https://{GITHUB_USERNAME}.github.io`
```
...
publishing:
    ...
    siteUrl: {WEBSITE_URL}
```
- Set up GitHub Pages
```
git checkout -b pages
git push -u origin HEAD
```

- Setup GitHub Actions
    - Switch back to your main branch
    ```
    git checkout main
    ```
    - Create a workflow
        - mac and linux
        ```
        mkdir -p .github/workflows
        touch .github/workflows/publish.yml
        ```
        - windows
        ```
        mkdir -p .github/workflows
        New-Item .github/workflows/publish.yml
        ```
    - Setup workflow
```
name: Build Dendron Static Site

on:
  workflow_dispatch: # Enables on-demand/manual triggering
  push:
    branches:
    - main

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout source
      uses: actions/checkout@v2
      with:
        fetch-depth: 0

    - name: Restore Node modules cache
      uses: actions/cache@v2
      id: node-modules-cache
      with:
        path: |
          node_modules
          .next/*
          !.next/.next/cache
          !.next/.env.*
        key: ${{ runner.os }}-dendronv2-${{ hashFiles('**/yarn.lock', '**/package-lock.json') }}

    - name: Install dependencies
      run: yarn

    - name: Initialize or pull nextjs template
      run: "(test -d .next) && (echo 'updating dendron next...' && cd .next && git reset --hard && git pull && yarn && cd ..) || (echo 'init dendron next' && yarn dendron publish init)"

    - name: Restore Next cache
      uses: actions/cache@v2
      with:
        path: .next/.next/cache
        # Generate a new cache whenever packages or source files change.
        key: ${{ runner.os }}-nextjs-${{ hashFiles('.next/yarn.lock', '.next/package-lock.json') }}-${{ hashFiles('.next/**.[jt]s', '.next/**.[jt]sx') }}

    - name: Export notes
      run: yarn dendron publish export --target github --yes

    - name: Deploy site
      uses: peaceiris/actions-gh-pages@v3
      with:
        github_token: ${{ secrets.GITHUB_TOKEN }}
        publish_branch: pages
        publish_dir: docs/
        force_orphan: true
        #cname: example.com
```
Commit your changes
```
git add .
git commit -m "add workflow"
git push
```

## Previewing Notes
```
npx dendron publish dev
```
Visit `http://localhost:3000` to check.
