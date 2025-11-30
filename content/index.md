---
title: "首页"
---
<div class="home-container">

<div class="hero-section">
  <div class="hero-content">
    <h1>🌱 我的数字花园</h1>
    <div class="tagline">思想在这里生长，知识在这里绽放</div>
    <div class="description">欢迎来到我的个人知识库，这里记录了我的学习历程、思考碎片和项目总结</div>
  </div>
</div>

<div class="stats-section">
  <div class="stat-card">
    <div class="stat-number">📚</div>
    <div class="stat-label">知识笔记</div>
  </div>
  <div class="stat-card">
    <div class="stat-number">🔗</div>
    <div class="stat-label">思维连接</div>
  </div>
  <div class="stat-card">
    <div class="stat-number">🚀</div>
    <div class="stat-label">持续更新</div>
  </div>
</div>

<div class="content-grid">
  <div class="content-column">
    <div class="section-card">
      <h2>📖 学习领域</h2>
      <div class="link-list">
        <div class="link-item"><a href="编程开发">编程开发</a> - 代码、框架、技术实践</div>
        <div class="link-item"><a href="产品设计">产品设计</a> - 用户体验、交互设计</div>
        <div class="link-item"><a href="个人成长">个人成长</a> - 学习方法、效率工具</div>
        <div class="link-item"><a href="读书笔记">读书笔记</a> - 阅读心得、书摘整理</div>
      </div>
    </div>
  </div>

  <div class="content-column">
    <div class="section-card">
      <h2>🌟 精选内容</h2>
      <div class="featured-links">
        <div class="featured-item">
          <a href="我的Obsidian工作流">我的Obsidian工作流</a>
          <span class="badge">📝</span>
        </div>
        <div class="featured-item">
          <a href="如何构建第二大脑">如何构建第二大脑</a>
          <span class="badge">🧠</span>
        </div>
        <div class="featured-item">
          <a href="高效学习的方法论">高效学习的方法论</a>
          <span class="badge">🚀</span>
        </div>
      </div>
    </div>
  </div>
</div>

<div class="recent-section">
  <div class="section-card">
    <h2>🔄 最近更新</h2>
    <div class="recent-list">
      <div class="recent-item">
        <div class="recent-title"><a href="最新笔记1">最新笔记标题</a></div>
        <div class="recent-date">2024-01-15</div>
      </div>
      <div class="recent-item">
        <div class="recent-title"><a href="最新笔记2">另一个更新内容</a></div>
        <div class="recent-date">2024-01-14</div>
      </div>
    </div>
  </div>
</div>

<div class="graph-section">
  <div class="section-card">
    <h2>🗺️ 知识图谱</h2>
    <div class="graph-placeholder">
      <!-- 这里可以放置知识图谱组件 -->
      <div class="graph-nodes">
        <div class="node central">核心概念</div>
        <div class="node connected">相关主题</div>
        <div class="node connected">其他领域</div>
      </div>
    </div>
  </div>
</div>



<style>
.home-container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px;
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', system-ui;
}

/* Hero Section */
.hero-section {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  border-radius: 20px;
  padding: 60px 40px;
  margin-bottom: 40px;
  text-align: center;
  color: white;
}

.hero-content h1 {
  font-size: 3.5rem;
  margin: 0 0 20px 0;
  font-weight: 700;
}

.tagline {
  font-size: 1.5rem;
  margin-bottom: 15px;
  opacity: 0.95;
  font-weight: 600;
}

.description {
  font-size: 1.1rem;
  opacity: 0.9;
  max-width: 600px;
  margin: 0 auto;
  line-height: 1.6;
}

/* Stats Section */
.stats-section {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
  gap: 20px;
  margin-bottom: 40px;
}

.stat-card {
  background: var(--light);
  border: 1px solid var(--lightgray);
  border-radius: 15px;
  padding: 30px 20px;
  text-align: center;
  transition: transform 0.2s ease;
}

.stat-card:hover {
  transform: translateY(-5px);
}

.stat-number {
  font-size: 2.5rem;
  margin-bottom: 10px;
}

.stat-label {
  font-size: 1rem;
  color: var(--darkgray);
  font-weight: 600;
}

/* Content Grid */
.content-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(350px, 1fr));
  gap: 30px;
  margin-bottom: 40px;
}

.section-card {
  background: var(--light);
  border: 1px solid var(--lightgray);
  border-radius: 15px;
  padding: 30px;
  height: fit-content;
}

.section-card h2 {
  margin: 0 0 20px 0;
  color: var(--secondary);
  font-size: 1.4rem;
}

/* Link Styles */
.link-list .link-item {
  margin-bottom: 12px;
  padding: 8px 0;
  border-bottom: 1px solid var(--lightgray);
}

.link-list .link-item:last-child {
  border-bottom: none;
}

.link-list a {
  color: var(--secondary);
  text-decoration: none;
  font-weight: 600;
}

.link-list a:hover {
  text-decoration: underline;
}

.featured-links .featured-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 12px 0;
  border-bottom: 1px solid var(--lightgray);
}

.featured-links .featured-item:last-child {
  border-bottom: none;
}

.featured-item a {
  color: var(--dark);
  text-decoration: none;
  font-weight: 500;
  flex-grow: 1;
}

.featured-item a:hover {
  color: var(--secondary);
}

.badge {
  background: var(--tertiary);
  color: white;
  padding: 4px 8px;
  border-radius: 20px;
  font-size: 0.8rem;
}

/* Recent Section */
.recent-list .recent-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 12px 0;
  border-bottom: 1px solid var(--lightgray);
}

.recent-item:last-child {
  border-bottom: none;
}

.recent-title a {
  color: var(--dark);
  text-decoration: none;
  font-weight: 500;
}

.recent-title a:hover {
  color: var(--secondary);
}

.recent-date {
  color: var(--gray);
  font-size: 0.9rem;
}

/* Graph Section */
.graph-section {
  margin-bottom: 40px;
}

.graph-placeholder {
  background: linear-gradient(135deg, #f5f7fa 0%, #c3cfe2 100%);
  border-radius: 12px;
  padding: 40px 20px;
  text-align: center;
  min-height: 200px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.graph-nodes {
  display: flex;
  gap: 20px;
  flex-wrap: wrap;
  justify-content: center;
}

.node {
  padding: 15px 25px;
  border-radius: 25px;
  font-weight: 600;
  color: white;
}

.node.central {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
}

.node.connected {
  background: linear-gradient(135deg, #f093fb 0%, #f5576c 100%);
}

/* Footer Section */
.footer-section {
  border-top: 1px solid var(--lightgray);
  padding-top: 40px;
  margin-top: 40px;
  text-align: center;
}

.footer-links {
  margin-bottom: 20px;
}

.footer-links a {
  color: var(--secondary);
  text-decoration: none;
  margin: 0 15px;
  font-weight: 500;
}

.footer-links a:hover {
  text-decoration: underline;
}

.footer-note {
  color: var(--gray);
  font-size: 0.9rem;
}

/* Responsive Design */
@media (max-width: 768px) {
  .home-container {
    padding: 15px;
  }
  
  .hero-content h1 {
    font-size: 2.5rem;
  }
  
  .tagline {
    font-size: 1.2rem;
  }
  
  .content-grid {
    grid-template-columns: 1fr;
  }
  
  .section-card {
    padding: 20px;
  }
  
  .stats-section {
    grid-template-columns: repeat(3, 1fr);
    gap: 10px;
  }
  
  .stat-card {
    padding: 20px 10px;
  }
}

/* Dark mode support */
@media (prefers-color-scheme: dark) {
  .section-card, .stat-card {
    background: var(--light);
    border-color: var(--lightgray);
  }
  
  .graph-placeholder {
    background: linear-gradient(135deg, #2c3e50 0%, #3498db 100%);
  }
}
</style>




//////////////////////////////
---
title: 我的数字果园
description: 我的个人知识库，记录学习笔记、创作灵感和项目实践。
tags: [数字花园, 知识库, 学习笔记]
---

# 🌿 我的数字果园
思想在这里生根，知识在这里发芽。

欢迎来到我的个人知识库，这里记录了学习笔记、创作灵感和项目实践。每个内容都是可探索、可复用的知识碎片。

[查看最新笔记](#最近更新){.btn-primary}

---

## 📊 站点统计

| 📖 笔记总数 | 🔗 关联条目 | ⏳ 本周更新 |
|-------------|-------------|------------|
| 128         | 342         | 7          |

---

## 🗂️ 探索主题

- [幼教实操](/category/education) - 课程活动
- [英语词汇](/category/english) - 单词拆解
- [短视频创作](/category/video) - 脚本模板
- [自动化工具](/category/tools) - 工作流优化

---

## 🌟 精选专题

- [Obsidian 快速入门](/guide/obsidian) `指南`
- [第二大脑方法](/guide/second-brain) `方法`
- [短视频脚本库](/guide/video) `模板`

---

## 🔄 最近更新

1. [情绪管理课堂设计](/notes/emo1) - 2025-11-30  
2. [高一英语词汇整理](/notes/vocab1) - 2025-11-28  
3. [短视频创作模板](/notes/video1) - 2025-11-25  

---

## 🗺️ 知识概览

- 核心
  - 概念 A
  - 概念 B

---

## 🔗 联系方式

- [GitHub](https://github.com/yourname)
- [博客](https://yourblog.example)
- 邮箱: you@example.com

> 本站由 Obsidian + Quartz 构建，部署于 Cloudflare Pages



////////////////////////////////////



---
title: "量子思维花园"
cssclass: "quantum-garden"
---

## 🌌 量子思维花园
**在知识的宇宙中探索无限可能**

### 📊 实时数据仪表板

| 指标 | 数值 | 趋势 |
|------|------|------|
| **知识节点** | `1,247` | ↑ 12% 本周 |
| **思维连接** | `8,642` | ↑ 8% 本周 |
| **活跃项目** | `23` | → 保持稳定 |
| **知识密度** | `87%` | ↑ 5% 本月 |

### 🌠 基于你的活动推荐

#### 🟥 高优先级
**完成机器学习项目文档**
- 基于你最近的搜索和笔记活动
- 📊 **进度**: 65% 完成
  ```mermaid
  graph LR
  A[65% 完成] --> B[100%]
  style A fill:#ff4444
  style B fill:#444
