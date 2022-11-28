# LFS to GitHub Pages prototype

Prototype of a solution to deploy LFS files on GitHub Pages.

![The `image.webp` stored in branch `main` *IS* stored through LFS.](/screenshot/screenshot-main-lfs.png?raw=true "Screenshot of branch `main`")

![The `image.webp` stored in branch `main` is *NOT* stored through LFS.](/screenshot/screenshot-gh-pages-not-lfs.png?raw=true "Screenshot of branch `gh-pages`")

## How It Works

1. First, the continuous integration fetches the latest version of the repository's `main` branch, fetching the files stored through Git-LFS:

   ```yaml
   - uses: actions/checkout@v3
   with:
       fetch-depth: 0 # Fetch all history for .GitInfo and .Lastmod
       lfs: true # Fetch large files
   ```

2. We remove the Git LFS hooks, and then remove all the Git metadata associated with the repository:

```yaml
# REF: https://github.com/git-lfs/git-lfs/issues/3026#issue-326390969
# REF: https://stackoverflow.com/a/50177571/408734
- name: Turn off LFS
run: >-
    git lfs uninstall;
    rm -Rf .git;
    rm .gitattributes;
```

3. We deploy the files we have checked out (including the files stored through Git LFS) to the `gh-pages` branch:

```yaml
- name: Deploy to gh-pages branch
uses: peaceiris/actions-gh-pages@v3.9.0
with:
    github_token: ${{ secrets.GITHUB_TOKEN }}
    publish_dir: ./
    publish_branch: gh-pages
    user_name: "github-actions[bot]"
    user_email: "github-actions[bot]@users.noreply.github.com"
```
