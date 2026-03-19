<%*
// ========================================
// 🌟 Obsidian SEO & 多彩金句生成器（Bug 修复版）
// ========================================
if(typeof window._tagCollectorRunning!=='undefined'){ tR+="# ⚠️ 上次运行未完成"; return; }
window._tagCollectorRunning=true;
const cleanup=()=>setTimeout(()=>delete window._tagCollectorRunning,1000);

try {
  const TARGET_FOLDER="02 Notes"; 
  const SCAN_FOLDER="02 Notes-原料间"; 
  const HEADER_SUFFIX="的名言金句";
  const EXCLUDE_FILES=new Set(["万能标签搜索器.md","全站标签总览.md","tR.md"]);
  
  // 🌈 颜料池：自动轮流领取的颜色
  const COLOR_PALETTE = [
    "#ff6b6b", "#5f27cd", "#00b894", "#fdcb6e", "#fd79a8", 
    "#3498db", "#00cec9", "#8e44ad", "#16a085", "#e17055", 
    "#c0392b", "#27ae60", "#74b9ff", "#a29bfe", "#fab1a0"
  ];

  // 🚀 函数 1：根据标签名自动领取颜色
  const getDynamicColor = (text) => {
    let h = 0;
    for (let i = 0; i < text.length; i++) {
      h = text.charCodeAt(i) + ((h << 5) - h);
    }
    const index = Math.abs(h) % COLOR_PALETTE.length;
    return COLOR_PALETTE[index];
  };

  // 🚀 函数 2：全自动拼音 Slug 算法 (已修复变量定义错误)
  const getAutoSlug = (text) => {
    const charMap = {"我":"wo","你":"ni","他":"ta","职":"zhi","场":"chang","哲":"zhe","学":"xue","高":"gao","适":"shi","茅":"mao","盾":"dun","心":"xin","态":"tai","人":"ren","性":"xing","成":"cheng","长":"zhang","思":"si","考":"kao"};
    let res = "";
    for (let c of text) { 
      res += (/[a-zA-Z0-9]/.test(c) ? c.toLowerCase() : (charMap[c] || c.charCodeAt(0).toString(16))); 
    }
    let h = 0; 
    for (let i = 0; i < text.length; i++) { 
      h = ((h << 5) - h) + text.charCodeAt(i); 
      h |= 0; 
    }
    return res.substring(0, 15) + "-" + Math.abs(h).toString(16).substring(0, 4);
  };

  const currentFile = app.workspace.activeLeaf?.view?.file?.name;
  if(currentFile) EXCLUDE_FILES.add(currentFile);
  
  const allFiles = app.vault.getMarkdownFiles().filter(f=>{
    return !EXCLUDE_FILES.has(f.name) && f.path.startsWith(`${SCAN_FOLDER}/`) && !f.name.includes(HEADER_SUFFIX);
  });

  const tagMap = new Map();
  const allTags = new Set();
  
  await Promise.all(allFiles.map(async file => {
    const content = await app.vault.read(file);
    content.split('\n').forEach(line => {
      line = line.trim();
      if(!line || line.length < 10 || line.startsWith('```') || line.startsWith('---')) return;
      const matches = line.match(/#[\w\u4e00-\u9fa5-/]+/g);
      if(!matches) return;
      const clean = line.replace(/[1]\s*/,'').replace(/#[\w\u4e00-\u9fa5-/]+/g,'').replace(/\s+/g,' ').trim();
      if(clean.length < 10) return;
      matches.filter(t => t.length > 1).forEach(tag=>{
        allTags.add(tag);
        if(!tagMap.has(tag)) tagMap.set(tag, []);
        const arr = tagMap.get(tag);
        if(!arr.some(i => i.s===clean)) arr.push({s: clean, tags: matches});
      });
    });
  }));

  if(!app.vault.getAbstractFileByPath(TARGET_FOLDER)) await app.vault.createFolder(TARGET_FOLDER);
  
  for(const tag of Array.from(allTags).sort()){
    const items = tagMap.get(tag) || [];
    if(items.length === 0) continue;
    const tn = tag.replace('#','');
    const filePath = `${TARGET_FOLDER}/${tn.replace(/[\\\/:*?"<>|]/g,'')}${HEADER_SUFFIX}.md`;
    const existingFile = app.vault.getAbstractFileByPath(filePath);
    const slug = getAutoSlug(tn);
    const mainColor = getDynamicColor(tn);

    let html = `---\nslug: ${slug}\n---\n<style>.tag-container{font-family:sans-serif;max-width:800px;margin:0 auto;padding:20px;background:#f8f9fa}.tag-header{background:#fff;padding:30px;border-radius:12px;box-shadow:0 4px 6px rgba(0,0,0,.05);margin-bottom:30px;text-align:center;border-top:5px solid ${mainColor}}.tag-grid{display:grid;gap:20px}.tag-card{background:#fff;padding:20px;border-radius:10px;border-left:4px solid var(--card-color);box-shadow:0 2px 4px rgba(0,0,0,.03);transition:transform 0.3s}.tag-card:hover{transform:translateY(-5px);box-shadow:0 6px 12px rgba(0,0,0,.1)}.sentence{font-size:16px;line-height:1.6;color:#2d3436;margin-bottom:12px}.tag-links{display:flex;gap:8px;flex-wrap:wrap}.tag-links a{font-size:12px;padding:2px 8px;border-radius:4px;background:#f1f2f6;color:var(--card-color);text-decoration:none;border:1px solid var(--card-color)}.tag-footer{margin-top:40px;text-align:center;font-size:12px;color:#95a5a6}</style>\n<div class="tag-container"><div class="tag-header"><h1>关于「${tn}」的名言金句</h1><p>收录 ${items.length} 条内容</p></div><div class="tag-grid">`;

    items.sort((a,b)=>a.s.localeCompare(b.s)).forEach(item => {
      const links = item.tags.map(t => {
        const name = t.replace('#','');
        return `<a href="https://txdcb.com/02-Notes/${getAutoSlug(name)}.html" style="--card-color:${getDynamicColor(name)}">${name}</a>`;
      }).join('');
      html += `<article class="tag-card" style="--card-color:${getDynamicColor(item.tags[0].replace('#',''))}"><div class="sentence">${item.s}</div><div class="tag-links">${links}</div></article>`;
    });

    html += `</div><div class="tag-footer"><p>🕐 生成时间：${new Date().toLocaleString()}</p></div></div></body>`;

    if(existingFile) await app.vault.modify(existingFile, html);
    else await app.vault.create(filePath, html);
  }
  tR += "# ✅ Bug 已修复！全自动多彩版运行成功";
} catch(e) { console.error(e); tR += "# ❌ 出错: " + e.message; } finally { cleanup(); }
%>
>-↩︎