<%*
// ==========================================
// 🚀 SEO专家版：智能增量更新标签收集器
// 特性：1. 智能SEO元数据管理 2. 稳定排名保护 3. 内容增量更新
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
    
    // 🎯 SEO专家配置
    const SEO_CONFIG = {
        // 更新阈值：内容变化30%以下不更新SEO元数据
        MINOR_UPDATE_THRESHOLD: 0.3,    // 30% 小更新
        MAJOR_UPDATE_THRESHOLD: 0.5,    // 50% 大更新
        CRITICAL_UPDATE_THRESHOLD: 0.8, // 80% 重大更新
        
        // 描述长度控制
        DESCRIPTION_MIN_LENGTH: 120,
        DESCRIPTION_MAX_LENGTH: 140,
        
        // 关键词控制
        MAX_KEYWORDS: 12,
        
        // 稳定性控制
        PRESERVE_EXISTING_SEO: true, // 保留已有SEO元数据
        FORCE_SEO_UPDATE: false      // 强制更新所有SEO元数据（手动开启）
    };
    
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

    // ==========================================
    // 🎯 SEO专家辅助函数
    // ==========================================
    
    // 1. 解析现有front matter
    function parseFrontMatter(content) {
        const result = {};
        const frontMatterMatch = content.match(/^---\n([\s\S]*?)\n---/);
        
        if (!frontMatterMatch) return null;
        
        const frontMatterText = frontMatterMatch[1];
        const lines = frontMatterText.split('\n');
        
        for (const line of lines) {
            if (!line.trim() || line.startsWith('#')) continue;
            
            const match = line.match(/^([^:#]+):\s*(.+)$/);
            if (match) {
                let key = match[1].trim();
                let value = match[2].trim();
                
                // 移除可能的引号
                if (value.startsWith('"') && value.endsWith('"')) {
                    value = value.slice(1, -1);
                } else if (value.startsWith("'") && value.endsWith("'")) {
                    value = value.slice(1, -1);
                }
                
                result[key] = value;
            }
        }
        
        return result;
    }
    
    // 2. 判断是否需要更新SEO元数据
    function shouldUpdateSEO(existingFrontMatter, newContentCount) {
        // 如果强制更新，直接返回true
        if (SEO_CONFIG.FORCE_SEO_UPDATE) return true;
        
        // 如果没有现有front matter，需要生成
        if (!existingFrontMatter) return true;
        
        // 获取现有内容数量
        const oldCountMatch = existingFrontMatter.quote_count || "0";
        const oldCount = parseInt(oldCountMatch);
        
        if (isNaN(oldCount) || oldCount === 0) return true;
        
        // 计算内容变化率
        const changeRatio = Math.abs(newContentCount - oldCount) / oldCount;
        
        // 根据阈值决定是否更新
        if (changeRatio >= SEO_CONFIG.CRITICAL_UPDATE_THRESHOLD) {
            return "critical"; // 重大更新
        } else if (changeRatio >= SEO_CONFIG.MAJOR_UPDATE_THRESHOLD) {
            return "major"; // 大更新
        } else if (changeRatio >= SEO_CONFIG.MINOR_UPDATE_THRESHOLD) {
            return "minor"; // 小更新
        } else {
            return false; // 不更新SEO元数据
        }
    }
    
    // 3. 生成稳定的SEO描述（避免频繁变化）
    function generateStableSEODescription(tagName, contentCount) {
        // 预定义稳定的描述模板
        const templates = [
            `探索${tagName}的核心智慧：精选${contentCount}条${tagName}相关经典名言与深度思考，涵盖人生哲学、实践指南和思维启示，助您深入理解${tagName}本质。`,
            `深度解析${tagName}的真谛：收录${contentCount}条${tagName}智慧语录，从多维度揭示${tagName}的实践价值与哲学思考，启迪心智成长。`,
            `${tagName}智慧宝库：${contentCount}条精选名言金句，系统梳理${tagName}的核心观点与实用建议，为您的思考与决策提供参考。`,
            `聚焦${tagName}的深度洞察：${contentCount}条经典语录汇聚${tagName}思想精华，涵盖理论思考与实践应用，助您掌握${tagName}要义。`
        ];
        
        // 根据标签名选择稳定的模板（确保同一标签每次选择相同模板）
        const hash = tagName.split('').reduce((acc, char) => acc + char.charCodeAt(0), 0);
        const templateIndex = hash % templates.length;
        let description = templates[templateIndex];
        
        // 确保描述长度在合理范围内
        if (description.length < SEO_CONFIG.DESCRIPTION_MIN_LENGTH) {
            description += ` 这些${tagName}智慧将引导您深入思考与实践应用。`;
        }
        
        if (description.length > SEO_CONFIG.DESCRIPTION_MAX_LENGTH) {
            description = description.substring(0, SEO_CONFIG.DESCRIPTION_MAX_LENGTH - 1) + '。';
        }
        
        return description;
    }
    
    // 4. 提取稳定的关键词（避免频繁变化）
    function extractStableKeywords(tagName, existingKeywords = "") {
        const baseKeywords = new Set([
            tagName, 
            '名言', 
            '金句', 
            '语录', 
            '智慧', 
            '哲理', 
            '思考',
            '经典',
            '摘录',
            '精华'
        ]);
        
        // 添加分类相关关键词
        if (COLORS[tagName]) {
            const categoryMap = {
                "职场": ['职场智慧', '工作哲学', '职业发展', '管理心得', '职场经验'],
                "哲学": ['哲学思考', '人生哲学', '思想深度', '智慧启迪', '哲理感悟'],
                "社会观察": ['社会现象', '社会洞察', '观察思考', '社会分析', '现实思考'],
                "个人成长": ['成长智慧', '自我提升', '进步思考', '发展哲理', '成长建议'],
                "AI": ['人工智能', 'AI思考', '科技智慧', '未来趋势', '技术哲学']
            };
            
            if (categoryMap[tagName]) {
                categoryMap[tagName].forEach(keyword => baseKeywords.add(keyword));
            }
        }
        
        // 如果已有关键词，优先保留它们
        if (existingKeywords) {
            const existingArray = existingKeywords.split(',').map(k => k.trim()).filter(k => k);
            existingArray.slice(0, 6).forEach(keyword => baseKeywords.add(keyword));
        }
        
        // 返回限定数量的关键词
        return Array.from(baseKeywords).slice(0, SEO_CONFIG.MAX_KEYWORDS);
    }
    
    // 5. 生成完整的front matter（新页面或重大更新）
    function generateCompleteFrontMatter(tagName, contents, updateType = "new") {
        const sourceFiles = new Set(contents.map(c => c.sourceFile));
        const description = generateStableSEODescription(tagName, contents.length);
        const keywords = extractStableKeywords(tagName);
        
        const updateLabels = {
            "new": "🚀 新页面生成",
            "critical": "🔄 重大内容更新",
            "major": "📈 重要内容扩充",
            "minor": "🔧 内容微调"
        };
        
        return `---
# 🎯 SEO优化元数据 | ${updateLabels[updateType]}
seo_title: "${tagName}的名言金句 | 精选${contents.length}条智慧语录"
meta_description: "${description}"
meta_keywords: "${keywords.join(', ')}"
og_title: "${tagName}的名言金句"
og_description: "${description}"
og_type: "article"
twitter_card: "summary_large_image"
canonical_url: "https://wisdom.com/${encodeURIComponent(tagName)}"
robots: "index, follow, max-image-preview:large"

# 📊 内容统计
quote_count: ${contents.length}
source_count: ${sourceFiles.size}
last_content_update: "${new Date().toISOString()}"
seo_version: "v2.0"
update_type: "${updateType}"

# 🏷️ 标签分类
primary_tag: "${tagName}"
category: "${COLORS[tagName] ? tagName : '综合智慧'}"
tag_type: "名言金句集合"

# 🌐 社交媒体优化
og_image: "/images/og-${tagName}.jpg"
og_image_alt: "${tagName}名言金句精选封面"
twitter_site: "@WisdomQuotes"
twitter_creator: "@WisdomQuotes"

# 📱 用户体验
viewport: "width=device-width, initial-scale=1.0"
mobile_friendly: true
language: "zh-CN"
charset: "UTF-8"

# 🔄 版本控制
content_version: "1.0.0"
generated_by: "Obsidian SEO智能收集器"
seo_stability: "stable"
last_seo_update: "${updateType === 'new' ? new Date().toISOString() : '保持不变'}"
---
`;
    }
    
    // 6. 保留原有front matter（只更新内容统计）
    function generatePreservedFrontMatter(existingFrontMatter, contents) {
        const sourceFiles = new Set(contents.map(c => c.sourceFile));
        const oldCount = parseInt(existingFrontMatter.quote_count || "0");
        const newCount = contents.length;
        const changeCount = newCount - oldCount;
        
        return `---
# 🎯 SEO优化元数据 | 📥 内容增量更新（SEO保持稳定）
seo_title: "${existingFrontMatter.seo_title || `${existingFrontMatter.primary_tag || '标签'}的名言金句`}"
meta_description: "${existingFrontMatter.meta_description || '未设置'}"
meta_keywords: "${existingFrontMatter.meta_keywords || '未设置'}"
og_title: "${existingFrontMatter.og_title || existingFrontMatter.seo_title || '未设置'}"
og_description: "${existingFrontMatter.og_description || existingFrontMatter.meta_description || '未设置'}"
og_type: "${existingFrontMatter.og_type || 'article'}"
twitter_card: "${existingFrontMatter.twitter_card || 'summary_large_image'}"
canonical_url: "${existingFrontMatter.canonical_url || 'https://wisdom.com/'}"
robots: "${existingFrontMatter.robots || 'index, follow, max-image-preview:large'}"

# 📊 内容统计（已更新）
quote_count: ${newCount} # 原${oldCount}条，新增${changeCount > 0 ? '+' : ''}${changeCount}条
source_count: ${sourceFiles.size}
last_content_update: "${new Date().toISOString()}"
seo_version: "${existingFrontMatter.seo_version || 'v1.0'}"
update_type: "content_only" # 仅内容更新，SEO元数据保持不变

# 🏷️ 标签分类
primary_tag: "${existingFrontMatter.primary_tag || '未设置'}"
category: "${existingFrontMatter.category || '未设置'}"
tag_type: "${existingFrontMatter.tag_type || '未设置'}"

# 🌐 社交媒体优化
og_image: "${existingFrontMatter.og_image || '/images/default-og.jpg'}"
og_image_alt: "${existingFrontMatter.og_image_alt || '名言金句精选封面'}"
twitter_site: "${existingFrontMatter.twitter_site || '@WisdomQuotes'}"
twitter_creator: "${existingFrontMatter.twitter_creator || '@WisdomQuotes'}"

# 📱 用户体验
viewport: "${existingFrontMatter.viewport || 'width=device-width, initial-scale=1.0'}"
mobile_friendly: "${existingFrontMatter.mobile_friendly || 'true'}"
language: "${existingFrontMatter.language || 'zh-CN'}"
charset: "${existingFrontMatter.charset || 'UTF-8'}"

# 🔄 版本控制
content_version: "${(parseFloat(existingFrontMatter.content_version || '1.0.0') + 0.1).toFixed(1)}"
generated_by: "Obsidian SEO智能收集器"
seo_stability: "stable"
last_seo_update: "${existingFrontMatter.last_seo_update || '保持稳定'}"
stability_note: "内容增量更新${changeCount > 0 ? '+' : ''}${changeCount}条，核心SEO元数据保持不变以确保排名稳定"
---
`;
    }

    // 🔍 扫描逻辑
    console.log("🔄 开始智能扫描标签...");
    
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

    // 📝 为每个标签生成文件
    const allTagsArray = Array.from(allTags).sort();
    let createdFiles = 0, updatedFiles = 0;
    let seoUpdatedFiles = 0, contentOnlyFiles = 0;

    for (const tag of allTagsArray) {
        const contents = tagContentMap.get(tag) || [];
        if (contents.length === 0) continue;
        
        const tagName = tag.replace('#', '');
        const color = getColor(tagName);
        const fileName = `${tagName}${HEADER_SUFFIX}.md`;
        const filePath = `${TARGET_FOLDER}/${fileName}`;
        const existingFile = app.vault.getAbstractFileByPath(filePath);
        
        let frontMatter = "";
        let updateType = "new";
        let existingFrontMatter = null;
        
        // 🎯 智能判断更新策略
        if (existingFile && SEO_CONFIG.PRESERVE_EXISTING_SEO) {
            try {
                const existingContent = await app.vault.read(existingFile);
                existingFrontMatter = parseFrontMatter(existingContent);
                
                if (existingFrontMatter) {
                    const seoUpdateDecision = shouldUpdateSEO(existingFrontMatter, contents.length);
                    
                    if (seoUpdateDecision === false) {
                        // 只更新内容，保留SEO
                        frontMatter = generatePreservedFrontMatter(existingFrontMatter, contents);
                        updateType = "content_only";
                        contentOnlyFiles++;
                    } else {
                        // 需要更新SEO元数据
                        frontMatter = generateCompleteFrontMatter(tagName, contents, seoUpdateDecision);
                        updateType = seoUpdateDecision;
                        seoUpdatedFiles++;
                    }
                } else {
                    // 有文件但没有front matter
                    frontMatter = generateCompleteFrontMatter(tagName, contents, "new");
                    seoUpdatedFiles++;
                }
            } catch (error) {
                console.warn(`读取现有文件 ${fileName} 失败，重新生成:`, error);
                frontMatter = generateCompleteFrontMatter(tagName, contents, "new");
                seoUpdatedFiles++;
            }
        } else {
            // 新文件或强制更新
            frontMatter = generateCompleteFrontMatter(tagName, contents, "new");
            seoUpdatedFiles++;
        }
        
        // 生成HTML内容
        const sourceFiles = new Set(contents.map(c => c.sourceFile));
        
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
        try {
            if (existingFile) {
                await app.vault.modify(existingFile, htmlContent);
                updatedFiles++;
                console.log(`📝 ${fileName}: ${updateType === "content_only" ? "内容增量更新" : "SEO元数据更新"}`);
            } else {
                await app.vault.create(filePath, htmlContent);
                createdFiles++;
                console.log(`🆕 ${fileName}: 新页面生成`);
            }
        } catch (error) {
            console.error(`❌ 处理文件 ${fileName} 时出错:`, error);
        }
    }

    // 📊 生成总览文件
    const overviewContent = `# 🏷️ 全站标签总览 - SEO智能版

## 📈 统计信息
- **扫描文件**：${totalFilesScanned} 份
- **发现标签**：${allTags.size} 个
- **新建页面**：${createdFiles} 个
- **更新页面**：${updatedFiles} 个
- **SEO更新**：${seoUpdatedFiles} 个
- **仅内容更新**：${contentOnlyFiles} 个
- **更新时间**：${new Date().toLocaleString()}

## 🎯 SEO智能更新策略
✅ **稳定排名保护** - 已有页面SEO元数据保持稳定  
✅ **智能阈值判断** - 内容变化30%以下不更新SEO  
✅ **增量内容更新** - 新增金句自动整合  
✅ **版本控制** - 清晰记录每次更新类型  
✅ **排名安全** - 避免不必要的SEO波动  

## 📋 标签列表
${allTagsArray.map(tag => {
    const contents = tagContentMap.get(tag) || [];
    const sources = new Set(contents.map(c => c.sourceFile));
    const tagName = tag.replace('#', '');
    return `- **${tag}** (${contents.length}条，${sources.size}个文件) → [[${tagName}${HEADER_SUFFIX}]]`;
}).join('\n')}

---

*智能更新完成：${new Date().toLocaleString('zh-CN')}*`;

    const overviewPath = `${TARGET_FOLDER}/全站标签总览.md`;
    const existingOverview = app.vault.getAbstractFileByPath(overviewPath);
    
    if (existingOverview) {
        await app.vault.modify(existingOverview, overviewContent);
    } else {
        await app.vault.create(overviewPath, overviewContent);
    }

    // 🎊 显示结果
    const runTime = ((Date.now() - startTime) / 1000).toFixed(1);
    tR += `# ✅ SEO智能更新完成！

## 📊 执行结果
- **扫描文件**：${totalFilesScanned} 份
- **发现标签**：${allTags.size} 个
- **新建页面**：${createdFiles} 个
- **更新页面**：${updatedFiles} 个
- **处理耗时**：${runTime} 秒

## 🏆 SEO智能更新详情
🔵 **SEO元数据更新**：${seoUpdatedFiles} 个页面  
🟢 **仅内容增量更新**：${contentOnlyFiles} 个页面  
📊 **保护已有排名**：${contentOnlyFiles} 个页面的SEO元数据保持稳定  

## 🎯 智能更新策略说明
1. **新标签页**：生成完整SEO元数据
2. **小更新(<30%)**：仅更新内容，SEO元数据不变
3. **中更新(30-50%)**：重新优化SEO元数据
4. **大更新(>50%)**：全面更新SEO和内容

## 🔧 配置选项
如需强制更新所有SEO元数据，请在代码中将 \`FORCE_SEO_UPDATE\` 设为 true
如需调整更新阈值，可修改 MINOR_UPDATE_THRESHOLD 等参数

## 🔗 快速访问
- [[全站标签总览]] - 查看详细统计
- [[${TARGET_FOLDER}]] - 进入生成目录

> 🕐 运行时间：${new Date().toLocaleString('zh-CN')}
> 
> 💡 SEO专家提示：
> 1. ✅ 智能保护已有排名页面
> 2. ✅ 增量更新避免SEO波动
> 3. ✅ 130字稳定Meta Description
> 4. ✅ 自动版本控制和更新记录
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