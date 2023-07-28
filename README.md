# 背景:

在团队协作中，一个比较大的痛点就是代码提交的信息不够规范。由于个人的不同习惯所导致的不统一的提交信息使得代码回溯极为困难。因此，在项目开发过程中，统一代码提交信息变得非常重要。

规范提交信息有下面这些好处：
- 在阅读代码时，我们可以通过 commit message 了解到作者编写某行代码时的背景；
- 回溯 bug 时可以通过搜索 commit message 快速定位相关的提交记录；
- 最重要的是养成好的习惯，提高团队和个人工作效率；
- 洁癖/强迫症 爱好者的福音😊

# 工具链:

接下来，我将介绍几款工具，使得我们更方便的规范我们的commit message

1、**`commitizen`**: 帮助我们编写commit messages的工具，并且可以替代git commit命令(通过`commitizen`提供的cli)

2、**`commitlint`**: 校验commit message的工具

3、**`husky`**: husky可以让我们向项目中方便添加git hooks

4、**`lint-staged`**: 当我们运行eslint或stylelint的命令时，只会检查我们通过git add添加到暂存区的文件，可以避免我们每次检查都把整个项目的代码都检查一遍

除此之外，我还想介绍一下关于 **`适配器(adapter)`** 的概念。什么是适配器，其实简单的说，就是一个 **更换 commitizen 命令行工具的交互方式插件。**

适配器有很多，可以进行不同的选择,比如cz-conventional-changelog, git-cz, cz-git…

如果使用不同的适配器？

修改 `package.json` 添加 `config` 指定使用的适配器：
```json
{
    "config": {
        "commitizen": {
            "path": "git-cz"    
        }
    }
}
```


# 原理：

由上面的这些工具，我们就可以完成一套commit标准化流程:

1、使用commitizen编写规范的commit messages
2、通过husky，我们可以更方便的使用git hooks，我们可以通过 git hook 在 pre-commit 进行 eslint，在 commit-msg 阶段通过commitlint进行commit校验


通过上面的工具和原理，你大概了解到一整套commitlint流程是如何工作的了🤗。

# 详细步骤：

下面是具体的步骤：

#### 1、下载commitizen
`sudo pip3 install -U Commitizen`
`npm install -g commitizen`
或者
`brew install commitizen (Mac os)`

如果需要适配器(adapter),可以下载并在 package.json 添加 config 指定使用的适配器，如果不需要可以跳过。

简单的推荐几个适配器:

[cz-git](https://cz-git.qbb.sh/)
[git-cz](https://www.npmjs.com/package/git-cz)
[cz-conventional-changelog](https://github.com/commitizen/cz-conventional-changelog)

#### 2、配置commitizen
在根目录创建`.cz.json` 或 `cz.json` 或 `.cz.yaml` 或 `cz.yaml` 这些文件作为commitizen的配置文件。详细参考commitizen configuration

#### 3、本地安装@commitlint/cli，@commitlint/config-conventional 

 - @commitlint/cli 【命令行工具】
 - @commitlint/config-conventional 【校验规则】符合 Angular团队规范（不同于代码规范），当然还有其它规范。

```bash
# Install and configure if needed
npm install --save-dev @commitlint/{cli,config-conventional}
# For Windows:
npm install --save-dev @commitlint/config-conventional @commitlint/cli
```

#### 4、配置commitlint.config.js

支持`commitlint.config.js` | `.commitlintrc.js` | `.commitlintrc` | `.commitlintrc.json` | `.commitlintrc.yml` 也支持在package.json中`commitlint`字段

```javascript
module.exports = { extends: ['@commitlint/config-conventional'] };
```

其他配置详见: commitlint configuration


#### 5、本地安装husky, lint-staged
```bash
npm install --save-dev husky lint-staged
```

#### 6、配置husky, lint-staged

eg：`package.json`中

```json
{
"lint-staged": {
    "src/**/*.{js,jsx,ts,tsx}": [
      "yarn lint",
      "yarn format"
    ]
  },
  "husky": {
    "hooks": {
      "pre-commit": "lint-staged"
    }
  },
 }
```
也可以在`.huskyrc`中：
```json
{
    "hooks": {
      "pre-commit": "lint-staged"
    }
}
```
#### 7、创建`.husky` **文件夹(是文件夹!!!)** 并在该文件夹中创建`commit-msg`和`pre-commit`文件

commit-msg文件内容:
```bash
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"

yarn commitlint --edit ${1}
```
pre-commit文件内容:
```bash
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"

yarn lint-staged
```

#### 8、删除node_modules, 重新yarn，修改文件，测试




