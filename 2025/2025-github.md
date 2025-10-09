## 🍒 github repos access
 
```bash
# For account1 (if you don’t already have one)
ssh-keygen -t ed25519 -C "williamjxj@gmail.com" -f ~/.ssh/id_ed25519_williamjxj

# For account2
ssh-keygen -t ed25519 -C "jxjwilliam@gmail.com" -f ~/.ssh/id_ed25519_jxjwilliam

eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519_williamjxj
ssh-add ~/.ssh/id_ed25519_jxjwilliam
```

then, Add Public Keys to GitHub Accounts
	•	Go to GitHub > Settings > SSH and GPG Keys > New SSH Key for each account.
	•	Copy the content of `~/.ssh/...pub`to respective accounts.

finally, Configure SSH Configurations ~/.ssh/config

## 🍑 several github repos

- awesome-claude-code
- shadcn-ui-mcp-server
- git-mcp
- **mcp_use**

## 🥭 

## 🍅

## 🍆

## 🍍

## 🥥

## 🥝

## 🍅

## 🍆
