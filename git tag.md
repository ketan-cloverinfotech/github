# What is git tag
Git tags are like bookmarks or labels that you attach to specific points in your project's history. They help you mark important moments, such as releases or versions, making it easier to reference them later. Think of tags as waypoints that highlight significant stages in your project's development.

# Git tag commands

| Task | Command |
| --- | --- |
| Create tag from current commit | `git tag <tag-name>` |
| Tag a specific commit | `git tag <tag-name> <commit-hash>` |
| Push a tag to GitHub | `git push origin <tag-name>` |
| Push all tags | `git push --tags` |
| Delete a tag locally | `git tag -d <tag-name>` |
| Delete a tag remotely | `git push origin --delete <tag-name>` |

* * *
# How tag is used for deployment in CI/CD in Jenkins

```bash

    stages {
        stage('Checkout') {
            steps {
                // Checkout the specific tag
                git branch: "${TAG_NAME}", url: 'https://github.com/yourusername/yourrepo.git'
            }
        }

```

Tags are a powerful way to mark specific points in your project history, such as release versions, and they work seamlessly across branches.

