# Pipline 实践



### Pipline 环境变量

| 方式                  | 声明位置    | 作用域 | 可修改性                   |
| --------------------- | ----------- | ------ | -------------------------- |
| `environment`         | pipeline 内 | 全局   | 字符串常量，不易修改       |
| `script` 内 `def var` | stage 内    | 局部   | 局部只在 script 块内有效   |
| pipeline 外 `def var` | pipeline 外 | 类全局 | 可在各 stage script 中修改 |

### Pipline 代码拉取

```
# 方法1 默认会拉取 master，此时如果你的主分支是 main 分支可能会报错，可以使用方法2
# 方法1
git url: env.GITREPO, credentialsId: env.GITREPO_CREDENTIALS
sh "git checkout ${env.gitCommit}"

# 方法2
git branch: 'main', credentialsId: env.GITREPO_CREDENTIALS, url: env.GITREPO 
sh "git checkout ${env.gitCommit}"
```



script

```
# Jenkins Pipeline 中的 shell 命令无法直接引用 Groovy 脚本中的变量，你需要使用 ${} 语法来引用

script {
	sh "cat .env"
	// 如果 Params 变量有换行和空格的话，要使用单引号，'${Params}' ，防止有问题
	sh "echo '${evn.Params}' > .env"
	sh "cat .env"
}
# script 默认的环境是 Groovy 语言环境

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



