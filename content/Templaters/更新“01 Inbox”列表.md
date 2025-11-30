<%*
// 这里是魔法咒语，可以自动列出文件
const inboxFiles = app.vault.getFiles()
    .filter(f => f.path.includes("01 Inbox/"))
    .sort((a, b) => b.stat.ctime - a.stat.ctime)
    .slice(0, 10);

// 开始制作漂亮的列表
let content = `## 📥 我的收集箱最新文件\n\n**🕒 更新时间：** ${new Date().toLocaleString()}\n\n`;

inboxFiles.forEach(file => {
    const name = file.basename;
    const date = new Date(file.stat.ctime).toLocaleDateString();
    content += `- [[${name}]] - 📅 ${date}\n`;
});

content += `\n**📊 文件总数：** ${inboxFiles.length} 个文件\n\n`;
content += `> 💡 这个列表是自动生成的魔法哦！`;

// 把制作好的内容保存到新文件
const targetFile = "收集箱文件列表.md";
const target = app.vault.getAbstractFileByPath(targetFile);

if (target) {
    await app.vault.modify(target, content);
} else {
    await app.vault.create(targetFile, content);
}

// 显示成功消息
tR += `🎉 魔法成功！已更新 ${targetFile}\n`;
tR += `📁 共找到 ${inboxFiles.length} 个文件`;
%>