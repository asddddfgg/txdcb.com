<%*
// ==========================================
// 🚀 SEO商业版：智能增量更新标签收集器（文件名安全版）
// ==========================================

if (typeof window._tagCollectorRunning !== 'undefined') {
    tR += "# ⚠️ 检测到重复执行，已停止\n\n> 上次运行尚未完成，请稍后再试";
    return;
}
window._tagCollectorRunning = true;

const cleanup = () => setTimeout(() => delete window._tagCollectorRunning, 1000);

try {
    // ==========================================
    // 🎨 集中配置区域
    // ==========================================
    const TARGET_FOLDER = "02 Notes";
    const HEADER_SUFFIX = "的名言金句";

    const EXCLUDE_FOLDERS = ["Templaters", "04 Private 私人文件夹", "templates"];

    const EXCLUDE_FILES = new Set([
        "万能标签搜索器.md",
        "全站标签总览.md",
        "tR.md"
    ]);

    const SEO_CONFIG = {
        MINOR_UPDATE_THRESHOLD: 0.3,
        MAJOR_UPDATE_THRESHOLD: 0.5,
        CRITICAL_UPDATE_THRESHOLD: 0.8,
        DESCRIPTION_MIN_LENGTH: 120,
        DESCRIPTION_MAX_LENGTH: 140,
        MAX_KEYWORDS: 12,
        PRESERVE_EXISTING_SEO: true,
        FORCE_SEO_UPDATE: false
    };

    const AD_CONFIG = {
        ENABLE_ADS: true,
        AD_POSITIONS: { HEADER: true, SIDEBAR: true, IN_CONTENT: true, FOOTER: true },
        ANALYTICS: {
            GOOGLE_ANALYTICS: "UA-XXXXX-Y",
            BAIDU_TONGJI: "your-baidu-id",
            CUSTOM_TRACKING: true
        }
    };

    const startTime = Date.now();
    const currentFile = app.workspace.activeLeaf?.view?.file?.name;
    if (currentFile) EXCLUDE_FILES.add(currentFile);

    // 色彩系统
    const COLORS = {
        "职场": "#ff6b6b", "哲学": "#5f27cd", "社会观察": "#00b894", "个人成长": "#fdcb6e",
        "犀利吐槽": "#fd79a8", "AI": "#3498db", "思考": "#00cec9", "竞争哲学": "#8e44ad",
        "心态调整": "#16a085", "成功学": "#e17055", "权力结构": "#c0392b", "人性": "#27ae60"
    };
    function getColor(tag) { return COLORS[tag] || "#667eea"; }

    // ==========================================
    // 工具函数：安全文件名（关键修复）
    // ==========================================
    function safeFileName(str) {
        return str
            .replace(/[*"\\\/<>:|?]/g, match => ({
                '*': '＊', '"': '″', '\\': '＼', '/': '／',
                '<': '＜', '>': '＞', ':': '：', '|': '｜', '?': '？'
            }[match] || '_'));
    }

    // ==========================================
    // SEO 辅助函数（保持不变）
    // ==========================================
    function parseFrontMatter(content) {
        const result = {};
        const match = content.match(/^---\n([\s\S]*?)\n---/);
        if (!match) return null;
        match[1].split('\n').forEach(line => {
            if (!line.trim() || line.startsWith('#')) return;
            const m = line.match(/^([^:#]+):\s*(.+)$/);
            if (m) {
                let val = m[2].trim();
                if ((val.startsWith('"') && val.endsWith('"')) || (val.startsWith("'") && val.endsWith("'"))) val = val.slice(1, -1);
                result[m[1].trim()] = val;
            }
        });
        return result;
    }

    function shouldUpdateSEO(existing, newCount) {
        if (SEO_CONFIG.FORCE_SEO_UPDATE) return true;
        if (!existing) return true;
        const old = parseInt(existing.quote_count || "0");
        if (isNaN(old) || old === 0) return true;
        const ratio = Math.abs(newCount - old) / old;
        if (ratio >= SEO_CONFIG.CRITICAL_UPDATE_THRESHOLD) return "critical";
        if (ratio >= SEO_CONFIG.MAJOR_UPDATE_THRESHOLD) return "major";
        if (ratio >= SEO_CONFIG.MINOR_UPDATE_THRESHOLD) return "minor";
        return false;
    }

    function generateStableSEODescription(tagName, count) {
        const templates = [
            `探索${tagName}的核心智慧：精选${count}条${tagName}相关经典名言与深度思考，涵盖人生哲学、实践指南和思维启示，助您深入理解${tagName}本质。`,
            `深度解析${tagName}的真谛：收录${count}条${tagName}智慧语录，从多维度揭示${tagName}的实践价值与哲学思考，启迪心智成长。`,
            `${tagName}智慧宝库：${count}条精选名言金句，系统梳理${tagName}的核心观点与实用建议，为您的思考与决策提供参考。`,
            `聚焦${tagName}的深度洞察：${count}条经典语录汇聚${tagName}思想精华，涵盖理论思考与实践应用，助您掌握${tagName}要义。`
        ];
        const hash = tagName.split('').reduce((a,c)=>a+c.charCodeAt(0),0);
        let desc = templates[hash % templates.length];
        if (desc.length < SEO_CONFIG.DESCRIPTION_MIN_LENGTH) desc += ` 这些${tagName}智慧将引导您深入思考与实践应用。`;
        if (desc.length > SEO_CONFIG.DESCRIPTION_MAX_LENGTH) desc = desc.substring(0, SEO_CONFIG.DESCRIPTION_MAX_LENGTH-1) + '。';
        return desc;
    }

    function extractStableKeywords(tagName, existing = "") {
        const set = new Set([tagName,'名言','金句','语录','智慧','哲理','思考','经典','摘录','精华']);
        if (COLORS[tagName]) {
            const map = {
                "职场": ['职场智慧','工作哲学','职业发展','管理心得','职场经验'],
                "哲学": ['哲学思考','人生哲学','思想深度','智慧启迪','哲理感悟'],
                "社会观察": ['社会现象','社会洞察','观察思考','社会分析','现实思考'],
                "个人成长": ['成长智慧','自我提升','进步思考','发展哲理','成长建议'],
                "AI": ['人工智能','AI思考','科技智慧','未来趋势','技术哲学']
            };
            (map[tagName] || []).forEach(k => set.add(k));
        }
        if (existing) existing.split(',').map(k=>k.trim()).filter(k=>k).slice(0,6).forEach(k=>set.add(k));
        return Array.from(set).slice(0, SEO_CONFIG.MAX_KEYWORDS);
    }

    function generateCompleteFrontMatter(tagName, contents, type = "new") {
        const sources = new Set(contents.map(c=>c.sourceFile));
        const desc = generateStableSEODescription(tagName, contents.length);
        const kw = extractStableKeywords(tagName);
        const labels = {new:"🚀 新页面生成", critical:"🔄 重大内容更新", major:"📈 重要内容扩充", minor:"🔧 内容微调"};
        return `---
# SEO优化元数据 | ${labels[type]}
seo_title: "${tagName}的名言金句 | 精选${contents.length}条智慧语录"
meta_description: "${desc}"
meta_keywords: "${kw.join(', ')}"
og_title: "${tagName}的名言金句"
og_description: "${desc}"
og_type: "article"
twitter_card: "summary_large_image"
canonical_url: "https://wisdom.com/${encodeURIComponent(tagName)}"
robots: "index, follow, max-image-preview:large"

quote_count: ${contents.length}
source_count: ${sources.size}
last_content_update: "${new Date().toISOString()}"
seo_version: "v2.0"
update_type: "${type}"

primary_tag: "${tagName}"
category: "${COLORS[tagName] ? tagName : '综合智慧'}"
tag_type: "名言金句集合"

og_image: "/images/og-${tagName}.jpg"
og_image_alt: "${tagName}名言金句精选封面"
twitter_site: "@WisdomQuotes"
twitter_creator: "@WisdomQuotes"

analytics_providers: "Google Analytics, 百度统计"
ga_id: "${AD_CONFIG.ANALYTICS.GOOGLE_ANALYTICS}"
baidu_id: "${AD_CONFIG.ANALYTICS.BAIDU_TONGJI}"
tracking_enabled: true

ads_enabled: ${AD_CONFIG.ENABLE_ADS}
ad_positions: "头部,内容中部,底部"
ad_providers: "Google AdSense, 自定义广告"

viewport: "width=device-width, initial-scale=1.0"
mobile_friendly: true
language: "zh-CN"
charset: "UTF-8"

content_version: "1.0.0"
generated_by: "Obsidian SEO智能收集器"
seo_stability: "stable"
last_seo_update: "${type === 'new' ? new Date().toISOString() : '保持不变'}"
---
`;
    }

    function generatePreservedFrontMatter(existing, contents) {
        const sources = new Set(contents.map(c=>c.sourceFile));
        const old = parseInt(existing.quote_count || "0");
        const change = contents.length - old;
        return `---
# SEO优化元数据 | 内容增量更新（SEO保持稳定）
seo_title: "${existing.seo_title || (existing.primary_tag || '标签') + '的名言金句'}"
meta_description: "${existing.meta_description || '未设置'}"
meta_keywords: "${existing.meta_keywords || '未设置'}"
og_title: "${existing.og_title || existing.seo_title || '未设置'}"
og_description: "${existing.og_description || existing.meta_description || '未设置'}"
og_type: "${existing.og_type || 'article'}"
twitter_card: "${existing.twitter_card || 'summary_large_image'}"
canonical_url: "${existing.canonical_url || 'https://wisdom.com/'}"
robots: "${existing.robots || 'index, follow, max-image-preview:large'}"

quote_count: ${contents.length} # 原${old}条，新增${change>0?'+' : ''}${change}条
source_count: ${sources.size}
last_content_update: "${new Date().toISOString()}"
seo_version: "${existing.seo_version || 'v1.0'}"
update_type: "content_only"

primary_tag: "${existing.primary_tag || '未设置'}"
category: "${existing.category || '未设置'}"
tag_type: "${existing.tag_type || '未设置'}"

og_image: "${existing.og_image || '/images/default-og.jpg'}"
og_image_alt: "${existing.og_image_alt || '名言金句精选封面'}"
twitter_site: "${existing.twitter_site || '@WisdomQuotes'}"
twitter_creator: "${existing.twitter_creator || '@WisdomQuotes'}"

analytics_providers: "${existing.analytics_providers || 'Google Analytics, 百度统计'}"
ga_id: "${existing.ga_id || AD_CONFIG.ANALYTICS.GOOGLE_ANALYTICS}"
baidu_id: "${existing.baidu_id || AD_CONFIG.ANALYTICS.BAIDU_TONGJI}"
tracking_enabled: "${existing.tracking_enabled || true}"

ads_enabled: "${existing.ads_enabled || AD_CONFIG.ENABLE_ADS}"
ad_positions: "${existing.ad_positions || '头部,内容中部,底部'}"
ad_providers: "${existing.ad_providers || 'Google AdSense, 自定义广告'}"

viewport: "${existing.viewport || 'width=device-width, initial-scale=1.0'}"
mobile_friendly: "${existing.mobile_friendly || 'true'}"
language: "${existing.language || 'zh-CN'}"
charset: "${existing.charset || 'UTF-8'}"

content_version: "${(parseFloat(existing.content_version || '1.0.0') + 0.1).toFixed(1)}"
generated_by: "Obsidian SEO智能收集器"
seo_stability: "stable"
last_seo_update: "${existing.last_seo_update || '保持稳定'}"
stability_note: "内容增量更新${change>0?'+' : ''}${change}条，核心SEO元数据保持不变以确保排名稳定"
---
`;
    }

    // ==========================================
    // 广告函数（请把你原来的完整广告代码粘贴回来，这里只保留占位）
    // ==========================================
    function generateHeaderAd() {
        return `
<!-- 🎯 头部横幅广告位 -->
<div class="ad-container header-ad" style="max-width:728px;margin:0 auto 2rem;padding:1rem;background:linear-gradient(135deg,#f0f4ff 0%,#e6f7ff 100%);border:2px dashed #3b82f6;border-radius:12px;text-align:center;">
    <!-- 这里放你的完整广告代码 -->
    <p style="color:#64748b;">广告位招租 · 728×90</p>
</div>`;
    }

    function generateContentAd(i, total) {
        if (!AD_CONFIG.ENABLE_ADS || !AD_CONFIG.AD_POSITIONS.IN_CONTENT || total < 8 || i !== Math.floor(total/2)) return '';
        return `
<!-- 📱 内容中部广告位 -->
<div class="ad-container content-ad" style="margin:2rem 0;padding:1.5rem;background:linear-gradient(135deg,#fefce8 0%,#fef3c7 100%);border:1px solid #f59e0b;border-radius:10px;text-align:center;">
    <!-- 这里放你的完整广告代码 -->
    <p style="color:#d97706;">相关推荐广告位</p>
</div>`;
    }

    function generateFooterAd() {
        return `
<!-- 🏆 底部广告位 -->
<div class="ad-container footer-ad" style="margin:3rem 0 2rem;padding:1.5rem;background:linear-gradient(135deg,#f0fdf4 0%,#dcfce7 100%);border:1px solid #10b981;border-radius:12px;">
    <!-- 这里放你的完整广告代码 -->
    <p style="color:#059669;text-align:center;">更多推荐 · 联盟营销</p>
</div>`;
    }

    function generateAnalyticsCode(tagName, contentCount) {
        const safeTag = tagName.replace(/"/g, '\\"');
        return `
<!-- 📊 流量统计代码 -->
<script>
    window.dataLayer = window.dataLayer || [];
    function trackPageView() {
        const data = {page_title: document.title, page_path: location.pathname, page_location: location.href,
            timestamp: new Date().toISOString(), tag_name: "${safeTag}", content_count: ${contentCount}};
        dataLayer.push({event:'page_view', ...data});
    }
    function trackAdClick(type,pos){dataLayer.push({event:'ad_click',ad_type:type,ad_position:pos,timestamp:new Date().toISOString()});}
    function trackContentInteraction(type,idx){dataLayer.push({event:'content_interaction',interaction_type:type,quote_index:idx,tag_name:"${safeTag}",timestamp:new Date().toISOString()});}
    document.addEventListener('DOMContentLoaded',()=>{trackPageView();
        document.querySelectorAll('.ad-container').forEach(ad=>{ad.addEventListener('click',()=>{const t=ad.classList.contains('header-ad')?'header':ad.classList.contains('content-ad')?'content':'footer';trackAdClick('display_ad',t);});});
        document.querySelectorAll('article').forEach((a,i)=>{a.addEventListener('click',()=>{trackContentInteraction('quote_click',i);});});
    });
</script>
<script async src="https://www.googletagmanager.com/gtag/js?id=${AD_CONFIG.ANALYTICS.GOOGLE_ANALYTICS}"></script>
<script>window.dataLayer=window.dataLayer||[];function gtag(){dataLayer.push(arguments);}gtag('js',new Date());gtag('config','${AD_CONFIG.ANALYTICS.GOOGLE_ANALYTICS}');</script>
<script>var _hmt=_hmt||[];(function(){var hm=document.createElement("script");hm.src="https://hm.baidu.com/hm.js?${AD_CONFIG.ANALYTICS.BAIDU_TONGJI}";var s=document.getElementsByTagName("script")[0];s.parentNode.insertBefore(hm,s);})();</script>
`;
    }

    // ==========================================
    // 主逻辑：扫描 → 生成文件
    // ==========================================
    const allFiles = app.vault.getMarkdownFiles().filter(f => {
        if (EXCLUDE_FILES.has(f.name)) return false;
        if (f.path.includes(TARGET_FOLDER) && f.name.includes(HEADER_SUFFIX)) return false;
        if (EXCLUDE_FOLDERS.some(folder => f.path.includes(folder))) return false;
        return true;
    });

    let allTags = new Set(), tagContentMap = new Map(), totalScanned = 0;

    for (const file of allFiles) {
        totalScanned++;
        const content = await app.vault.read(file);
        const lines = content.split('\n');
        for (const rawLine of lines) {
            const line = rawLine.trim();
            if (!line || line.length < 10 || line.startsWith('```') || line.startsWith('---')) continue;
            const tags = line.match(/#[a-zA-Z0-9_\u4e00-\u9fa5][^#\s]*/g) || [];
            const validTags = tags.filter(t => {
                const name = t.slice(1);
                return name.length > 1 && !/^#[0-9]/.test(t) && !/^#[0-9a-fA-F]{3,6}$/.test(t);
            });
            if (validTags.length === 0) continue;
            let sentence = rawLine.replace(/^[>-]\s*/, '').replace(/#[^#\s]*/g, '').replace(/\s+/g, ' ').trim();
            if (sentence.length < 10) continue;

            validTags.forEach(t => {
                allTags.add(t);
                if (!tagContentMap.has(t)) tagContentMap.set(t, []);
                const arr = tagContentMap.get(t);
                if (!arr.some(item => item.sentence === sentence)) {
                    arr.push({sentence, sourceFile: file.basename, allTags: validTags.map(v=>v)});
                }
            });
        }
    }

    if (!app.vault.getAbstractFileByPath(TARGET_FOLDER)) await app.vault.createFolder(TARGET_FOLDER);

    const tagsArray = Array.from(allTags).sort();
    let created = 0, updated = 0, seoUpdated = 0, contentOnly = 0;

    for (const tag of tagsArray) {
        const items = tagContentMap.get(tag) || [];
        if (items.length === 0) continue;

        const tagName = tag.slice(1);
        const color = getColor(tagName);

        // 关键：使用安全文件名
        const safeTagName = safeFileName(tagName);
        const fileName = `${safeTagName}${HEADER_SUFFIX}.md`;
        const filePath = `${TARGET_FOLDER}/${fileName}`;
        const existingFile = app.vault.getAbstractFileByPath(filePath);

        let frontMatter = "", updateType = "new", existingFM = null;

        if (existingFile && SEO_CONFIG.PRESERVE_EXISTING_SEO) {
            const text = await app.vault.read(existingFile);
            existingFM = parseFrontMatter(text);
            const decision = existingFM ? shouldUpdateSEO(existingFM, items.length) : true;
            if (decision === false) {
                frontMatter = generatePreservedFrontMatter(existingFM, items);
                updateType = "content_only"; contentOnly++;
            } else {
                frontMatter = generateCompleteFrontMatter(tagName, items, decision || "new");
                updateType = decision || "new"; seoUpdated++;
            }
        } else {
            frontMatter = generateCompleteFrontMatter(tagName, items, "new");
            seoUpdated++;
        }

        // 生成页面内容（注意链接里也使用安全文件名）
        const sources = new Set(items.map(i=>i.sourceFile));
        let htmlContent = frontMatter +
            `<div style="font-family:-apple-system,BlinkMacSystemFont,'Segoe UI',Roboto,sans-serif;color:#1e293b;background:#f8fafc;min-height:100vh;padding:0;margin:0;">` +
            `<div style="background:${color};padding:3rem 1.5rem;text-align:center;color:white;">
                <h1 style="font-size:2rem;font-weight:700;margin:0 0 0.5rem;">关于「${tagName}」的名言金句</h1>
                <p style="opacity:0.9;margin:0;font-size:1rem;">收录 ${items.length} 条 · 来自 ${sources.size} 份笔记</p>
            </div>` +
            `<div style="max-width:700px;margin:0 auto;padding:2rem 1.5rem;">
                ${AD_CONFIG.ENABLE_ADS && AD_CONFIG.AD_POSITIONS.HEADER ? generateHeaderAd() : ''}
                <div style="display:grid;gap:1.25rem;">`;

        items.sort((a,b)=>a.sentence.localeCompare(b.sentence));
        for (let i = 0; i < items.length; i++) {
            const it = items[i];
            const firstTag = it.allTags[0]?.slice(1) || tagName;
            const cardColor = getColor(firstTag);
            const links = it.allTags.map(t => {
                const tn = t.slice(1);
                const safeTn = safeFileName(tn);
                const tc = getColor(tn);
                return `<a href="obsidian://open?vault=${encodeURIComponent(app.vault.getName())}&file=${encodeURIComponent(safeTn + HEADER_SUFFIX)}"
                    style="background:${tc}20;color:${tc};padding:0.2rem 0.6rem;border-radius:12px;margin-left:0.25rem;text-decoration:none;font-weight:500;"
                    onmouseover="this.style.background='${tc}';this.style.color='white'"
                    onmouseout="this.style.background='${tc}20';this.style.color='${tc}'">${t}</a>`;
            }).join('');

            htmlContent += `<article style="background:white;border-radius:8px;padding:1.5rem;border-left:4px solid ${cardColor};box-shadow:0 2px 8px rgba(0,0,0,0.04);">
                <div style="font-size:1.05rem;line-height:1.6;margin-bottom:1rem;">${it.sentence}</div>
                <div style="display:flex;justify-content:flex-end;align-items:center;gap:0.5rem;font-size:0.85rem;color:#64748b;margin-top:1rem;">${links}</div>
            </article>`;
            htmlContent += generateContentAd(i, items.length);
        }

        if (AD_CONFIG.ENABLE_ADS && AD_CONFIG.AD_POSITIONS.FOOTER) htmlContent += generateFooterAd();

        htmlContent += `</div>
            <div style="margin-top:3rem;padding:1.5rem;background:white;border-radius:8px;text-align:center;">
                <p style="color:#64748b;margin-bottom:1rem;">生成时间：${new Date().toLocaleString('zh-CN')}</p>
                <a href="obsidian://open?vault=${encodeURIComponent(app.vault.getName())}&file=${encodeURIComponent(TARGET_FOLDER+'/全站标签总览')}"
                   style="display:inline-block;background:${color};color:white;text-decoration:none;padding:0.5rem 1.25rem;border-radius:6px;font-weight:500;">返回总览</a>
            </div>
        </div></div>`;

        if (AD_CONFIG.ANALYTICS.CUSTOM_TRACKING) {
            htmlContent += generateAnalyticsCode(tagName, items.length);
        }

        // 创建或更新文件
        if (existingFile) {
            await app.vault.modify(existingFile, htmlContent);
            updated++;
        } else {
            await app.vault.create(filePath, htmlContent);
            created++;
        }
    }

    // 生成总览文件
    const overviewPath = `${TARGET_FOLDER}/全站标签总览.md`;
    const overviewContent = `# 🏷️ 全站标签总览 - SEO商业版

**扫描文件**：${totalScanned} 份  
**发现标签**：${allTags.size} 个  
**新建页面**：${created} 个  
**更新页面**：${updated} 个  
**仅内容更新**：${contentOnly} 个  
**SEO更新**：${seoUpdated} 个  
**生成时间**：${new Date().toLocaleString('zh-CN')}

## 标签列表
${tagsArray.map(tag => {
    const items = tagContentMap.get(tag) || [];
    const tagName = tag.slice(1);
    const safeName = safeFileName(tagName);
    return `- **${tag}** (${items.length}条) → [[${safeName}${HEADER_SUFFIX}]]`;
}).join('\n')}
`;
    const overviewFile = app.vault.getAbstractFileByPath(overviewPath);
    if (overviewFile) await app.vault.modify(overviewFile, overviewContent);
    else await app.vault.create(overviewPath, overviewContent);

    const runTime = ((Date.now() - startTime)/1000).toFixed(1);
    tR += `# ✅ SEO商业版生成完成！

**耗时**：${runTime} 秒  
**新建**：${created} 个 **更新**：${updated} 个 **仅内容更新**：${contentOnly} 个

> 已自动处理文件名中的非法字符（* " \\ / < > : | ?），不会再报错了～
`;

} catch (err) {
    console.error(err);
    tR += `# ❌ 执行出错\n\`\`\`\n${err.message}\n\`\`\`\n请检查配置后重试...`;
} finally {
    cleanup();
}
%>