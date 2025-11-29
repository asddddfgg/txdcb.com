保存起来待用，

# 先搞清

[[引用代码而不执行代码，在obsidian笔记里怎么写]],再说

从前三点，复制到后三点，粘贴到笔记里，OK了。



`````
```dataviewjs
// 🔧 在这里修改配置 🔧
const CONFIG = {
  TARGET_TAG: "#职场",      // 要查询的标签
  LIBRARY_NAME: "职场金句"  // 库名称
};
// 🔧 配置结束 🔧

// 🆕 添加刷新按钮（放在最前面）
this.container.createEl('button', {text: '🔄 刷新数据'}).onclick = () => location.reload();

dv.header(3, `📚 ${CONFIG.LIBRARY_NAME}库`)

// 🎯 完整查询，不限制数量
const pages = dv.pages(CONFIG.TARGET_TAG)
dv.paragraph(`找到 ${pages.length} 篇包含${CONFIG.TARGET_TAG}标签的笔记`)

if (pages.length === 0) {
  dv.paragraph(`> 暂无${CONFIG.LIBRARY_NAME}`)
} else {
  let allSentences = [];
  
  for (let page of pages) {
    try {
      const file = app.vault.getMarkdownFiles().find(f => f.path === page.file.path);
      if (file) {
        const content = await app.vault.read(file);
        
        // 完整扫描，不跳过任何行
        let inCodeBlock = false;
        const lines = content.split('\n')
          .map(line => line.trim())
          .filter(line => {
            // 只排除代码块内的内容
            if (line.startsWith('```')) {
              inCodeBlock = !inCodeBlock;
              return false;
            }
            if (inCodeBlock) return false;
            
            // 确保包含目标标签且是有效内容
            return line && 
                   line.includes(CONFIG.TARGET_TAG) && 
                   line.length > 10;
          });
        
        allSentences = allSentences.concat(lines);
      }
    } catch (e) {
      console.log(`读取文件 ${page.file.name} 时出错:`, e);
    }
  }
  
  // 完整去重显示
  const uniqueSentences = [...new Set(allSentences)];
  
  if (uniqueSentences.length === 0) {
    dv.paragraph(`> 未找到具体的${CONFIG.LIBRARY_NAME}内容`)
  } else {
    // 显示所有找到的内容，不限制数量
    for (let sentence of uniqueSentences) {
      dv.paragraph(`> ${sentence}`);
    }
    
    // 完整统计
    dv.paragraph(`**完整扫描完成 · 共 ${uniqueSentences.length} 条${CONFIG.LIBRARY_NAME}**`);
  }
}


```
`````
