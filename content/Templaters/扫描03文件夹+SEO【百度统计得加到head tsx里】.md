<%*
// ==========================================
// 🌟 Obsidian SEO & 标签金句生成器（含百度统计，网页路径版 for Quartz）
// ==========================================

if(typeof window._tagCollectorRunning!=='undefined'){ tR+="# ⚠️ 上次运行未完成"; return; }
window._tagCollectorRunning=true;
const cleanup=()=>setTimeout(()=>delete window._tagCollectorRunning,1000);

try {
  const TARGET_FOLDER="02 Notes"; // 输出文件夹
  const SCAN_FOLDER="02 Notes-原料间"; // 只扫描这个文件夹
  const HEADER_SUFFIX="的名言金句";
  const EXCLUDE_FILES=new Set(["万能标签搜索器.md","全站标签总览.md","tR.md"]);

  const COLORS={
    "职场":"#ff6b6b","哲学":"#5f27cd","社会观察":"#00b894","个人成长":"#fdcb6e",
    "犀利吐槽":"#fd79a8","AI":"#3498db","思考":"#00cec9","竞争哲学":"#8e44ad",
    "心态调整":"#16a085","成功学":"#e17055","权力结构":"#c0392b","人性":"#27ae60"
  };
  const DEFAULT_COLOR="#667eea";
  const getColor=(tag)=>COLORS[tag]||DEFAULT_COLOR;

  const currentFile = app.workspace.activeLeaf?.view?.file?.name;
  if(currentFile) EXCLUDE_FILES.add(currentFile);

  // 只扫描 SCAN_FOLDER 下的 Markdown 文件
  const allFiles = app.vault.getMarkdownFiles().filter(f=>{
    if(EXCLUDE_FILES.has(f.name)) return false;
    if(!f.path.startsWith(`${SCAN_FOLDER}/`)) return false;
    if(f.name.includes(HEADER_SUFFIX)) return false;
    return true;
  });

  const allTags = new Set();
  const tagContentMap = new Map();

  await Promise.all(allFiles.map(async file => {
    const content = await app.vault.read(file);
    content.split('\n').forEach(rawLine => {
      const line = rawLine.trim();
      if(!line || line.length < 10 || line.startsWith('```') || line.startsWith('---')) return;
      const tagMatches = line.match(/#[\w\u4e00-\u9fa5\-\/]+/g);
      if(!tagMatches) return;
      const cleanSentence = line.replace(/^[>-]\s*/,'').replace(/#[\w\u4e00-\u9fa5\-\/]+/g,'').replace(/\s+/g,' ').trim();
      if(cleanSentence.length < 10) return;
      const validTags = tagMatches.filter(t => t.length > 1);
      if(validTags.length === 0) return;

      validTags.forEach(tag=>{
        allTags.add(tag);
        if(!tagContentMap.has(tag)) tagContentMap.set(tag, []);
        const arr = tagContentMap.get(tag);
        if(!arr.some(i => i.sentence===cleanSentence && JSON.stringify(i.allTags)===JSON.stringify(validTags)))
          arr.push({sentence: cleanSentence, sourceFile: file.basename, allTags: [...validTags]});
      });
    });
  }));

  if(!app.vault.getAbstractFileByPath(TARGET_FOLDER)) await app.vault.createFolder(TARGET_FOLDER);

  const WEB_ROOT = 'https://txdcb.com/02-Notes/';

  for(const tag of Array.from(allTags).sort()){
    const contents = tagContentMap.get(tag) || [];
    if(contents.length === 0) continue;

    const tagName = tag.replace('#','');
    const safeFileName = tagName.replace(/[\\\/:*?"<>|]/g,''); 
    const filePath = `${TARGET_FOLDER}/${safeFileName}${HEADER_SUFFIX}.md`;
    const existingFile = app.vault.getAbstractFileByPath(filePath);
    const color = getColor(tagName);

    // ✅ SEO Meta 动态生成
    const metaTitle = `关于「${tagName}」的名言金句 - TXDCB`;
    const metaDesc = `收录 ${contents.length} 条关于${tagName}的精选金句，覆盖职场、哲学、心态调整等领域，助力个人成长与思考。`;
    const metaKeywords = [tagName,...contents.flatMap(i=>i.allTags.map(t=>t.replace('#','')))].join(',');

    let html = `
<head>
  <title>${metaTitle}</title>
  <meta name="description" content="${metaDesc}">
  <meta name="keywords" content="${metaKeywords}">
  <script type="application/ld+json">
  {
    "@context": "https://schema.org",
    "@type": "ItemList",
    "name": "${metaTitle}",
    "itemListElement": [
      ${contents.map((i,k)=>`{
        "@type":"ListItem",
        "position":${k+1},
        "name":"${i.sentence}",
        "url":"${WEB_ROOT}${encodeURIComponent(safeFileName)}.html"
      }`).join(',')}
    ]
  }
  </script>



</head>
<body>
<div class="tag-header" style="background:${color}">
  <h1>关于「${tagName}」的名言金句</h1>
  <p>收录 ${contents.length} 条内容</p>
</div>
<div class="tag-content-wrapper"><div class="tag-content">`;

    contents.sort((a,b)=>a.sentence.localeCompare(b.sentence));

    for(const item of contents){
      const tagLinks = item.allTags.map(t=>{
        const tn = t.replace('#','');
        const fileName = encodeURIComponent(`${tn}${HEADER_SUFFIX}.html`);
        return `<a href="${WEB_ROOT}${fileName}" style="--card-color:${getColor(tn)}">${tn}</a>`;
      }).join('');
      html += `<article class="tag-card" style="--card-color:${getColor(item.allTags[0]?.replace('#','')||tagName)}">
                 <div class="sentence">${item.sentence}</div>
                 <div class="tag-links">${tagLinks}</div>
               </article>`;
    }

    html += `</div><div class="tag-footer"><p>🕐 生成时间：${new Date().toLocaleString()}</p></div></div></body>`;

    if(existingFile) await app.vault.modify(existingFile, html);
    else await app.vault.create(filePath, html);
  }

  tR += "# ✅ 标签金句收集完成（含百度统计）";

}catch(e){ console.error(e); tR += `# ❌ 出错: ${e.message}`; }
finally{ cleanup(); }
%>
