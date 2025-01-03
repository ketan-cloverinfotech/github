**Step 1: Create SSH-key**
```bash
ssh-keygen -t rsa -b 2048 -f ~/.ssh/azure
```

**Step 2: add your SSH key to your config file**
```bash
nano ~/.ssh/config
```
**Step 3: add host to config file**
```bash
# GitHub Configuration
Host github-azure
    HostName github.com
    User git
    IdentityFile ~/.ssh/azure

# Azure DevOps Configuration
Host azure-devops
    HostName ssh.dev.azure.com
    User git
    IdentityFile ~/.ssh/azure
```
**Step 4 : Make sure the ssh-agent is running and your key is added.**
```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/azure
```