---
title: Create a symbolic link at system node path to default nvm node path
date: "2018-01-15"
category: 'bash'
tags:
    - bash
    - Sublime
---

Today I got an issue when trying to pretify HTML code in Sublime editor. In case you wonder why I am still using Sublime because it's incredibly fast and I am still using Sublime to do some quick formating code. Here is the error from HTMLPrettify add-on:

![HTMLPrettify add-on error](https://user-images.githubusercontent.com/138803/34925052-01158440-f9fb-11e7-9161-13c10225ce35.png)

There error is quite straitghtforward. You can resolve that error by clikcing Ok button and HTMLPrettify config file will be open automatically for you and then you can change node path basing in your OS.

![HTMLPrettify add-on config file](https://user-images.githubusercontent.com/138803/34925053-014878aa-f9fb-11e7-9054-e7cb1e710808.png)

However, I am using [nvm](https://github.com/creationix/nvm) to mange node version so I have my default node version in different path (ex: `/Users/<user-name>/.nvm/versions/node/v8.9.3/bin/node`) and I don't want to change default config of HTMLPrettify add-on as well. Hence, I have to create a symbolic link between system node path and nvm default node path. Here is a command to do that:

```bash
# command structure: ln -s <nvm-node-path> <system-node-path>
# example:
ln -s /Users/tthanhdang/.nvm/versions/node/v8.9.3/bin/node /usr/local/bin/node
```

The other benefit of keeping system node path (`/usr/local/bin/node`) in sync with nvm default node path is because some apps (add-ons of Visual Studio Code, Sublime…) needs to read system node (`/usr/local/bin/node`) by default and I want change/upgrade node versions of those apps quickly by `nvm` command. 

You can write a bash function helper to keep system node path and nvm default node path in sync quickly whenever you change node version by `nvm` command.


```bash
##############################################################
# Update system node path to nvm default node path
##############################################################
update_system_node_path() {
    SYSTEM_NODE_PATH="/usr/local/bin/node"
    NVM_DEFAULT_NODE_PATH="$(nvm which default)"
    ln -Ffsv "$NVM_DEFAULT_NODE_PATH" "$SYSTEM_NODE_PATH"
}
```
