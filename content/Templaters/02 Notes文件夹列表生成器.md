<%*
// ==========================================
// 万能文件夹列表生成器 - 超级魔法咒语
// 小朋友可以修改下面的变量来适应不同文件夹
// ==========================================

// 🎯 在这里设置你的目标文件夹（可以修改！）
const TARGET_FOLDER = "02 Notes";  // 改成你想要的文件夹名
const OUTPUT_FILE = "02 Notes文件列表.md";  // 改成你想要的输出文件名
const FOLDER_ICON = "📥";  // 改成你喜欢的图标
const FOLDER_NAME = "02 Notes";  // 改成文件夹的中文名

// 🪄 魔法开始（这部分不用改）
const files = app.vault.getFiles()
    .filter(f => f.path.includes(TARGET_FOLDER + "/"))
    .sort((a, b) => b.stat.ctime - a.stat.ctime)
    .slice(0, 15);  // 显示最多15个文件

// 制作漂亮的列表
let content = `## ${FOLDER_ICON} 我的${FOLDER_NAME}最新文件\n\n`;
content += `**🕒 更新时间：** ${new Date().toLocaleString()}\n\n`;

if (files.length === 0) {
    content += `> 😴 ${FOLDER_NAME}里还没有文件呢，快去添加一些吧！\n\n`;
} else {
    files.forEach(file => {
        const name = file.basename;
        const date = new Date(file.stat.ctime).toLocaleDateString();
        content += `- [[${name}]] - 📅 ${date}\n`;
    });
}

content += `\n**📊 文件总数：** ${files.length} 个文件\n\n`;
content += `> 💡 这个列表是自动生成的魔法哦！每次运行都会更新！`;

// 保存到文件
const target = app.vault.getAbstractFileByPath(OUTPUT_FILE);
if (target) {
    await app.vault.modify(target, content);
} else {
    await app.vault.create(OUTPUT_FILE, content);
}

// 显示成功消息
tR += `🎉 魔法成功！\n`;
tR += `📁 已更新: ${OUTPUT_FILE}\n`;
tR += `📂 扫描文件夹: ${TARGET_FOLDER}\n`;
tR += `📄 找到文件: ${files.length} 个\n\n`;
tR += `💡 提示：修改模板开头的变量可以用于不同文件夹！`;
%>