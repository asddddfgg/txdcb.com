
<%*
// ==========================================
// 🚀 SEO优化版标签收集器
// 新增：1. 专业Front Matter SEO元数据 2. 每个金句的article标签
// 包含：130汉字Meta Description、Keywords、Canonical等完整SEO优化
// ==========================================

// 立即设置一个执行锁，防止重复执行
if (typeof window._tagCollectorRunning !== 'undefined') {
    tR += "# ⚠️ 检测到重复执行，已停止\n\n> 上次运行尚未完成，请稍后再试";
    return;
}
window._tagCollectorRunning = true;

// 清理函数
const cleanup = () => {
    setTimeout(() => {
        delete window._tagCollectorRunning;
    }, 1000);
};

try {
    // ==========================================
    // 🎨 集中配置区域
    // ==========================================
    
    const TARGET_FOLDER = "02 Notes"; 
    const HEADER_SUFFIX = "的名言金句"; 
    const SOURCE_ICON = "📘";
    
    const EXCLUDE_FOLDERS = [
        "Templaters",
        "04 Private 私人文件夹",
        "templates"
    ];
    
    const EXCLUDE_FILES = new Set([
        "万能标签搜索器.md",
        "全站标签总览.md",
        "tR.md"
    ]);
    
    const startTime = Date.now();
    const currentFile = app.workspace.activeLeaf?.view?.file?.name;
    if (currentFile) EXCLUDE_FILES.add(currentFile);

    // 🎨 色彩系统
    const COLORS = {
        "职场": "#ff6b6b",
        "哲学": "#5f27cd", 
        "社会观察": "#00b894",
        "个人成长": "#fdcb6e",
        "犀利吐槽": "#fd79a8",
        "AI": "#3498db",
        "思考": "#00cec9",
        "竞争哲学": "#8e44ad",
        "心态调整": "#16a085",
        "成功学": "#e17055",
        "权力结构": "#c0392b",
        "人性": "#27ae60"
    };

    function getColor(tag) {
        return COLORS[tag] || "#667eea";
    }

    // 🔍 扫描逻辑
    console.log("🔄 开始扫描标签...");
    
    const allFiles = app.vault.getMarkdownFiles().filter(file => {
        const name = file.name;
        const path = file.path;
        
        if (EXCLUDE_FILES.has(name)) return false;
        if (name.includes(HEADER_SUFFIX) && path.includes(TARGET_FOLDER)) return false;
        if (name === "全站标签总览.md" && path.includes(TARGET_FOLDER)) return false;
        
        for (const folder of EXCLUDE_FOLDERS) {
            if (path.includes(folder)) return false;
        }
        
        return true;
    });

    console.log(`📂 找到 ${allFiles.length} 个待扫描文件`);

    let allTags = new Set();
    let tagContentMap = new Map();
    let totalFilesScanned = 0;

    // 📖 扫描文件内容
    for (const file of allFiles) {
        totalFilesScanned++;
        try {
            const content = await app.vault.read(file);
            const lines = content.split('\n');
            
            for (let i = 0; i < lines.length; i++) {
                const line = lines[i].trim();
                if (!line || line.length < 10) continue;
                
                if (line.startsWith('```') || line.startsWith('---')) continue;
                
                const tagMatches = line.match(/#[a-zA-Z0-9_\u4e00-\u9fa5][a-zA-Z0-9\/\-_\u4e00-\u9fa5]*/g);
                if (!tagMatches) continue;
                
                let cleanSentence = line
                    .replace(/^[>-]\s*/, '')
                    .replace(/#[a-zA-Z0-9_\u4e00-\u9fa5][a-zA-Z0-9\/\-_\u4e00-\u9fa5]*/g, '')
                    .replace(/\s+/g, ' ')
                    .trim();
                
                if (cleanSentence.length < 10) continue;
                
                const validTags = tagMatches.filter(tag => {
                    const tagName = tag.substring(1);
                    return tagName.length > 1 && 
                           !tag.match(/^#[0-9]/) &&
                           !/^#[0-9a-fA-F]{3,6}$/.test(tag);
                });
                
                if (validTags.length === 0) continue;
                
                validTags.forEach(tag => {
                    allTags.add(tag);
                    
                    if (!tagContentMap.has(tag)) {
                        tagContentMap.set(tag, []);
                    }
                    
                    const existing = tagContentMap.get(tag);
                    if (!existing.some(item => item.sentence === cleanSentence)) {
                        existing.push({
                            sentence: cleanSentence,
                            sourceFile: file.basename,
                            allTags: [...validTags]
                        });
                    }
                });
            }
        } catch (error) {
            console.warn(`❌ 读取文件 ${file.name} 时出错:`, error);
        }
    }

    console.log(`🏷️ 发现 ${allTags.size} 个标签`);

    // 📁 确保目标文件夹存在
    const folder = app.vault.getAbstractFileByPath(TARGET_FOLDER);
    if (!folder) {
        await app.vault.createFolder(TARGET_FOLDER);
        console.log(`📁 创建文件夹: ${TARGET_FOLDER}`);
    }

    // 📝 SEO辅助函数
    function generateSEOMetaDescription(tagName, contents) {
        // 提取前3个最具代表性的句子
        const topSentences = contents.slice(0, 3).map(item => {
            let sentence = item.sentence.trim();
            // 确保句子长度适中
            if (sentence.length > 40) {
                sentence = sentence.substring(0, 38) + '...';
            }
            return sentence;
        });
        
        // 构建130字左右的meta description
        const baseDescription = `探索${tagName}的智慧真谛：精选${contents.length}条${tagName}相关名言金句，涵盖深度思考、实践指南和人生哲学。`;
        
        const sentencesStr = topSentences.join('；');
        let description = `${baseDescription} ${sentencesStr}。`;
        
        // 确保长度在120-140汉字之间
        if (description.length < 120) {
            description += `这些${tagName}智慧语录将助您深入理解${tagName}的本质，启迪思维，指引行动方向。`;
        }
        
        // 截断到135字左右
        if (description.length > 140) {
            description = description.substring(0, 135) + '。';
        }
        
        return description;
    }
    
    function extractKeywords(tagName, contents) {
        const keywords = new Set([tagName, '名言', '金句', '语录', '智慧', '哲理', '思考']);
        
        // 从内容中提取高频词汇（简化版）
        contents.slice(0, 10).forEach(item => {
            const sentence = item.sentence;
            // 提取2-3个字的可能关键词
            const words = sentence.match(/[\u4e00-\u9fa5]{2,3}/g);
            if (words) {
                words.slice(0, 5).forEach(word => keywords.add(word));
            }
        });
        
        // 从所有标签中提取关键词
        contents.slice(0, 5).forEach(item => {
            item.allTags.forEach(tag => {
                const tagText = tag.replace('#', '');
                if (tagText.length > 1 && tagText !== tagName) {
                    keywords.add(tagText);
                }
            });
        });
        
        return Array.from(keywords).slice(0, 15);
    }
    
    function generateCanonicalTag(tagName) {
        // 在实际部署时，应替换为真实域名
        const baseUrl = "https://example.com";
        const encodedTag = encodeURIComponent(tagName);
        return `${baseUrl}/${encodedTag}`;
    }

    // 📝 为每个标签生成文件
    const allTagsArray = Array.from(allTags).sort();
    let createdFiles = 0, updatedFiles = 0;

    for (const tag of allTagsArray) {
        const contents = tagContentMap.get(tag) || [];
        if (contents.length === 0) continue;
        
        const tagName = tag.replace('#', '');
        const color = getColor(tagName);
        const lightColor = color + '20';
        
        // 生成SEO元数据
        const seoDescription = generateSEOMetaDescription(tagName, contents);
        const seoKeywords = extractKeywords(tagName, contents);
        const canonicalUrl = generateCanonicalTag(tagName);
        
        // 🆕 生成专业Front Matter
        const frontMatter = `---
# 🎯 SEO优化元数据
seo_title: "${tagName}的名言金句 | 精选${contents.length}条智慧语录"
meta_description: "${seoDescription}"
meta_keywords: "${seoKeywords.join(', ')}"
og_title: "${tagName}的名言金句"
og_description: "${seoDescription}"
og_type: "article"
twitter_card: "summary_large_image"
canonical_url: "${canonicalUrl}"
robots: "index, follow, max-image-preview:large"
article_section: "${tagName}智慧"
content_type: "名言金句集合"
language: "zh-CN"

# 📊 内容统计
quote_count: ${contents.length}
source_count: ${new Set(contents.map(c => c.sourceFile)).size}
last_updated: "${new Date().toISOString()}"
generated_by: "Obsidian标签收集器"

# 🏷️ 标签分类
primary_tag: "${tagName}"
related_tags: "${contents.slice(0, 5).flatMap(item => item.allTags.map(t => t.replace('#', ''))).filter((v, i, a) => a.indexOf(v) === i && v !== tagName).join(', ')}"

# 🌐 社交媒体
og_image: "/images/${tagName}-og.jpg"
og_image_alt: "${tagName}名言金句封面图"
twitter_creator: "@wisdom_quotes"

# 📱 移动优化
viewport: "width=device-width, initial-scale=1.0"
mobile_friendly: true
---
`;

        const fileName = `${tagName}${HEADER_SUFFIX}.md`;
        const sourceFiles = new Set(contents.map(c => c.sourceFile));
        
        // --- 生成HTML内容（添加Front Matter）---
        let htmlContent = frontMatter + `<div style="
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
            color: #1e293b;
            background: #f8fafc;
            min-height: 100vh;
            padding: 0;
            margin: 0;
        ">`;
        
        // 顶部标题
        htmlContent += `
        <div style="background: ${color}; padding: 3rem 1.5rem; text-align: center; color: white;">
            <h1 style="font-size: 2rem; font-weight: 700; margin: 0 0 0.5rem;">关于「${tagName}」的名言金句</h1>
            <p style="opacity: 0.9; margin: 0; font-size: 1rem;">收录 ${contents.length} 条内容 · 来自 ${sourceFiles.size} 份笔记</p>
        </div>`;
        
        // 主内容区
        htmlContent += `
        <div style="max-width: 700px; margin: 0 auto; padding: 2rem 1.5rem;">
            <div style="display: grid; gap: 1.25rem;">`;
        
        // 按句子排序
        contents.sort((a, b) => a.sentence.localeCompare(b.sentence));
        
        // 生成卡片 - 每个金句都用article标签包裹
        for (const item of contents) {
            const firstTagName = item.allTags[0]?.replace('#', '') || tagName;
            const cardColor = getColor(firstTagName);
            
            // 生成标签链接
            const tagLinks = item.allTags.map(t => {
                const tName = t.replace('#', '');
                const tColor = getColor(tName);
                return `<a href="obsidian://open?vault=${encodeURIComponent(app.vault.getName())}&file=${encodeURIComponent(tName + HEADER_SUFFIX)}"
                       style="
                           background: ${tColor}20;
                           color: ${tColor};
                           padding: 0.2rem 0.6rem;
                           border-radius: 12px;
                           margin-left: 0.25rem;
                           text-decoration: none;
                           font-weight: 500;
                       "
                       onmouseover="this.style.background='${tColor}'; this.style.color='white'"
                       onmouseout="this.style.background='${tColor}20'; this.style.color='${tColor}'">${t}</a>`;
            }).join('');
            
            htmlContent += `
            <article style="
                background: white;
                border-radius: 8px;
                padding: 1.5rem;
                border-left: 4px solid ${cardColor};
                box-shadow: 0 2px 8px rgba(0,0,0,0.04);
            ">
                <div style="font-size: 1.05rem; line-height: 1.6; margin-bottom: 1rem;">
                    ${item.sentence}
                </div>
                <div style="
                    display: flex;
                    justify-content: flex-end;
                    align-items: center;
                    flex-wrap: wrap;
                    gap: 0.5rem;
                    font-size: 0.85rem;
                    color: #64748b;
                    margin-top: 1rem;
                ">
                    <div style="display: flex; flex-wrap: wrap; gap: 0.25rem;">
                        ${tagLinks}
                    </div>
                </div>
            </article>`;
        }
        
        // 底部
        htmlContent += `
            </div>
            <div style="margin-top: 3rem; padding: 1.5rem; background: white; border-radius: 8px; text-align: center;">
                <p style="color: #64748b; margin-bottom: 1rem;">
                    🕐 生成时间：${new Date().toLocaleString('zh-CN')}
                </p>
                <a href="obsidian://open?vault=${encodeURIComponent(app.vault.getName())}&file=${encodeURIComponent(TARGET_FOLDER + '/全站标签总览')}"
                   style="
                       display: inline-block;
                       background: ${color};
                       color: white;
                       text-decoration: none;
                       padding: 0.5rem 1.25rem;
                       border-radius: 6px;
                       font-weight: 500;
                   ">返回总览</a>
            </div>
        </div>
        </div>`;
        
        // 创建或更新文件
        const filePath = `${TARGET_FOLDER}/${fileName}`;
        const existingFile = app.vault.getAbstractFileByPath(filePath);
        
        try {
            if (existingFile) {
                await app.vault.modify(existingFile, htmlContent);
                updatedFiles++;
            } else {
                await app.vault.create(filePath, htmlContent);
                createdFiles++;
            }
        } catch (error) {
            console.error(`❌ 处理文件 ${fileName} 时出错:`, error);
        }
    }

    // 📊 生成总览文件
    const overviewContent = `# 🏷️ 全站标签总览

## 📈 统计信息
- **扫描文件**：${totalFilesScanned} 份
- **发现标签**：${allTags.size} 个
- **生成页面**：${createdFiles + updatedFiles} 个
- **SEO优化**：✅ 专业Front Matter ✅ Article标签 ✅ 130字描述
- **更新时间**：${new Date().toLocaleString()}

## 📋 标签列表
${allTagsArray.map(tag => {
    const contents = tagContentMap.get(tag) || [];
    const sources = new Set(contents.map(c => c.sourceFile));
    const tagName = tag.replace('#', '');
    return `- **${tag}** (${contents.length}条，${sources.size}个文件) → [[${tagName}${HEADER_SUFFIX}]]`;
}).join('\n')}

---

*SEO优化完成：${new Date().toLocaleString('zh-CN')}*`;

    const overviewPath = `${TARGET_FOLDER}/全站标签总览.md`;
    const existingOverview = app.vault.getAbstractFileByPath(overviewPath);
    
    if (existingOverview) {
        await app.vault.modify(existingOverview, overviewContent);
    } else {
        await app.vault.create(overviewPath, overviewContent);
    }

    // 🎊 显示结果
    const runTime = ((Date.now() - startTime) / 1000).toFixed(1);
    tR += `# ✅ SEO专业优化完成！

## 📊 执行结果
- **扫描文件**：${totalFilesScanned} 份
- **发现标签**：${allTags.size} 个
- **新建页面**：${createdFiles} 个
- **更新页面**：${updatedFiles} 个
- **处理耗时**：${runTime} 秒

## 🏆 SEO优化功能
✅ **专业Front Matter** - 完整SEO元数据  
✅ **130字Meta Description** - 针对每个标签精心生成  
✅ **Article标签** - 每条金句语义化包裹  
✅ **关键词优化** - 自动提取15个相关关键词  
✅ **Canonical URL** - 标准化链接处理  
✅ **社交媒体元数据** - Open Graph & Twitter Cards  

## 🔗 快速访问
- [[全站标签总览]] - 查看所有标签统计
- [[${TARGET_FOLDER}]] - 进入生成目录

> 🕐 运行时间：${new Date().toLocaleString('zh-CN')}
> 
> 💡 SEO专家提示：
> 1. ✅ Meta Description针对搜索意图优化
> 2. ✅ Keywords覆盖长尾关键词
> 3. ✅ 结构化数据为搜索引擎提供清晰信号
> 4. ✅ 移动端友好，加载速度快
`;

} catch (error) {
    console.error("❌ 模板执行出错:", error);
    tR += `# ❌ 执行出错

错误信息：
\`\`\`
${error.message}
\`\`\`

请检查配置后重试...`;
} finally {
    cleanup();
}
%>