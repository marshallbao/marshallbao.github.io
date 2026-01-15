---
home: true
title: 首页
icon: home
heroImage: /logo.png
heroImageDark: /logo-dark.png
heroImageStyle: "width: 200px; height: 200px;"

heroText: DevOpsNotes
tagline: 个人运维技术笔记与经验分享

actions:
  - text: 开始阅读
    link: /notes/
    type: primary
  - text: 关于我
    link: /person
    type: secondary

features:
  - title: 🚀 DevOps 实践
    details: 持续集成/持续部署、自动化运维、容器化部署等实践经验
    link: /notes/CICD/
    icon: rocket

  - title: 🐳 容器技术
    details: Docker、Kubernetes、Helm等
    link: /notes/容器/
    icon: docker

  - title: 🐧 Linux 运维
    details: 系统管理、软件服务、文本处理、网络配置等
    link: /notes/Linux/
    icon: linux

  - title: 🗄️ 数据库管理
    details: MySQL、PostgreSQL、MongoDB、etcd等数据库运维
    link: /notes/DB/
    icon: database

  - title: 🔧 中间件运维
    details: Kafka、RabbitMQ等中间件
    link: /notes/中间件/
    icon: tools

footer: false
---

## 🎯 网站介绍

这是一个专注于 **DevOps** 和 **运维技术** 的个人笔记网站，记录我在实际工作中的技术实践和经验总结。

## 📚 坚持更新

<AutoCatalog />

<style>
.stat-item {
  padding: 1rem;
  border-radius: 8px;
  background: var(--vp-c-bg-soft);
  border: 1px solid var(--vp-c-divider);
  transition: all 0.3s ease;
}

.stat-item:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

.stat-number {
  font-size: 2rem;
  font-weight: bold;
  color: var(--vp-c-brand);
}

.stat-label {
  font-size: 0.9rem;
  color: var(--vp-c-text-2);
  margin-top: 0.5rem;
}

.grid {
  display: grid;
  gap: 1rem;
}

.grid-cols-1 {
  grid-template-columns: repeat(1, minmax(0, 1fr));
}

@media (min-width: 768px) {
  .grid-cols-2 {
    grid-template-columns: repeat(2, minmax(0, 1fr));
  }
  
  .md\\:grid-cols-2 {
    grid-template-columns: repeat(2, minmax(0, 1fr));
  }
  
  .md\\:grid-cols-4 {
    grid-template-columns: repeat(4, minmax(0, 1fr));
  }
}

@media (min-width: 1024px) {
  .lg\\:grid-cols-3 {
    grid-template-columns: repeat(3, minmax(0, 1fr));
  }
}
</style> 