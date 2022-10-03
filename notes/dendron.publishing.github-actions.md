---
id: vy8cgig6uv1hav1zfzxj9na
title: GitHub Actions
desc: ''
updated: 1664788712283
created: 1664788061096
recall: ignore
---
# Summary:
1. Do the initial `GitHub Actions` setup
1. Make a commit to your repo's `main` branch
1. GitHub Actions will automatically publish the updates to your git pages.

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
1. Create GitHub repo
1. `npx dendron publish init`
1. `>Dendron: Configure (yaml)`, and make the following modification under `publishing`, where `WEBSITE_URL` is (in this case) `https://{GITHUB_USERNAME}.github.io`
```
...
publishing:
    ...
    siteUrl: {WEBSITE_URL}
```
1. Set up GitHub Pages
```
git checkout -b pages
git push -u origin HEAD
```

## Previewing Notes
```
npx dendron publish dev
```
Visit `http://localhost:3000` to check.
