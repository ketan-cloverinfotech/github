## Step 1: Install github cli
```
# as a sudo user
type -p curl >/dev/null || sudo apt-get update && sudo apt-get install -y curl
curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg \
 | sudo dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg
sudo chmod go+r /usr/share/keyrings/githubcli-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" \
 | sudo tee /etc/apt/sources.list.d/github-cli.list >/dev/null
sudo apt-get update
sudo apt-get install -y gh
gh --version
```
## Step 2: Login to github 
```
gh auth login --hostname github.com
```
## Step 3: Run following script for getting user roles and permission in following format
**User	Role	Repo	Visibility	Permission**

```
#!/usr/bin/env bash
set -euo pipefail

# ====== CONFIG ======
ORG="Clover-jio"                 # <-- your org
INCLUDE_OUTSIDE_COLLABS="false"  # "true" to include outside collaborators
OUT="${ORG}_all-user-repo-permissions.csv"
# ====================

command -v gh >/dev/null || { echo "gh not installed"; exit 1; }
echo "Token user: $(gh api /user -q .login)"
echo "Org       : $(gh api /orgs/$ORG -q .login)"

# 1) Collect users
echo "Collecting users..."
mapfile -t MEMBERS < <(gh api "/orgs/$ORG/members" --paginate -q '.[].login')
if [[ "$INCLUDE_OUTSIDE_COLLABS" == "true" ]]; then
  mapfile -t OUTSIDERS < <(gh api "/orgs/$ORG/outside_collaborators" --paginate -q '.[].login' 2>/dev/null || true)
else
  OUTSIDERS=()
fi

# De-dupe users, preserve order
declare -A SEEN=()
USERS=()
for u in "${MEMBERS[@]}" "${OUTSIDERS[@]}"; do
  if [[ -n "${SEEN[$u]:-}" ]]; then continue; fi
  SEEN[$u]=1; USERS+=("$u")
done

# 2) Pull user roles (owner/member/outside)
declare -A ROLE
for u in "${USERS[@]}"; do
  r=$(gh api "/orgs/$ORG/memberships/$u" -q .role 2>/dev/null || true)
  if [[ -z "$r" && " ${OUTSIDERS[*]} " == *" $u "* ]]; then
    r="outside_collaborator"
  fi
  ROLE[$u]="${r:-member}"
done

# 3) Collect repos + visibility (cache for speed)
echo "Collecting repos..."
mapfile -t REPOS < <(gh api "/orgs/$ORG/repos" --paginate -q '.[].name')
declare -A VIS
for r in "${REPOS[@]}"; do
  pv=$(gh api "/repos/$ORG/$r" -q '.private' 2>/dev/null || echo "")
  if [[ "$pv" == "true" ]]; then VIS[$r]="Private"
  elif [[ "$pv" == "false" ]]; then VIS[$r]="Public"
  else VIS[$r]="" ; fi
done

# 4) Emit CSV header
echo "user,role,repo,visibility,permission" > "$OUT"

# 5) For each user x repo: effective permission (empty means no access)
echo "Querying permissions (this may take a bit for many repos/users)..."
for u in "${USERS[@]}"; do
  for r in "${REPOS[@]}"; do
    perm=$(gh api "/repos/$ORG/$r/collaborators/$u/permission" -q .permission 2>/dev/null || true)
    if [[ -n "$perm" ]]; then
      echo "$u,${ROLE[$u]},$ORG/$r,${VIS[$r]},$perm" >> "$OUT"
    fi
    sleep 0.05  # be gentle on rate limits
  done
done

echo "Done. $(wc -l < "$OUT") rows written to $OUT"

```
