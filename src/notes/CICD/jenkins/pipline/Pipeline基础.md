# Pipeline

### 概念

Pipeline，简而言之，就是一套运行于Jenkins上的工作流框架，将原本独立运行于单个或者多个节点的任务连接

起来，实现单个任务难以完成的复杂流程编排与可视化；

### 语法

Jenkins Pipeline 支持两种不同的语法：Declarative Pipeline 和 Scripted Pipeline。

Declarative Pipeline：Declarative Pipeline 提供了一种更结构化、可读性更强的声明式语法，通过声明式的方式

定义流水线的各个阶段和步骤。它使用 `pipeline` 块来定义整个流水线，而不需要像传统的 Jenkins Job 那样编

写复杂的 Groovy 脚本。Declarative Pipeline 还提供了一系列的内置步骤和指令，可以方便地定义流水线的逻辑

和控制流程。

Scripted Pipeline：Scripted Pipeline 允许你使用完整的 Groovy 脚本来编写流水线。它提供了更灵活、更自由的

编程能力，可以直接使用 Groovy 的语法和特性来编写复杂的流水线逻辑。Scripted Pipeline 通常适用于对自定

义逻辑和高级流程控制有更高需求的场景。

### Declarative Pipeline

关键字

```
pipeline

parameters

agent

stages
	stage
		step
			echo
			script
				sh
				echo

post
	success
	always
	failure
triggers 
```



示例

```
pipeline {

	options {
    	timeout(time: 30, unit: 'MINUTES')
    	buildDiscarder(logRotator(numToKeepStr: '10'))
    	disableConcurrentBuilds()
    	ansiColor('xterm')
    }

    parameters {
        text(name: 'IMAGES', defaultValue: 'imageName1', description: '镜像')
    }
    agent {
        label 'gcp-deploy-01-125'
    }
    environment {
        DOCKER_SOURCE_REGISTRY = 'repository.bianjie.ai'
        DOCKER_SOURCE_CREDENTIALS = 'nexus'
    }
    stages {
        stage('Docker') {
            steps {
                script {
                    def images = env.IMAGES.split(',')
                        for (def image : images) {
                        	echo ${image}
                        }
                    }
                }
            }
        }

    post {
        success {
            script {
                def images = env.IMAGES.split(',')
                    for (def image : images) {
                        sh "docker rmi ${DOCKER_SOURCE_REGISTRY}/adb/${image}" 
                        sh "docker rmi ${DOCKER_TARGET_REGISTRY}/adb/${image}"
                    }
            }

        }
    }
}
```



Scripted Pipeline





### 高级

注意区分 Jenkins Job 层 和 Pipeline 层：

| 层级            | 概念                     | 定义位置                                             | 生命周期     | 典型内容                                 |
| --------------- | ------------------------ | ---------------------------------------------------- | ------------ | ---------------------------------------- |
| **Job 层**      | Jenkins 的“构建任务定义” | Jenkins UI / Job DSL / Configuration as Code (JCasC) | 在构建启动前 | 参数定义、触发器、构建脚本路径、SCM 配置 |
| **Pipeline 层** | 实际运行的 CI/CD 脚本    | Jenkinsfile（Git 仓库中）                            | 在构建运行时 | stages、steps、agent、post 等逻辑        |



#### 关于 pipline/job as code 最佳实践

Seed Job DSL + Job DSL + pipline script from SCM

Seed Job DSL 放在 jenkins 服务器上

Job DSL 和 pipline script 放在 gitlab 上

Seed Job 是通用入口，只负责扫描指定目录 DSL 文件。新增 Job，只需要在 GitLab 添加 DSL 文件即可，不用

修改 Seed Job。



| 层级                     | 存放内容                                               | 责任                            | 存储位置             |
| ------------------------ | ------------------------------------------------------ | ------------------------------- | -------------------- |
| **Seed Job（固定模板）** | 执行 DSL、拉取 GitLab 脚本                             | Jenkins 入口、自动化 Job 生成器 | Jenkins 本地         |
| **Job DSL 文件**         | 定义 Jenkins Job 的元数据、参数（包括 Active Choices） | 负责“声明 Job 该怎么长什么样”   | GitLab（Infra 仓库） |
| **Jenkinsfile**          | 定义 CI/CD 流程逻辑（Build / Deploy / Test 等）        | 负责“Job 具体要干什么”          | GitLab（项目仓库）   |



关于 Pipline script 和 Pipline script from SCM

在使用 Declarative Pipline 时，如果只在 jenkins pipline 中声明参数，那么放在 Pipline script 中可以在 UI 中自

动配置；但是如果是放在 Pipline script from SCM 那么就不会自动配置





#### 使用 Shared Library

```
@Library('dyb-shared@master') _
node {
    dyb.buildAndPush(
        repo: params.GIT_REPO,
        commit: params.GIT_COMMIT,
        project: params.PROJECT,
        service: params.SERVICE
    )
}

```







注意点

```
1. pipeline 文件格式必须调整无误后 pipeline 才能进行调试，比如如何提供参数
```

