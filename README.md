# KiCad Project Template

A standardized starting point for new PCB projects. It includes custom KiCad
settings (text variables, field templates, design rules, jobset)
and links the shared ECAD component library (symbols, footprints, 3D models) as
a Git submodule under `lib/`.

---

## Prerequisites

- Git 2.13 or newer (for sane submodule defaults) (check with `git --version`).

---

# Starting a new project from this template

1. On GitHub, click **Use this template** to create your new project repo.
    > **Important:** GitHub's template feature copies the `.gitmodules` file but
    > **not** the contents of the submodule. After cloning your new repo you must
    > initialize the submodule yourself (next step).
2. Clone your new project **with submodules**:

```
   git clone --recurse-submodules <your-new-repo-url>
```

If you already cloned without that flag, populate the submodule with:

```
   git submodule update --init --recursive
```

3. (Optional, recommended) Load the repo's convenience Git settings:

```
   git config --local include.path ../.gitconfig
```

This enables quality-of-life behavior for working with submodules (clearer
`git status`/`git diff` output and a safety check before pushing). It is
optional and only affects your local clone.

---

## Updating the library to the latest version (consuming)

Use this when you want to pull in newer symbols/footprints/3D models that have
been added to the shared library. Run from the **project root**:

```
git submodule update --remote lib
git add lib
git commit -m "Update component library to latest"
```

- The first command advances the `lib/` submodule to the newest commit on the
  library's `main` branch (the branch is configured in `.gitmodules`).
- The second and third commands record that new commit as this project's pinned
  library version, so the update is reproducible for anyone else on the project.
  Each project pins a specific library commit. Updating is always a deliberate
  act — the library never changes underneath an open project on its own.

---

## Contributing new parts to the library (authoring)

Use this when you create a new symbol/footprint/3D model **while working in a
project** and want to push it back to the shared library so other projects can
use it.

> All work happens inside the `lib/` submodule, which is its own repository.

1. Move into the submodule and get onto a branch (a fresh clone leaves it in
   detached HEAD):

```
   cd lib
   git status
```

If status shows `HEAD detached at <hash>`:

```
   git checkout main
```

If that fails with _"Your local changes would be overwritten by checkout"_,
stash first, switch, then restore:

```
   git stash
   git checkout main
   git stash pop
```

2. Stage, commit, and push your new part **to the library repo**:

```
   git add .
   git commit -m "[footprint] Add <part name>"
   git push
```

> Once contributor review is enabled on the library, push a feature branch
> and open a pull request instead of pushing to `main` directly.

3. Return to the project and record the new library commit as this project's
   pinned version:

```
   cd ..
   git add lib
   git commit -m "Update component library pointer"
   git push
```

Step 3 is what links your project to the exact library commit containing the
new part. Skipping it means the part exists in the library but your project
still points at the old commit.

---

## Quick reference

| I want to...                                   | Run (from project root unless noted)              |
| ---------------------------------------------- | ------------------------------------------------- |
| Set up a freshly cloned project                | `git submodule update --init --recursive`         |
| Pull the latest shared library into my project | `git submodule update --remote lib` then commit   |
| Add a new part to the shared library           | work inside `lib/`, push, then commit `lib` above |
| Refresh after pulling a teammate's changes     | `git submodule update --init --recursive`         |

---

## Helpful links

- [Submodules chapter — Pro Git book](https://git-scm.com/book/en/v2/Git-Tools-Submodules)
- [`git submodule` reference](https://git-scm.com/docs/git-submodule)
- [`.gitmodules` reference](https://git-scm.com/docs/gitmodules)
