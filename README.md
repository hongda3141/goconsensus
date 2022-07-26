## Go Consensus

This is a 'simulator', which should be run from within [hive](https://github.com/ethereum/hive). 

## Cross client testing

Testing implementations of Ethereum clients has been a difficult task. A standard testcase repository has been maintained,
but the actual test-execution has been left unspecified.

Each client has had to implement client-specific test-harnesses to perform the tests:

* The actual execution of the testcase, which includes creating necessary `prestate`.
* The verificaton of the testcase, which includes verifying various things, such as:
  * `poststate` state root
  * `lastblock` hash
  * Various `account`s, and `storage`.

## A generalised test

The [Ethereum Standard Testcases](https://github.com/ethereum/tests) testcases have now been transformed so that the bulk of all tests can be expressed as blocktests. The framework consists
of the following parts.

1. The testcase sources. These are basically the current testcases (testfillers). They contain the semantics of each test,
  and could e.g contain contract source code or individual transactions to be applied to a state. These tests should contain
  sufficient information for a human to be able to interpret the meaning of the test, and how to go about debugging a failed test.

2. The testcase artefacts. These are a transformation of the testcases (testfillers), onto the following form:

      `prestate` + `block(s)` => `postState`.

3. The execution of cross-client testing is then performed using Hive. The hive framework simply performs the following.

* For each client, for each testcase:
   * Create genesis by combining 'pre' and 'genesisBlockHeader'. // genesisBlockHeader跨链场景才使用吗
   * Create ruleset, according to testcase (Frontier, Homestead, Tangerine or Spurious), and set Hive `ENV` variables for the node
   * Instantiate hive-node (client), and write generated files to the node filesystem
   * The node will import blocks upon startup
   * Verify preconditions at block(0), using standard web3 api
   * Verify postconditions at block(n), using standard web3 api
   * Emit testcase subresult to Hive

This repo is responsible for souping up the tests, telling the framework to boot up new clients and then verify the results, and report back to hive, 

The Hive framework then outputs the results-report as a json-file, which can be packaged with a HTML viewer and client-logs for analysis of all test failures.  

The benefits of this approach is that there is no need for client-implementations to create a bespoke testing framework, as long as they
conform to the requirements of being a hive-node. Which are, basically:

- Ability to import blocks.
- Ability to configure ruleset via commandline/config.
- Standard web3 methods (getBlock).

Onboarding a client into Hive is pretty simple:

1. Create a Docker file (as minimal as possible) which installs the client
2. Create a shell script which can boot the client according to given `ENV` variables.
  * The `ENV` variables contain information about which ruleset to use (Frontier, Homestead, Tangerine etc). The script is also responsible for importing genesis and blocks from the filesystem.

## History

This repo is a rewrite of an older version which was implemented in python, and resides within the hive repository. 


来自的翻译
wenyanet.com/opensource/zh/5ff3d20baab356681355c0fd.html  

广义测试
在复仇标准的测试用例现在测试用例已经转变，使所有测试的体积可以表示为blocktests。该框架由以下部分组成。

测试用例的来源。这些基本上是当前的测试用例（测试填充器）。它们包含每个测试的语义，并且可以例如包含要应用于状态的合同源代码或单个交易。这些测试应包含足够的信息，以便人类能够解释测试的含义以及如何调试失败的测试。

测试用人工制品。这些是测试用例（测试填充器）的转换，格式如下：

prestate
+
block(s)
=>
postState
。
然后使用Hive执行跨客户端测试。蜂巢框架仅执行以下操作。

对于每个客户端，对于每个测试用例：
通过组合'pre'和'genesisBlockHeader'创建创世纪。
根据测试用例（Frontier，Homestead，Tangerine或Spurious）创建规则集，并
ENV
为节点设置Hive变量
实例化配置单元节点（客户端），并将生成的文件写入节点文件系统
该节点将在启动时导入块
使用标准Web3 API验证块（0）的前提条件
使用标准Web3 API在块（n）处验证后置条件
将测试用例发送给Hive
此仓库负责汇总测试，告诉框架启动新客户端，然后验证结果，并报告给蜂巢，

然后，Hive框架将结果报告输出为json文件，可以将其与HTML查看器和客户端日志打包在一起，以分析所有测试失败。

这种方法的好处是，只要满足成为蜂巢节点的要求，就不需要客户端实现来创建定制的测试框架。基本上是：

能够导入块。
能够通过命令行/配置配置规则集。
标准的web3方法（getBlock）。
将客户端加入Hive非常简单：

创建一个Docker文件（尽可能少）以安装客户端
创建一个shell脚本，该脚本可以根据给定的
ENV
变量引导客户端。
的
ENV
变量包含有关哪些规则集使用（前沿，宅地，橘子等）的信息。该脚本还负责从文件系统导入创始和块。
