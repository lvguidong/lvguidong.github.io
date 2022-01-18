---
layout: post
title: 使用truffle构建第二个DAPP
tags: 
- eth
- truffle
- DApp
date: 2022-01-18 08:12 +0800
---

​	使用truffle快速搭建一个DApp

​	相关[链接](https://trufflesuite.com/boxes/webpack/index.html)

# 环境准备

  1. 安装 truffle

 ```
 npm install -g truffle
 ```

相关版本如下:

```
dong@DESKTOP-KS722GA:/mnt/g/dong/coin/eth/metabox$ truffle version
Truffle v5.4.28 (core: 5.4.28)
Solidity v0.5.16 (solc-js)
Node v14.18.3
Web3.js v1.5.3
```

 安装[ganache](https://lvguidong.github.io/2022/01/16/ganache%E4%BD%BF%E7%94%A8/)

# 初始化

​	新建一个目录并初始化：

```
lvguidong@DESKTOP-LBTKP51:/mnt/d/work/coin/eth/truffle$ mkdir eth-todo-list
lvguidong@DESKTOP-LBTKP51:/mnt/d/work/coin/eth/truffle$ cd eth-todo-list/
lvguidong@DESKTOP-LBTKP51:/mnt/d/work/coin/eth/truffle/eth-todo-list$ truffle init

Starting init...
================

> Copying project files to /mnt/d/work/coin/eth/truffle/eth-todo-list

Init successful, sweet!

Try our scaffold commands to get started:
  $ truffle create contract YourContractName # scaffold a contract
  $ truffle create test YourTestName         # scaffold a test

http://trufflesuite.com/docs

lvguidong@DESKTOP-LBTKP51:/mnt/d/work/coin/eth/truffle/eth-todo-list$
```

创建一个 `package.json` 来安装所需要的依赖

```
touch package.json
```

并添加一下内容到 `package.json`：

```
{
  "name": "eth-todo-list",
  "version": "1.0.0",
  "description": "Blockchain Todo List Powered By Ethereum",
  "main": "truffle-config.js",
  "directories": {
    "test": "test"
  },
  "scripts": {
    "dev": "lite-server",
    "test": "echo \"Error: no test specified\" && sexit 1"
  },
  "author": "gregory@dappuniversity.com",
  "license": "ISC",
  "devDependencies": {
    "bootstrap": "4.1.3",
    "chai": "^4.1.2",
    "chai-as-promised": "^7.1.1",
    "chai-bignumber": "^2.0.2",
    "lite-server": "^2.3.0",
    "nodemon": "^1.17.3",
    "truffle": "5.0.2",
    "truffle-contract": "3.0.6"
  }
}
```

安装：

```
npm install
```

```
lvguidong@DESKTOP-LBTKP51:/mnt/d/work/coin/eth/truffle/eth-todo-list$ npm install
npm WARN deprecated truffle-contract@3.0.6: WARNING: This package has been renamed to @truffle/contract.
npm WARN deprecated chokidar@2.1.8: Chokidar 2 will break on node v14+. Upgrade to chokidar 3 with 15x less dependencies.
npm WARN deprecated truffle-blockchain-utils@0.0.5: WARNING: This package has been renamed to @truffle/blockchain-utils.
npm WARN deprecated truffle-contract-schema@2.0.3: WARNING: This package has been renamed to @truffle/contract-schema.
npm WARN deprecated truffle-error@0.0.3: WARNING: This package has been renamed to @truffle/error.
npm WARN deprecated fsevents@1.2.13: fsevents 1 will break on node v14+ and could be using insecure binaries. Upgrade to fsevents 2.
npm WARN deprecated mkdirp@0.5.1: Legacy versions of mkdirp are no longer supported. Please update to mkdirp 1.x. (Note that the API surface has changed to use Promises in 1.x.)
npm WARN deprecated debug@4.1.1: Debug versions >=3.2.0 <3.2.7 || >=4 <4.3.1 have a low-severity ReDos regression when used in a Node.js environment. It is recommended you upgrade to 3.2.7 or 4.3.1. (https://github.com/visionmedia/debug/issues/797)
npm WARN deprecated source-map-resolve@0.5.3: See https://github.com/lydell/source-map-resolve#deprecated
npm WARN deprecated resolve-url@0.2.1: https://github.com/lydell/resolve-url#deprecated
npm WARN deprecated urix@0.1.0: Please see https://github.com/lydell/urix#deprecated
npm WARN deprecated source-map-url@0.4.1: See https://github.com/lydell/source-map-url#deprecated

> keccak@1.4.0 install /mnt/d/work/coin/eth/truffle/eth-todo-list/node_modules/keccak
> npm run rebuild || echo "Keccak bindings compilation fail. Pure JS implementation will be used."
.......
```

现在的目录结构是这样的：

<img src="https://github.com/lvguidong/lvguidong.github.io/blob/main/_posts/images/truffle-to-do-list-dir.png?raw=true" alt="block-min" style="zoom:80%;" />

1. `contracts目录`：是存放合约文件的目录，这里已经有一个部署我们的合约到链上的一个合约
2. `migrations目录`: 这是所有部署文件所在的位置。这些部署类似于其他需要迁移以更改数据库状态的 Web 开发框架。每当我们将智能合约部署到区块链时，我们都会更新区块链的状态，因此需要migration。
3. `node_modules目录`:  使用  `npm install` 安装的node依赖包。
4. `test目录`: 用来测试我们的智能合约。
5. `truffle-config.js`: 这是我们 Truffle 项目的主要配置文件，将在其中处理诸如网络配置之类的事情。



# 编写合约

​	添加合约文件：

```
$ touch ./contracts/TodoList.sol
```

先写下一个最简单的合约：

```
pragma solidity ^0.8.0;

contract TodoList {
    uint256 taskCount = 0;
}
```

先来编译一下试试：

```
turffle compile
```

```
lvguidong@DESKTOP-LBTKP51:/mnt/d/work/coin/eth/truffle/eth-todo-list$ truffle compile

Compiling your contracts...
===========================
> Compiling ./contracts/Migrations.sol
> Compiling ./contracts/TodoList.sol
> Compilation warnings encountered:

    Warning: SPDX license identifier not provided in source file. Before publishing, consider adding a comment containing "SPDX-License-Identifier: <SPDX-License>" to each source file. Use "SPDX-License-Identifier: UNLICENSED" for non-open-source code. Please see https://spdx.org for more information.
--> project:/contracts/TodoList.sol


> Artifacts written to /mnt/d/work/coin/eth/truffle/eth-todo-list/build/contracts
> Compiled successfully using:
   - solc: 0.8.11+commit.d7f03943.Emscripten.clang
```

看到`Compiled successfully`说明 编译成功了。

至此，你会看到目录下生成了一个build,并且该目录下生成了 `contracts/TodoList.json`和 `contracts/Migrations.json`。 

json文件是合约的 `ABI` 文件, 全称 `Abstract Binary Interface`,这个文件有许多的用处，这里说两点：

 	1. 它包含可在以太坊虚拟机（EVM）（即以太坊节点）上运行的 Solidity 智能合约代码的编译字节码版本。
 	2. 它包含智能合约功能的 JSON 表示，可以暴露给外部客户端，例如客户端 JavaScript 应用程序。

我们的下一个目标是访问 Truffle 控制台内的智能合约。但是，我们无法运行 Truffle 控制台，因为我们的应用程序尚未连接到我们在依赖项部分设置的 Ganache 个人区块链网络。要在 Truffle 控制台内与个人区块链网络上的智能合约对话，我们必须做几件事：

	1. 更新我们项目的配置文件以指定我们要连接的个人区块链网络（Ganache）。
 	2. 创建一个部署脚本，告诉 Truffle 如何将智能合约部署到个人区块链网络。
 	3. 运行新创建的迁移脚本，将智能合约部署到个人区块链网络。

首先，我们将更新项目配置文件以指定我们想要在第一部分中设置的个人区块链网络。找到文件 truffle-config.js 并粘贴以下代码：

```
module.exports = {
  networks: {
    development: {
      host: "127.0.0.1",
      port: 7545,
      network_id: "*" // Match any network id
    }
  },
  solc: {
    optimizer: {
      enabled: true,
      runs: 200
    }
  }
}
```

> 注意：这些应该与 Ganache 个人区块链网络提供的默认设置相匹配。如果您更改了 Ganache 设置页面中的任何设置，例如端口，这些设置应表示出来。



接下来，我们将在迁移目录中创建一个部署脚本，以将智能合约部署到个人区块链网络。在您的项目根目录中，从命令行创建一个新文件，如下所示：

```
$ touch migrations/2_deploy_contracts.js
```

​	让我解释一下这个文件的作用。每当我们创建新的智能合约时，我们都会更新区块链的状态。请记住，我说过区块链本质上是一个数据库。因此，每当我们永久更改它时，我们都必须将其从一种状态迁移到另一种状态。这与您可能在其他 Web 应用程序开发框架中执行的数据库迁移非常相似。

​	请注意，我们使用数字对迁移目录中的所有文件进行编号，以便 Truffle 知道执行它们的顺序。在这个新创建的迁移文件中，您可以使用以下代码来部署智能合约：

```
var TodoList = artifacts.require("./TodoList.sol");

module.exports = function (deployer) {
    deployer.deploy(TodoList);
};
```

​	首先，我们需要我们创建的合约，并将其分配给一个名为“TodoList”的变量。接下来，我们将其添加到已部署合约的清单中，以确保在我们运行迁移时部署它。现在让我们从命令行运行这个迁移脚本，如下所示：

```
$ truffle migrate
```

```
Compiling your contracts...
===========================
> Everything is up to date, there is nothing to compile.



Starting migrations...
======================
> Network name:    'development'
> Network id:      5777
> Block gas limit: 6721975 (0x6691b7)


1_initial_migration.js
======================

   Deploying 'Migrations'
   ----------------------
   > transaction hash:    0x39ae7d21c6f2cb3f0cb67550b8141dde74fe5db88bdd2bb2ba3a811f0a8f7767
   > Blocks: 0            Seconds: 0
   > contract address:    0xf757E69926E9Cc51e52879b1f4c6ef1FD584b6E2
   > block number:        1
   > block timestamp:     1642506645
   > account:             0x2B75BEfAA86578c07dEBD918524B0AB229E1cEfb
   > balance:             999.99502316
   > gas used:            248842 (0x3cc0a)
   > gas price:           20 gwei
   > value sent:          0 ETH
   > total cost:          0.00497684 ETH


   > Saving migration to chain.
   > Saving artifacts
   -------------------------------------
   > Total cost:          0.00497684 ETH


2_deploy_contracts.js
=====================

   Deploying 'TodoList'
   --------------------
   > transaction hash:    0x6a6ff0c340426149b262175b08726e82d0d0697caecab4e0056e10038e1e077a
   > Blocks: 0            Seconds: 0
   > contract address:    0xDec445B6a80b469Fc151B3e9dbF46206bbaFF359
   > block number:        3
   > block timestamp:     1642506647
   > account:             0x2B75BEfAA86578c07dEBD918524B0AB229E1cEfb
   > balance:             999.99281442
   > gas used:            67924 (0x10954)
   > gas price:           20 gwei
   > value sent:          0 ETH
   > total cost:          0.00135848 ETH


   > Saving migration to chain.
   > Saving artifacts
   -------------------------------------
   > Total cost:          0.00135848 ETH


Summary
=======
> Total deployments:   2
> Final cost:          0.00633532 ETH
```



既然我们已经成功将智能合约迁移到个人区块链网络上，那么让我们打开控制台与智能合约进行交互。您可以像这样从命令行打开 truffle 控制台：

```
$ truffle console
```

现在我们在控制台中，让我们获取我们部署的智能合约的一个实例，看看我们是否可以从合约中读取 taskCount。从控制台运行以下代码：

```
todoList = await TodoList.deployed()
```

​	这里 TodoList 是我们在迁移文件中创建的变量的名称。我们使用 deploy() 函数检索了已部署的合约实例，并将其分配给 todoList。另外，请注意 await 关键字的使用。我们必须以异步方式与区块链交互。因此，JavaScript 是与区块链智能合约进行客户端交互的绝佳选择。在 JavaScript 中有几种处理异步操作的策略。最流行的方式之一是我在这里选择的 async/await 模式。 Truffle 最近在 Truffle 控制台中发布了对此的支持。



# 完善合约

```solidity
pragma solidity ^0.8.0;

contract TodoList {
    uint256 public taskCount = 0;

    constructor() public {
        createTask("Check out dappuniversity.com");
    }

    struct Task {
        uint256 id;
        string content;
        bool completed;
    }

    mapping(uint256 => Task) public tasks;

    function createTask(string memory _content) public {
        taskCount++;
        tasks[taskCount] = Task(taskCount, _content, false);
    }
}

```

​	现在让我们将这个智能合约部署到区块链上。为此，我们必须备份一份部署代码。请记住，智能合约代码是不可变的！它不能改变。因此，每当我们进行代码更改时，我们都必须创建一个新的智能合约。幸运的是，Truffle 提供了一个快捷方式来帮助解决这个问题。我们可以像这样重新运行迁移：

```
$ truffle migrate --reset
```

```
Compiling your contracts...
===========================
> Compiling ./contracts/TodoList.sol
> Compilation warnings encountered:

    Warning: SPDX license identifier not provided in source file. Before publishing, consider adding a comment containing "SPDX-License-Identifier: <SPDX-License>" to each source file. Use "SPDX-License-Identifier: UNLICENSED" for non-open-source code. Please see https://spdx.org for more information.
--> project:/contracts/TodoList.sol

,Warning: Visibility for constructor is ignored. If you want the contract to be non-deployable, making it "abstract" is sufficient.
 --> project:/contracts/TodoList.sol:6:5:
  |
6 |     constructor() public {
  |     ^ (Relevant source part starts here and spans across multiple lines).


> Artifacts written to /mnt/d/work/coin/eth/truffle/eth-todo-list/build/contracts
> Compiled successfully using:
   - solc: 0.8.11+commit.d7f03943.Emscripten.clang



Starting migrations...
======================
> Network name:    'development'
> Network id:      5777
> Block gas limit: 6721975 (0x6691b7)


1_initial_migration.js
======================

   Replacing 'Migrations'
   ----------------------
   > transaction hash:    0x34865a640d3f2e4832c7c8d6335f4784eb3fbb39676a624585b28c21f1f9aa9d
   > Blocks: 0            Seconds: 0
   > contract address:    0xD337Bc5A8c547Da2159532D9b17c6fe2F1431Eb6
   > block number:        5
   > block timestamp:     1642507391
   > account:             0x2B75BEfAA86578c07dEBD918524B0AB229E1cEfb
   > balance:             999.98728732
   > gas used:            248842 (0x3cc0a)
   > gas price:           20 gwei
   > value sent:          0 ETH
   > total cost:          0.00497684 ETH


   > Saving migration to chain.
   > Saving artifacts
   -------------------------------------
   > Total cost:          0.00497684 ETH


2_deploy_contracts.js
=====================

   Replacing 'TodoList'
   --------------------
   > transaction hash:    0x14050c016c146e503adbfda29a27f1ceae7d92632ecbfefb39d28a7285d639c6
   > Blocks: 0            Seconds: 0
   > contract address:    0x932FEF4722E48Deba4B691C6D13161f779642B24
   > block number:        7
   > block timestamp:     1642507393
   > account:             0x2B75BEfAA86578c07dEBD918524B0AB229E1cEfb
   > balance:             999.97923276
   > gas used:            360215 (0x57f17)
   > gas price:           20 gwei
   > value sent:          0 ETH
   > total cost:          0.0072043 ETH


   > Saving migration to chain.
   > Saving artifacts
   -------------------------------------
   > Total cost:           0.0072043 ETH


Summary
=======
> Total deployments:   2
> Final cost:          0.01218114 ETH
```



​	成功部署了。

​	现在我们已经将此智能合约迁移到了区块链上，让我们创建客户端代码以与待办事项列表智能合约进行交互。您需要为您的项目创建以下文件：

- bs-config.json
- src/index.html
- src/app.js

```json
bs-config.json
{
  "server": {
    "baseDir": [
      "./src",
      "./build/contracts"
    ],
    "routes": {
      "/vendor": "./node_modules"
    }
  }
}
```

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <!-- The above 3 meta tags *must* come first in the head; any other head content must come *after* these tags -->
    <title>Dapp University | Todo List</title>

    <!-- Bootstrap -->
    <link href="vendor/bootstrap/dist/css/bootstrap.min.css" rel="stylesheet">

    <!-- HTML5 shim and Respond.js for IE8 support of HTML5 elements and media queries -->
    <!-- WARNING: Respond.js doesn't work if you view the page via file:// -->
    <!--[if lt IE 9]>
      <script src="https://oss.maxcdn.com/html5shiv/3.7.3/html5shiv.min.js"></script>
      <script src="https://oss.maxcdn.com/respond/1.4.2/respond.min.js"></script>
    <![endif]-->

    <style>
        main {
            margin-top: 60px;
        }

        #content {
            display: none;
        }

        form {
            width: 350px;
            margin-bottom: 10px;
        }

        ul {
            margin-bottom: 0px;
        }

        #completedTaskList .content {
            color: grey;
            text-decoration: line-through;
        }
    </style>
</head>

<body>
    <nav class="navbar navbar-dark fixed-top bg-dark flex-md-nowrap p-0 shadow">
        <a class="navbar-brand col-sm-3 col-md-2 mr-0" href="http://www.dappuniversity.com/free-download"
            target="_blank">Dapp University | Todo List</a>
        <ul class="navbar-nav px-3">
            <li class="nav-item text-nowrap d-none d-sm-none d-sm-block">
                <small><a class="nav-link" href="#"><span id="account"></span></a></small>
            </li>
        </ul>
    </nav>
    <div class="container-fluid">
        <div class="row">
            <main role="main" class="col-lg-12 d-flex justify-content-center">
                <div id="loader" class="text-center">
                    <p class="text-center">Loading...</p>
                </div>
                <div id="content">
                    <form onSubmit="App.createTask(); return false;">
                        <input id="newTask" type="text" class="form-control" placeholder="Add task..." required>
                        <input type="submit" hidden="">
                    </form>
                    <ul id="taskList" class="list-unstyled">
                        <div class="taskTemplate" class="checkbox" style="display: none">
                            <label>
                                <input type="checkbox" />
                                <span class="content">Task content goes here...</span>
                            </label>
                        </div>
                    </ul>
                    <ul id="completedTaskList" class="list-unstyled">
                    </ul>
                </div>
            </main>
        </div>
    </div>

    <!-- jQuery (necessary for Bootstrap's JavaScript plugins) -->
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/1.12.4/jquery.min.js"></script>
    <!-- Include all compiled plugins (below), or include individual files as needed -->
    <script src="vendor/bootstrap/dist/js/bootstrap.min.js"></script>
    <script src="vendor/truffle-contract/dist/truffle-contract.js"></script>
    <script src="app.js"></script>
</body>

</html>
```

```js
App = {
  loading: false,
  contracts: {},

  load: async () => {
    await App.loadWeb3()
    await App.loadAccount()
    await App.loadContract()
    await App.render()
  },

  // https://medium.com/metamask/https-medium-com-metamask-breaking-change-injecting-web3-7722797916a8
  loadWeb3: async () => {
    if (typeof web3 !== 'undefined') {
      App.web3Provider = web3.currentProvider
      web3 = new Web3(web3.currentProvider)
    } else {
      window.alert("Please connect to Metamask.")
    }
    // Modern dapp browsers...
    if (window.ethereum) {
      window.web3 = new Web3(ethereum)
      try {
        // Request account access if needed
        await ethereum.enable()
        // Acccounts now exposed
        web3.eth.sendTransaction({/* ... */})
      } catch (error) {
        // User denied account access...
      }
    }
    // Legacy dapp browsers...
    else if (window.web3) {
      App.web3Provider = web3.currentProvider
      window.web3 = new Web3(web3.currentProvider)
      // Acccounts always exposed
      web3.eth.sendTransaction({/* ... */})
    }
    // Non-dapp browsers...
    else {
      console.log('Non-Ethereum browser detected. You should consider trying MetaMask!')
    }
  },

  loadAccount: async () => {
    // Set the current blockchain account
    App.account = web3.eth.accounts[0]
  },

  loadContract: async () => {
    // Create a JavaScript version of the smart contract
    const todoList = await $.getJSON('TodoList.json')
    App.contracts.TodoList = TruffleContract(todoList)
    App.contracts.TodoList.setProvider(App.web3Provider)

    // Hydrate the smart contract with values from the blockchain
    App.todoList = await App.contracts.TodoList.deployed()
  },

  render: async () => {
    // Prevent double render
    if (App.loading) {
      return
    }

    // Update app loading state
    App.setLoading(true)

    // Render Account
    $('#account').html(App.account)

    // Render Tasks
    await App.renderTasks()

    // Update loading state
    App.setLoading(false)
  },

  renderTasks: async () => {
    // Load the total task count from the blockchain
    const taskCount = await App.todoList.taskCount()
    const $taskTemplate = $('.taskTemplate')

    // Render out each task with a new task template
    for (var i = 1; i <= taskCount; i++) {
      // Fetch the task data from the blockchain
      const task = await App.todoList.tasks(i)
      const taskId = task[0].toNumber()
      const taskContent = task[1]
      const taskCompleted = task[2]

      // Create the html for the task
      const $newTaskTemplate = $taskTemplate.clone()
      $newTaskTemplate.find('.content').html(taskContent)
      $newTaskTemplate.find('input')
                      .prop('name', taskId)
                      .prop('checked', taskCompleted)
                      .on('click', App.toggleCompleted)

      // Put the task in the correct list
      if (taskCompleted) {
        $('#completedTaskList').append($newTaskTemplate)
      } else {
        $('#taskList').append($newTaskTemplate)
      }

      // Show the task
      $newTaskTemplate.show()
    }
  },

  createTask: async () => {
    App.setLoading(true)
    const content = $('#newTask').val()
    await App.todoList.createTask(content)
    window.location.reload()
  },

  toggleCompleted: async (e) => {
    App.setLoading(true)
    const taskId = e.target.name
    await App.todoList.toggleCompleted(taskId)
    window.location.reload()
  },

  setLoading: (boolean) => {
    App.loading = boolean
    const loader = $('#loader')
    const content = $('#content')
    if (boolean) {
      loader.show()
      content.hide()
    } else {
      loader.hide()
      content.show()
    }
  }
}

$(() => {
  $(window).load(() => {
    App.load()
  })
})
```

运行：

```
npm run dev
```





