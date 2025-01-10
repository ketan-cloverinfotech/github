# Documentation: Git Revert vs Git Reset

This document explains the purpose, use cases, and step-by-step instructions for using `git revert` and `git reset`. It is essential to understand the differences between the two commands to use them effectively.

---

## **1. Git Revert**

### **Overview**

- `git revert` creates a new commit that undoes the changes introduced by a specific commit or a range of commits.
- It is a safe way to undo changes because it doesn’t alter the commit history.

### **Use Cases**

- Undo a specific commit in a shared branch.
- Maintain history integrity while removing unwanted changes.

### **Syntax**

```bash
git revert <commit_hash>
git revert <oldest_commit>..<newest_commit>
```

### **Examples**

1. **Revert a Single Commit**

   ```bash
   git revert abc123
   ```
   - This creates a new commit that undoes the changes introduced in commit `abc123`.

2. **Revert a Range of Commits**

   ```bash
   git revert abc123..def456
   ```
   - Reverts all commits from `abc123` (exclusive) to `def456` (inclusive).

3. **Revert Without Committing Immediately**

   ```bash
   git revert <commit_hash> -n
   ```
   - Stages the revert changes without committing them, allowing you to review or modify before committing.

### **Steps to Resolve Merge Conflicts During Revert**
1. If conflicts arise, resolve them manually in the files.
2. Stage the resolved files:
   ```bash
   git add <file_name>
   ```
3. Continue the revert process:
   ```bash
   git revert --continue
   ```

---

## **2. Git Reset**

### **Overview**

- `git reset` alters the state of your branch by moving the HEAD pointer to a specified commit.
- It modifies the commit history and can affect the staging area and working directory.

### **Use Cases**

- Undo commits in a local branch before sharing with others.
- Remove unwanted commits permanently from the history.

### **Modes of Reset**

1. **Soft Reset (`--soft`)**
   - Moves the HEAD pointer but keeps changes in the staging area.
   ```bash
   git reset --soft <commit_hash>
   ```

2. **Mixed Reset (`--mixed`)**
   - Moves the HEAD pointer and clears the staging area but keeps changes in the working directory.
   ```bash
   git reset --mixed <commit_hash>
   ```

3. **Hard Reset (`--hard`)**
   - Moves the HEAD pointer and clears both the staging area and the working directory.
   ```bash
   git reset --hard <commit_hash>
   ```

### **Syntax**

```bash
git reset [--soft|--mixed|--hard] <commit_hash>
```

### **Examples**

1. **Undo the Last Commit (Keep Changes in Staging Area)**

   ```bash
   git reset --soft HEAD~1
   ```

2. **Undo the Last Commit (Keep Changes in Working Directory)**

   ```bash
   git reset --mixed HEAD~1
   ```

3. **Undo the Last Commit (Remove Changes Completely)**

   ```bash
   git reset --hard HEAD~1
   ```

4. **Reset to a Specific Commit**

   ```bash
   git reset --hard abc123
   ```
   - Resets the branch to commit `abc123`, removing all commits after it.

### **Warning**

- **Hard reset** is destructive and cannot be undone unless you have backups (e.g., reflog).
- Avoid using `git reset` on shared branches.

---

## **Key Differences Between Git Revert and Git Reset**

| Feature                         | `git revert`                          | `git reset`                                  |
| ------------------------------- | ------------------------------------- | -------------------------------------------- |
| **Purpose**                     | Creates a new commit to undo changes. | Moves the HEAD pointer to a specific commit. |
| **History Alteration**          | Preserves history integrity.          | Rewrites commit history.                     |
| **Use on Shared Branches**      | Safe to use.                          | Not recommended.                             |
| **Impact on Commit History**    | Adds a new commit.                    | Removes existing commits.                    |
| **Impact on Working Directory** | Does not modify.                      | Depends on reset mode.                       |

---

## **When to Use Which Command**

1. **Use `git revert` when:**
   - You need to undo a specific commit without rewriting history.
   - You’re working on a shared branch.

2. **Use `git reset` when:**
   - You want to permanently remove unwanted commits from history.
   - You’re working on a local branch and can afford to rewrite history.

---

## **Best Practices**

1. Use `git revert` for shared branches to maintain collaboration integrity.
2. Use `git reset` cautiously, especially on branches that others are using.
3. Always create backups or check the reflog before performing destructive operations.

---

For further assistance, refer to the official [Git documentation](https://git-scm.com/doc).
