---
title: "从零开始的Windows专属个人博客——搜索功能"
date: 2026-06-28T15:00:00+08:00
draft: false
tags: ["Hugo", "博客", "搜索", "教程"]
series: ["从零开始的Windows专属个人博客"]
---

# 从零开始的Windows专属个人博客——搜索功能

> 想象一下：你的博客已经有几十篇文章，用户想找某个特定内容，结果只能一个个翻目录...这时候，一个好用的搜索功能就像给你的博客装上了"Ctrl+F"，让用户分分钟找到想要的内容！今天我们就来给博客加上搜索功能！

## 一、为什么需要搜索功能？

### 用户场景

```
❌ 没有搜索：用户找文章 → 翻目录 → 翻标签 → 放弃离开
✅ 有搜索：用户找文章 → 输入关键词 → 立刻找到 → 开心阅读
```

### 搜索的价值

| 价值 | 说明 |
|------|------|
| 提升体验 | 用户快速找到想要的内容 |
| 增加停留 | 搜索后往往会阅读多篇文章 |
| 提高回访 | 好用的搜索让用户愿意再来 |

## 二、实现原理

PaperMod 主题的搜索基于 **JSON 索引** 实现：

```
┌─────────────┐    生成    ┌─────────────┐    加载    ┌─────────────┐
│  所有文章   │ ────────→ │ index.json │ ────────→ │ 搜索页面   │
└─────────────┘            └─────────────┘            └─────────────┘
```

### 核心原理

1. **生成索引**：Hugo 构建时自动生成 `index.json`，包含所有文章标题、内容、标签
2. **加载索引**：搜索页面加载时获取这个 JSON 文件
3. **本地搜索**：JavaScript 在浏览器中进行关键词匹配
4. **实时显示**：输入关键词后立即显示匹配结果

### 优点

- ✅ **无需后端**：纯静态实现，不占用服务器资源
- ✅ **响应快速**：所有数据已在本地，搜索无延迟
- ✅ **隐私友好**：搜索不经过第三方服务器
- ✅ **易于部署**：和其他静态文件一样部署

## 三、开启方法

### 第一步：修改配置文件

打开 `hugo.yml`，添加搜索配置：

```yaml
params:
  ShowSearch: true           # 启用搜索功能
  SearchPageSize: 10       # 搜索结果每页显示数量
```

同时修改输出格式，添加 JSON 输出：

```yaml
outputs:
  home:
    - HTML
    - JSON    # 添加这一行，生成搜索索引
  page:
    - HTML
  section:
    - HTML
```

### 第二步：创建搜索页面模板

在 `layouts/` 目录下创建 `search.html` 文件：

```html
{{- define "main" -}}
<!-- 搜索页面 HTML 结构 -->
<div class="search-container">
    <h1>🔍 搜索文章</h1>
    <input type="text" id="search-input" placeholder="输入关键词搜索...">
    <div id="search-results"></div>
</div>

<script>
// 加载搜索索引
async function loadSearchIndex() {
    const response = await fetch('/index.json');
    const data = await response.json();
    return data;
}

// 搜索函数
function search(query) {
    const results = searchIndex.filter(item => {
        return item.title.includes(query) ||
               item.content.includes(query);
    });
    displayResults(results);
}

// 显示结果
function displayResults(results) {
    // 渲染搜索结果
}

// 初始化
loadSearchIndex();
</script>
{{- end -}}
```

### 第三步：导航菜单已有搜索入口

我们的博客导航栏已经有搜索链接了：

```yaml
menu:
  main:
    - name: 搜索
      url: /search/
      weight: 4
```

## 四、搜索页面效果

### 界面组成

```
┌────────────────────────────────────────┐
│         🔍 搜索文章                      │
│   输入关键词，找到你想要的内容            │
├────────────────────────────────────────┤
│  ┌──────────────────────────────────┐   │
│  │ 🔍 输入关键词搜索...              │   │
│  └──────────────────────────────────┘   │
├────────────────────────────────────────┤
│  ┌──────────────────────────────────┐   │
│  │ 📅 2026-06-28                    │   │
│  │ 留心语博客正式上线                │   │
│  │ 今天是个特殊的日子，我们的博客...   │   │
│  │ [博客] [教程]                     │   │
│  └──────────────────────────────────┘   │
│  ┌──────────────────────────────────┐   │
│  │ 📅 2026-06-27                    │   │
│  │ Git 操作手册                     │   │
│  │ 这是一份完整的 Git 操作指南...    │   │
│  │ [Git] [工具]                     │   │
│  └──────────────────────────────────┘   │
└────────────────────────────────────────┘
```

### 交互特性

- ⌨️ **实时搜索**：输入时自动搜索，无需点击按钮
- 🎯 **高亮匹配**：标题和内容中的关键词高亮显示
- 🏷️ **标签过滤**：支持通过标签搜索
- 📅 **日期显示**：每条结果显示发布日期
- 📱 **响应式**：完美适配手机和电脑

## 五、进阶优化（可选）

### 美化搜索结果

如果想让搜索结果更美观，可以添加 CSS 样式：

```css
.search-result-item {
    padding: 20px;
    margin-bottom: 15px;
    background: white;
    border-radius: 10px;
    box-shadow: 0 2px 10px rgba(0,0,0,0.1);
    transition: transform 0.2s;
}

.search-result-item:hover {
    transform: translateY(-3px);
    box-shadow: 0 5px 20px rgba(0,0,0,0.15);
}
```

### 添加快捷键支持

```javascript
// 按 / 键快速聚焦搜索框
document.addEventListener('keydown', (e) => {
    if (e.key === '/') {
        e.preventDefault();
        searchInput.focus();
    }
});
```

### 添加搜索建议

```javascript
// 输入时显示搜索建议
searchInput.addEventListener('input', (e) => {
    const query = e.target.value;
    if (query.length >= 2) {
        showSuggestions(query);
    }
});
```

## 六、常见问题

### Q: 搜索不到最新文章？

**A**: 确保文章 `draft: false`，Hugo 构建时会自动排除草稿。

### Q: 搜索结果不准确？

**A**: 检查文章内容是否在 Front Matter 的 `content` 字段中。

### Q: 移动端体验不好？

**A**: 搜索页面已使用响应式设计，确保 CSS 中有 `@media` 适配。

## 七、总结

```
搜索功能 = 配置文件 + 模板 + JSON索引
```

| 步骤 | 操作 | 难度 |
|------|------|------|
| 1 | 修改 hugo.yml | ⭐ |
| 2 | 创建 search.html | ⭐⭐ |
| 3 | 测试搜索功能 | ⭐ |

**一句话概括**：搜索功能让博客从"图书馆"变成"Google"！

---

*下一篇我们将介绍如何添加社交图标，让你的博客更具个性！*

<script src="https://utteranc.es/client.js"
        repo="aasd9022/aasd9022.github.io"
        issue-term="title"
        theme="github-light"
        crossorigin="anonymous"
        async>
</script>