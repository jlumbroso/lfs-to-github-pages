# LFS to GitHub Pages prototype

GitHub Pages doesn't serve files that are stored in LFS, even though [this has been consistently requested for years](https://github.com/git-lfs/git-lfs/issues/1342). This is understandable: **It would instantly turn GitHub Pages from the world's largest code hosting service to the world's largest file hosting service.**

That said this repository showcases a simple work-around...

## Demo

This repository has been configured to store `*.webp` files with Git LFS.

This is the file `image.webp` as stored in the `main` branch:

![The `image.webp` stored in branch `main` *IS* stored through LFS.](https://raw.githubusercontent.com/jlumbroso/lfs-to-github-pages/main/screenshots/screenshot-main-lfs.png "Screenshot of branch `main`")

After GitHub Actions' continuous integration, this is the file `image.webp` as stored in the `gh-pages` branch:

![The `image.webp` stored in branch `main` is *NOT* stored through LFS.](https://raw.githubusercontent.com/jlumbroso/lfs-to-github-pages/main/screenshots/screenshot-gh-pages-not-lfs.png "Screenshot of branch `gh-pages`")

You can confirm that this file is being served through GitHub Pages:

https://jlumbroso.github.io/lfs-to-github-pages/image.webp

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

## Copyright & License

The image "_Robots Cutting Books_" is copyrighted 2022 by the artist, Jérémie Lumbroso, and is made available here under the [CC BY-ND 4.0](https://creativecommons.org/licenses/by/4.0/) license, which means you can distribute it, with attribution, for any purpose, as long as you don't distribute modified versions of it.

The code in this repository is provided under the MIT license.
