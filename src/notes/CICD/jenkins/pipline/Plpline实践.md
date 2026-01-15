# Pipline 实践



### Pipline  环境变量

| 方式                  | 声明位置    | 作用域 | 可修改性                   |
| --------------------- | ----------- | ------ | -------------------------- |
| `environment`         | pipeline 内 | 全局   | 字符串常量，不易修改       |
| `script` 内 `def var` | stage 内    | 局部   | 局部只在 script 块内有效   |
| pipeline 外 `def var` | pipeline 外 | 类全局 | 可在各 stage script 中修改 |

全局变量和参数

```
# 环境变量模块，Jenkins Pipeline 的设计机制会把所有的 env 变量（包括内置变量如 BUILD_NUMBER 和您自定义的变量如 env.NODE_SELECTOR）自动注入到执行 sh 或 bat 命令的 Shell/Batch 进程的环境中。

environment

# 参数
params，params 变量存在于 Groovy/Pipeline 运行时环境中，它们不会自动像 env 变量那样被注入到 Shell 进程中。
def cluster = params.Cluster 


# 脚本方式
script{
	def selector = getNode(targetRegion)
	// NODE_SELECTOR 就成了全局变量
	env.NODE_SELECTOR = selector
}
# 全局函数
比如上面的 getNode 函数
@Library('your-shared-library-name') _  // 声明使用共享库

```

变量和参数的使用

```
# pipline 中使用，建议直接使用
echo env.ENV
echo params.Cluster
echo "xxx is ${env.ENV}"

def cluster = params.Cluster 
echo "Cluster is ${cluster}"
println("Cluster : ${cluster}")
println(cluster)

# Groovy 原生代码中使用
def Node = getnode(region)
def Node = getnode(env.region)
def Node = getnode(params.region)

# shell 中使用(无法直接使用 params)
sh 'echo ${evn.region}'
# 可以提升为 env 再使用,比如
def region = params.region
env.R = region
或者直接
env.R = params.region

```

环境变量、参数、局部变量的用户总结

| **变量类型**                 | **上下文**                    | **推荐引用方式**            | **示例**                                                     | **关键说明**                     |
| ---------------------------- | ----------------------------- | --------------------------- | ------------------------------------------------------------ | -------------------------------- |
| **环境变量** (`env`)         | Declarative Steps             | `"${env.VAR_NAME}"`         | `label "${env.NODE_LABEL}"`                                  | 必须使用 `${...}` 进行插值。     |
| **环境变量** (`env`)         | Groovy Script (`script {}`)   | `env.VAR_NAME`              | `def node = env.NODE_LABEL`                                  | 直接使用 `env` 对象。            |
| **环境变量** (`env`)         | Shell/Batch 命令 (`sh`/`bat`) | `$VAR_NAME`                 | `sh 'mvn build -DENV=$ENV'`                                  | 使用 Shell/OS 原生语法。         |
| **Pipeline 参数** (`params`) | Declarative Steps             | `"${params.PARAM_NAME}"`    | `when { expression { return "${params.DO_BUILD}" == 'true' } }` | 必须使用 `${...}` 进行插值。     |
| **Pipeline 参数** (`params`) | Groovy Script (`script {}`)   | `params.PARAM_NAME`         | `if (params.DEBUG) { ... }`                                  | 直接使用 `params` 对象。         |
| **Groovy 局部变量**          | Groovy Script (`script {}`)   | `${localVar}` 或 `localVar` | `echo "Found: ${fileCount}"`                                 | 仅在定义它的 `script` 块内可用。 |

script 中 shell 的使用

```
# 如果使用双引号，则是 Groovy 来解析了变量再插入到了shell 中，不推荐
# 如果使用单引号，Groovy 不做任何解析。
# shell 只能读到环境变量，读不到局部变量和参数！！

sh "echo ${NODE_SELECTOR}" (Groovy 插值)
sh "echo ${env.NODE_SELECTOR}" (Groovy 明确插值)

# 推荐使用单引号,(单引号 + Shell 原生解析)
sh 'echo $NODE_SELECTOR' 
sh 'echo ${env.NODE_SELECTOR}'
```

关于**Jenkins Pipeline (Groovy)** 和 **Shell (sh)** 之间的变量传递

```
# 环境变量 env.PP = pp
# shell 引用这个环境变量，两种写法
# groovy 遇到单引号，不处理，shell 直接读取环境变量
sh 'echo ${PP}'

# groovy 遇到双引号，进行处理，传给 shell 的其实是 echo PP,然后 shell 直接执行了
sh "echo ${PP}"  --> echo pp
sh "echo \"${PP}\"" --> echo "pp" --> echo pp 

# 如果是局部变量 def PP = "pp"
只能使用双引号，即让 groovy 来处理；因为 shell 只能读取环境变量，读不了 groovy 的局部变量
sh "echo ${PP}"

# 关于参数，shell 读不了 pipline/groovy 的参数，只能在 script 中升级为 局部/环境 变量才行；（最好是环境变量）
```



### Pipline  代码拉取

```
# 方法1 默认会拉取 master，此时如果你的主分支是 main 分支可能会报错，可以使用方法2
# 方法1
git url: env.GITREPO, credentialsId: env.GITREPO_CREDENTIALS
sh "git checkout ${env.gitCommit}"

# 方法2
git branch: 'main', credentialsId: env.GITREPO_CREDENTIALS, url: env.GITREPO 
sh "git checkout ${env.gitCommit}"
```



关于 Scritp 模块

处于 `script { ... }` 块内部时，您正在编写原生的 Groovy 代码。

```
# Jenkins Pipeline 中的 shell 命令无法直接引用 Groovy 脚本中的变量，你需要使用 ${} 语法来引用

script {
	sh "cat .env"
	// 如果 Params 变量有换行和空格的话，要使用单引号，'${Params}' ，防止有问题
	sh 'echo "${evn.Params}" > .env'
	sh 'cat .env'
}
# script 默认的环境是 Groovy 语言环境

```



关于动态参数逻辑

| 说法                                                         | 是否正确   | 说明                                              |
| ------------------------------------------------------------ | ---------- | ------------------------------------------------- |
| “Declarative Pipeline 不能定义 Active Choices 参数”          | ✅ 正确     | Declarative Pipeline 语法内不支持定义动态参数逻辑 |
| “Declarative Pipeline 不能与 Active Choices 一起使用”        | ❌ 错误     | 可以，通过 Job 层（UI 或 Job DSL）定义参数        |
| “Declarative Pipeline + Job DSL 可以完全代码化动态参数”      | ✅ 正确     | 推荐做法，企业标准方案                            |
| “Scripted Pipeline + Job DSL 才能代码化 Active Choices 参数” | ✅ 但不唯一 | Declarative 同样可以配合 DSL 使用                 |

Active Choices Plugin 如何在 Declarative pipline 中使用

```
// 混合式方案
// 1. 在 pipeline 块之外，使用 properties 步骤定义 Active Choices 参数
//    这部分使用了 Scripted Pipeline 语法
properties([
    // Active Choices Parameter
    [$class: 'ChoiceParameter', 
        name: 'MY_DYNAMIC_PARAM',
        choiceType: 'PT_SINGLE_SELECT',
        description: 'Dynamic choices loaded via Active Choices Plugin',
        script: [$class: 'GroovyScript', 
            script: [
                classpath: [], 
                sandbox: true,
                script: "return ['Value 1', 'Value 2', 'Value 3'].join('\\n')"
            ]
        ]
    ]
    // ... 你可以添加更多的 Active Choices 或 Reactive 参数
])

// 2. 然后，开始标准的 Declarative Pipeline
pipeline {
    agent any

    // 无需 parameters 或 options 块来定义这些参数

    stages {
        stage('Run Dynamic Build') {
            steps {
                // 在 steps 中，你仍然使用 params 变量访问参数
                echo "Selected Value: ${params.MY_DYNAMIC_PARAM}"
            }
        }
    }
}

//Job DSL + Declarative Pipeline (Seed Job 方案)
```





打印调试信息

```
steps{
	script{
		echo "debug info"
		printlin("debug info")
	}
	echo "debug info"
}
# 推荐 echo

```



### 构建结果

currentBuild.result 各种结果值 & 控制流场景

| 值             | 说明   | 执行效果                              | 典型用法                                                     |
| -------------- | ------ | ------------------------------------- | ------------------------------------------------------------ |
| SUCCESS (默认) | 成功   | Pipeline 正常执行完毕，显示绿色 ✅     | 一切正常，不需要手动设置                                     |
| FAILURE        | 失败   | 直接标记构建失败 ❌，后续阶段 不会执行 | 在遇到严重错误时：currentBuild.result = 'FAILURE'``error("原因") |
| ABORTED        | 已终止 | 构建会显示灰色 ⏹，类似用户点击 停止   | 在不满足条件时退出：currentBuild.result = 'ABORTED'``return  |
| UNSTABLE       | 不稳定 | 构建继续执行，但最后标记为黄色 ⚠️      | 单测失败、lint 不通过但不阻塞发布                            |
| NOT_BUILT      | 未构建 | Stage 被跳过，或 Job 没触发，显示灰色 | 一般由 when {} 或 skipStagesAfterUnstable() 导致             |



