
搭建Obsidian + Quartz v4+ github+Cloudflare Pages + 自定义域名技术栈
所写的笔记放这里E:\quartz\content  
1、content文件夹里放写好的笔记，比如：index.md
2、选content文件夹，作为本地库，用obsidian打开。

所用的模板放这里E:\quartz\content\Templaters
样式表在E:\quartz\quartz\styles
我的自定义css在E:\quartz\quartz\styles\custom.scss

样式如下：@use "./base.scss";


:root {
  --color-职场: #ff6b6b;
  --color-哲学: #5f27cd;
  --color-社会观察: #00b894;
  --color-个人成长: #fdcb6e;
  --color-犀利吐槽: #fd79a8;
  --color-AI: #3498db;
  --color-思考: #00cec9;
  --color-竞争哲学: #8e44ad;
  --color-心态调整: #16a085;
  --color-成功学: #e17055;
  --color-权力结构: #c0392b;
  --color-人性: #27ae60;
  --color-default: #667eea;
}

body {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
  color: #1e293b;
  line-height: 1.6;
}

.tag-header {
  padding: 3.5rem 1.5rem;
  text-align: center;
  color: white;
  h1 { font-size: 2.2rem; font-weight: 700; margin: 0 0 0.5rem; }
  p { opacity: 0.9; margin: 0; font-size: 1.1rem; }
}

.tag-content-wrapper {
  background: #f8fafc;
  padding: 3rem 1.5rem;
  .tag-content { max-width: 720px; margin: 0 auto; display: grid; gap: 1.5rem; }
}

.tag-card {
  background: white;
  border-radius: 12px;
  padding: 1.8rem;
  border-left: 5px solid var(--card-color, var(--color-default));
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.06);
  .sentence { font-size: 1.08rem; line-height: 1.75; }
  .tag-links {
    margin-top: 1.5rem;
    display: flex; justify-content: flex-end; flex-wrap: wrap; gap: 0.35rem;
    a {
      background: rgba(102, 126, 234, 0.125);
      color: #667eea;
      padding: 0.2rem 0.6rem;
      border-radius: 12px;
      text-decoration: none;
      font-weight: 500;
      white-space: nowrap;
      transition: 0.3s;
      &:hover { background: #667eea; color: white; }
    }
  }
}

.tag-footer {
  margin-top: 4rem; padding: 2rem;
  background: white; border-radius: 12px; text-align: center;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.06);
  p { color: #64748b; margin-bottom: 1rem; font-size: 0.95rem; }
}

@media(max-width: 768px) {
  .tag-header { padding: 2rem 1rem; h1 { font-size: 1.8rem; } p { font-size: 1rem; } }
  .tag-content-wrapper { padding: 2rem 1rem; }
  .tag-card { padding: 1.2rem; }
  .tag-footer { padding: 1.5rem; }
}

//////////////////////
现在的问题是，我的这个自定义在网页上预览效果，在obsidian笔记的阅读视图上也发挥作用，效果也像网页一样呢？如何设置？

E:\quartz\public\index.css
怼到
E:\quartz\content\.obsidian\snippets里。





