# Git rev-parse explained


`git rev-parse` in plain words
==============================

Think of Git like your phone contacts.

*   You say **“Mom”** → phone dials **+91…** (the exact number).
*   In Git you say **“HEAD”** or **“main”** → `git rev-parse` tells you the **exact commit ID** (the long hash like `9f2e4c…`).

So, **`rev-parse` = translator** that turns nicknames (HEAD, main, tags, `HEAD~2`) into exact IDs or handy info about your repo.

* * *

Why it’s useful
---------------

*   **Be precise** in scripts/CI (computers love exact IDs).
*   **Find paths** (repo root, .git folder).
*   **Quick checks** (am I inside a repo? what branch am I on?).

* * *

5 commands you’ll actually use
------------------------------

1.  **Exact commit you’re on** (the “phone number” of your current code)

```bash
git rev-parse HEAD
```

2.  **Short ID** (easy to show in logs/build names)

```bash
git rev-parse --short HEAD
```

3.  **Current branch name**

```bash
git rev-parse --abbrev-ref HEAD
```

(If it prints `HEAD`, you’re not on a branch—“detached HEAD”.)

4.  **Repo root folder** (top of your project)

```bash
git rev-parse --show-toplevel
```

5.  **Am I inside a Git repo?**

```bash
git rev-parse --is-inside-work-tree
```

Prints `true` or `false`.

* * *

Mini use cases (copy-paste friendly)
------------------------------------

*   **Always run from repo root**
    ```bash
    cd "$(git rev-parse --show-toplevel)"
    ```
*   **Make a build label**
    ```bash
    echo "Build: $(git rev-parse --short HEAD)"
    ```
*   **Get hash of any name**
    ```bash
    git rev-parse main
    git rev-parse HEAD~2      # “two steps back” commit
    git rev-parse v1.2.0      # tag to hash
    ```

* * *

One-line mental model
---------------------

You give a **nickname** → `rev-parse` gives the **exact thing** (hash/path/boolean) so scripts don’t guess.

If you want, tell me where you’ll use it (CI, shell script, makefile), and I’ll craft a tiny snippet for that.



---
