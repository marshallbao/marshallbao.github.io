# VUE



关于使用 nginx 代理 dist 打包文件夹

Vue SPA 在使用 **history 路由模式** 时，所有的入口都在 index.html 里，所以需要配置

```
# 配置 try_files
server {
  root /usr/share/nginx/html;
  
  location / {
    try_files $uri $uri/ /index.html;
  }
}

```

index.html 只是应用壳（shell），真正的应用逻辑在 JS 文件中，包括 Vue 框架运行时代码、组件代码、路由逻辑等，由浏览器的 JavaScript 引擎执行。

Vue 打包成静态资源后，本质是浏览器下载 JS 并执行，Vue 在浏览器中运行和渲染页面。

关于 SPA (Single Page Application)

SPA = HTML 只加载一次，后续页面切换靠 JS 动态渲染。