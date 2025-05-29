# Git revert explanation


## Prompt:
what is git revert -m-1

## Response:
`git revert -m 1 <commit-hash>` (note the space between `-m` and `1`) is a command used to **revert a merge commit** in Git.

* * *

### 🔹 What does it do?

It creates a new commit that **undoes the changes** introduced by a **merge commit** — but it needs to know **which parent to keep**. That’s what the `-m` option is for.

* * *

### 🔹 Explanation of the `-m` option:

*   `-m` stands for **"mainline"**.
    
*   A merge commit has **two parents** (usually):
    
    *   `Parent 1`: the branch you merged **into**
        
    *   `Parent 2`: the branch you **merged**
        

When you run:

```
git revert -m 1 <merge-commit-hash>
```

You are saying:

> "Undo this merge commit, and pretend the main line (Parent 1) is the one we want to keep."

* * *

### 🔹 Example:

Let’s say:

*   You're on `main` branch.
    
*   You merged a feature branch `feature-x` into `main`, creating a merge commit `abc123`.
    

To revert that merge, keeping the changes from `main` and removing `feature-x`'s effect:

```bash
git revert -m 1 abc123
```

This **does not delete the `feature-x` branch**, it just undoes the changes that were added by that merge.

* * *

### ⚠️ Common mistakes:

*   `git revert -m-1` ← ❌ Incorrect (missing space)
    
*   Correct usage is: `git revert -m 1 <commit-hash>`
    

* * *

Let me know if you'd like a visual diagram to understand this!

## Prompt:
give me example where i have feaures branch with 

code as follow 

line1
line2
line3

and UAT branch have code
line1
line2
it create merge requirest #1 with commitid ce12345
what exactly happens when 
git revert -m 1 ce12345

## Response:
Great! Let’s walk through your example **step-by-step** with the real behavior of:

```bash
git revert -m 1 ce12345
```

* * *

### 🧾 Scenario

#### 🟦 UAT branch:

```txt
line1
line2
```

#### 🟨 feature branch:

```txt
line1
line2
line3
```

You create a **merge request (MR #1)** and merge the **feature** branch into the **UAT** branch.

This creates a **merge commit `ce12345`**.

Now, **UAT** becomes:

```txt
line1
line2
line3
```

* * *

### 🔁 Now you run:

```bash
git revert -m 1 ce12345
```

* * *

### 🔍 What does this do?

*   `-m 1` means:
    
    > “I want to **revert** this merge commit, keeping the changes from **parent 1**, which is the UAT branch before the merge.”
    
*   Git compares:
    
    *   **Parent 1**: UAT before the merge (with just `line1`, `line2`)
        
    *   **Merge result**: UAT after merge (`line1`, `line2`, `line3`)
        

So, it generates a **new commit** that **undoes the effect** of merging `line3`.

* * *

### ✅ Result after revert:

Your **UAT branch** becomes:

```txt
line1
line2
```

🔁 So the effect of the `feature` branch (`line3`) is **reversed**.

* * *

### 🔒 What stays unchanged?

*   The **original merge commit (`ce12345`) still exists** in history.
    
*   The **feature branch** also stays untouched.
    
*   A **new commit** is added on top of UAT that reverts the changes from the merge.
    

* * *

### 🧠 Summary:

| Step | Code on UAT |
| --- | --- |
| Before merge | `line1`  
`line2` |
| After merge | `line1`  
`line2`  
`line3` |
| After `git revert -m 1` | `line1`  
`line2` |

Would you like a Git log view or diagram for visual clarity?

## Prompt:
what happens when I use just command git revert ce12345

## Response:
Excellent question.

When you run:

```bash
git revert ce12345
```

on a **merge commit** (like `ce12345`), **without** the `-m` option, Git will:

* * *

### ❌ Throw an error:

```bash
error: commit ce12345 is a merge but no -m option was given.
fatal: revert failed
```

* * *

### 🧠 Why?

*   A **merge commit** has **two or more parents**.
    
*   Git doesn’t know **which parent to consider as "mainline"** (i.e., which branch's changes to preserve).
    
*   So Git **requires `-m`** when reverting a merge commit.
    

* * *

### ✅ Correct usage:

You **must** specify the mainline parent:

```bash
git revert -m 1 ce12345   # Revert keeping UAT changes
```

Or

```bash
git revert -m 2 ce12345   # Revert keeping feature branch changes (rare case)
```

* * *

### 🔄 For regular (non-merge) commits:

If `ce12345` was **not a merge commit**, then:

```bash
git revert ce12345
```

✅ **Would work fine** — it would create a new commit that undoes what `ce12345` introduced.

* * *

### 📌 Summary

| Command | Result |
| --- | --- |
| `git revert ce12345` | ❌ Fails with error (merge commit needs `-m`) |
| `git revert -m 1 ce12345` | ✅ Reverts the merge, keeps parent 1 (e.g. UAT) |
| `git revert -m 2 ce12345` | ✅ Reverts the merge, keeps parent 2 (e.g. feature) |

Let me know if you want to simulate this with a step-by-step Git setup!

## Prompt:
supose we have log as follow,

merge commit #1 -- ce12345
3931fae (origin/features/new, features/new) added all files
45bc9d7 UAT branch commit

git revert ce12345
it cant goes back to commit  3931fae correct ?

as it is commit of feature branch

## Response:
You're absolutely right — let's break it down clearly.

* * *

### 🧾 Given Git log (top to bottom means newer to older):

```
ce12345  ← merge commit (MR #1) from features/new → UAT
3931fae  ← last commit on features/new branch
45bc9d7  ← commit on UAT before merge
```

This means:

*   `ce12345` is a **merge commit** with:
    
    *   Parent 1: `45bc9d7` (UAT before merge)
        
    *   Parent 2: `3931fae` (feature branch head)
        

* * *

### 🔁 Now you run:

```bash
git revert ce12345
```

### ❌ What happens?

Git throws this error:

```
error: commit ce12345 is a merge but no -m option was given.
fatal: revert failed
```

Because you must tell Git **which parent to keep** (UAT or feature).

* * *

### ✅ If you run:

```bash
git revert -m 1 ce12345
```

Then Git:

*   Keeps **Parent 1** (UAT: `45bc9d7`)
    
*   Reverts the changes **introduced by the merge commit**
    
*   Creates a **new commit** that undoes anything that came from **Parent 2 (3931fae)**
    

* * *

### 🔄 Does it "go back to 3931fae"?

No — and that's a very important detail.

*   `git revert -m 1 ce12345` **does not checkout or reset to** `3931fae`.
    
*   It just **undoes the changes** introduced **from** `3931fae` into UAT.
    
*   Your history still shows:
    
    *   `ce12345` (merge)
        
    *   followed by a new revert commit (let’s say `abcd1234`)
        

So the resulting state of the UAT branch is **functionally like** `45bc9d7`, but the history is:

```
abcd1234 ← revert commit (undoes merge)
ce12345  ← merge
45bc9d7  ← previous UAT commit
```

* * *

### 📌 Summary

| Question | Answer |
| --- | --- |
| Does `git revert ce12345` work? | ❌ No, error without `-m` |
| Does revert "go back" to `3931fae`? | ❌ No, it undoes changes _from_ it |
| Does `3931fae` get deleted or undone? | ❌ No, it stays intact in history |
| Is UAT state same as before merge (45bc9d7)? | ✅ Functionally yes, via a new commit |

* * *

Let me know if you'd like a visual Git graph for this!

## Prompt:
is it follow ,
git revert -m 3 ce12345 

?

## Response:
Great question — and the answer is **❌ No**, `git revert -m 3 ce12345` is **invalid** in your case.

* * *

### 🔍 Why?

The `-m <parent-number>` in `git revert -m` tells Git **which parent to consider as "mainline"**, meaning:

*   **`-m 1`** → keep changes from **parent 1** (usually the branch you merged _into_, like `UAT`)
    
*   **`-m 2`** → keep changes from **parent 2** (usually the branch you _merged_, like `features/new`)
    

A **merge commit** usually has **two parents**.

So:

*   `-m 1` ✅ valid
    
*   `-m 2` ✅ valid
    
*   `-m 3` ❌ **invalid unless it's a 3-way merge**, which is rare
    

* * *

### ⚠️ If you run:

```bash
git revert -m 3 ce12345
```

Git will return:

```
error: mainline was specified as 3, but commit ce12345 has only 2 parents
fatal: revert failed
```

* * *

### ✅ Correct usage for your scenario (2-parent merge):

*   `git revert -m 1 ce12345` → revert the merge, **keep UAT**
    
*   `git revert -m 2 ce12345` → revert the merge, **keep feature branch**
    

* * *

### 👀 How to check how many parents a merge has?

You can run:

```bash
git show --no-patch --pretty=%P ce12345
```

This will print the parent commit SHAs. If it shows **two SHAs**, then only `-m 1` and `-m 2` are valid.

* * *

Let me know if you'd like to see what happens with 3-way or octopus merges (e.g., `git merge branch1 branch2 branch3`)!