# AUR packaging

GitHub's `aur/pixora-icons-git/` files are the source for the AUR package. Every push to `main` validates the package and publishes a new version to AUR, without importing its older recipe or committing back to GitHub.

The workflow uses the recipe's `pkgver()` function with full Git history, giving versions such as `1.0.0.r450.gabcdef0-1`. Each new commit increases the revision count. It updates `pkgver`, resets `pkgrel` to `1` in a temporary copy, and publishes that copy with matching `.SRCINFO`. Rerunning the same commit makes no extra AUR update. If several pushes arrive together, an older run can skip publication so the latest run publishes the current version.

Users receive these updates through their AUR helper's usual upgrade command. `pacman -Syu` alone only updates packages available from configured binary repositories.

When changing the package:

1. Update `PKGBUILD`. The workflow calculates the published version automatically;
2. On Arch Linux, run `makepkg --printsrcinfo > .SRCINFO` from `aur/pixora-icons-git/`;
3. Run `python3 -m unittest discover -s tests` from the repository root;
4. Submit both packaging files together. After merging into `main`, validation runs before publication.

The workflow also supports **Run workflow** on `main` for a manual retry. Icon-only and README commits trigger updates too. It uses the existing `AUR_KEY` secret in the `aur-packages` environment, only in the publication job; pull requests run validation without the key.

The package must include Pixora, Pixora Dark and Pixora Accent, with their relative symlinks preserved. The tests check the package contents and publish into a temporary local Git repository to prevent another AUR-to-GitHub overwrite.
