---
Author:
  - Xinyang YU
Author Profile:
  - https://linkedin.com/in/xinyang-yu
tags:
  - js
Creation Date: 2023-12-15, 20:01
Last Date: 2024-01-10T19:04:45+08:00
References: 
title: Confused About Setting Up a JavaScript Project? This Guide Has Your Back!
---
## Abstract
---
- This is how a 10X developer starts his/her JS project

Setup Steps:
- [ ] [[#Install NVM]]
- [ ] [[#Install Code Quality Assurance Tools]]


## Install NVM
---
- Stands for *Node Version Manager*
- You can install [here](https://github.com/nvm-sh/nvm#install--update-script)
- Includes [[Node.js]], [[#NPM]] and [[#NPX]]
### NPM
- Tool to install the *JS Modules*, refer to [[MacOs Setup]] for set up details
- Obtain the absolute path to global node modules 
```bash
npm root -g

# We can take advantage of this to copy certain runtime dep to the production build
cp -R $(npm root -g)/dd-trace ./traced-deps/node_modules
```

- Manage `package.json` using [npm pkg](https://docs.npmjs.com/cli/v7/commands/npm-pkg)
```shell
npm pkg get [<field> [.<subfield> ...]] 
npm pkg set <field>=<value> [.<subfield>=<value> ...] 
npm pkg delete <field> [.<subfield> ...]

# Example, set a cript called 'pre-commit'
npm pkg set scripts.pre-commit="npx prettier . --write && npx oxlint"
```
### NPX
- Stands for Node Package eXecute
- A *package runner tool* that comes bundled with [[#NPM]]
- Allows you to execute any package from the npm registry without having to install it globally or locally using NPM which may pollute your local or global *node_modules directory*

## Install and Config Code Quality Assurance Tools
---
- [[Code Quality Assurance Tools|What are code quality assurance tools]]

2 code quality assurance tools:
- [ ] [[#oxlint]]
- [ ] [[#prettier]]

2 git automation tools to run the quality assurance tools:
- [ ] [[#husky]]
- [ ] [[#lint-staged]]

Lastly:
- [ ] [[#Integrate All Tools]] 
### oxlint
- A JS [[Code Quality Assurance Tools#Linter]]
- Setup script
```shell
#!/bin/bash
npm add -D oxlint # Add oxlint to package.json

# Specify path/file to ignore
# In this case, we are ignoring all files in node_modules
touch .eslintignore
echo node_modules > .eslintignore
```

- Run the linter with `npx oxlint`
- Refer to [oxlint official docs](https://oxc-project.github.io/docs/guide/usage/linter.html) for more configuration information

>[!info]
>At the current stage, oxlint is **not intended to fully replace [ESLint](https://eslint.org/)**; it serves as an enhancement when ESLint's slowness becomes a bottleneck in your workflow.
>
>We recommend running oxlint before ESLint in your lint-staged or CI setup for a quicker feedback loop, considering it only takes a few seconds to run on large codebases.
### prettier
- A [[Code Quality Assurance Tools#Formatter]]
- Setup script
```bash
#!/bin/bash
npm install --save-dev --save-exact prettier # Add oxlint to package.json

touch .prettierignore # Specify path/file to ignore
touch .prettierrc # Config prettier
```

- Run `npx prettier . --write` to format all the files in current directory recursively
- Refer to [prettier official docs](https://prettier.io/docs/en/install.html) for more configuration information

### husky
- A tool to auto run the [[#prettier]] & [[#oxlint]] when making a git commit  
- Configuration details see [[Git Hook#Husky|here]]

### lint-staged
- A tool usually integrates with [[#husky]] to ensure the [[Code Quality Assurance Tools]] like [[#prettier]] & [[#oxlint]] only run on current staged files to fasten the process of each commit. Since only the staged files are going to be pushed to the database, no point to run the tools on all files to slow the current commit

### Integrate All Tools
```bash title=".husky/pre-commit" {4}
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"

npm run pre-commit
```
- The idea is to have [[#husky]] to trigger pre-commit script which runs the pre-commit npm script

```json title="package.json" {11} {27-31}
{
  "name": "authentication",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "node ./dbInit.js && node index.js",
    "dev": "rm db.json && node ./dbInit.js && nodemon index.js",
    "prepare": "husky install",
    "pre-commit": "lint-staged --verbose"
  },
  "author": "xy241",
  "license": "ISC",
  "type": "module",
  "dependencies": {
    "express": "^4.18.2",
    "lowdb": "^7.0.1"
  },
  "devDependencies": {
    "husky": "^8.0.0",
    "lint-staged": "^15.2.0",
    "nodemon": "^3.0.2",
    "oxlint": "^0.1.2",
    "prettier": "3.1.1"
  },
  "lint-staged": {
    "*": [
      "./codeAssurance.sh"
    ]
  }
}
```
- The pre-commit npm script triggers the [[#lint-staged]], `--verbose`  means we want to see the output of the code quality assurance tools, line 11
- The `lint-staged` configuration is specified at line 27-31
- The `lint-staged` runs the code quality assurance tools on all the staged files via `codeAssurance.sh`

```bash title="codeAssurance.sh" {2} {7} {10}
#!/bin/bash
set -e

echo -e "\033[1m=== Start of Code Quality Assurance Tools ===\n \033[0m"

echo -e "\033[1;32m🔎 Perform oxlint Linter: \033[0m"
npx oxlint

echo -e "\n\033[1;32m💅 Perform Prettier Formatting: \033[0m"
npx prettier . --write

echo -e "\n\033[1;32m👍 Code Quality Assurance Tools Passed! \033[0m"
```
- The `codeAssurance.sh` basically run all the code quality assurance tools we configured above
- Remember to `set -e` at line 2, refer to [[Bash Script#Bash Script Exit Code]] for more details
