<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>思屑录 | 思想的碎片与火花</title>
    <style>
        /* 基础样式与字体 */
        body {
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif;
            line-height: 1.7;
            color: #333;
            background-color: #f8f9fa;
            max-width: 800px;
            margin: 0 auto;
            padding: 2rem 1rem;
        }

        /* 页头样式 */
        .header {
            text-align: center;
            margin-bottom: 3rem;
            border-bottom: 1px solid #eaeaea;
            padding-bottom: 1.5rem;
        }
        .site-title {
            font-size: 2.5rem;
            font-weight: 300;
            margin: 0;
            color: #2c3e50;
        }
        .site-description {
            font-size: 1.1rem;
            color: #7f8c8d;
            margin-top: 0.5rem;
        }

        /* 标签过滤器样式 */
        .tag-filters {
            text-align: center;
            margin-bottom: 2rem;
        }
        .filter-btn {
            display: inline-block;
            padding: 0.4rem 0.8rem;
            margin: 0.3rem;
            background: #fff;
            border: 1px solid #ddd;
            border-radius: 20px;
            cursor: pointer;
            font-size: 0.9rem;
            color: #555;
            transition: all 0.2s ease;
        }
        .filter-btn:hover, .filter-btn.active {
            background-color: #2c3e50;
            color: white;
            border-color: #2c3e50;
        }

        /* 思想卡片样式 - 核心部分 */
        .thoughts-container {
            display: grid;
            gap: 2rem;
        }
        .thought-card {
            background: #fff;
            padding: 2rem;
            border-radius: 8px;
            box-shadow: 0 2px 10px rgba(0, 0, 0, 0.05);
            border-left: 4px solid #3498db; /* 默认边框色 */
            transition: transform 0.2s ease, box-shadow 0.2s ease;
            cursor: pointer;
        }
        .thought-card:hover {
            transform: translateY(-3px);
            box-shadow: 0 5px 20px rgba(0, 0, 0, 0.1);
        }
        /* 不同标签的卡片边框色 (示例) */
        .thought-card[data-tags~="职场"] { border-left-color: #e74c3c; }
        .thought-card[data-tags~="哲学"] { border-left-color: #9b59b6; }
        .thought-card[data-tags~="社会观察"] { border-left-color: #1abc9c; }
        .thought-card[data-tags~="个人成长"] { border-left-color: #f39c12; }

        .card-content {
            font-size: 1.2rem;
            margin: 0;
            color: #2c3e50;
        }
        .card-meta {
            margin-top: 1rem;
            font-size: 0.9rem;
            color: #95a5a6;
        }
        .card-tags {
            display: flex;
            flex-wrap: wrap;
            gap: 0.5rem;
            margin-top: 0.5rem;
        }
        .card-tag {
            background: #ecf0f1;
            padding: 0.2rem 0.6rem;
            border-radius: 12px;
            font-size: 0.8rem;
            color: #7f8c8d;
        }

        /* 无结果提示 */
        .no-results {
            text-align: center;
            padding: 3rem;
            color: #95a5a6;
            display: none; /* 默认隐藏 */
        }
    </style>
</head>
<body>
    <!-- 页头 -->
    <header class="header">
        <h1 class="site-title">思屑录</h1>
        <p class="site-description">收集阅读与生活中，那些一闪而过却值得珍藏的思想火花。</p>
    </header>

    <!-- 标签过滤器 -->
    <nav class="tag-filters">
        <button class="filter-btn active" data-filter="all">全部</button>
        <button class="filter-btn" data-filter="职场"># 职场</button>
        <button class="filter-btn" data-filter="哲学"># 哲学</button>
        <button class="filter-btn" data-filter="社会观察"># 社会观察</button>
        <button class="filter-btn" data-filter="个人成长"># 个人成长</button>
        <button class="filter-btn" data-filter="犀利吐槽"># 犀利吐槽</button>
    </nav>

    <!-- 思想卡片容器 -->
    <main class="thoughts-container">
        <!-- 卡片 1 -->
        <article class="thought-card" data-tags="哲学 个人成长">
            <p class="card-content">需求是发明之母。</p>
            <div class="card-meta">
                <div class="card-tags">
                    <span class="card-tag">哲学</span>
                    <span class="card-tag">个人成长</span>
                </div>
            </div>
        </article>

        <!-- 卡片 2 -->
        <article class="thought-card" data-tags="个人成长">
            <p class="card-content">强者自强，从来不是靠对方衰落而强，是自身实力上去了那才是强。</p>
            <div class="card-meta">
                <div class="card-tags">
                    <span class="card-tag">个人成长</span>
                </div>
            </div>
        </article>

        <!-- 卡片 3 -->
        <article class="thought-card" data-tags="职场 AI 犀利吐槽">
            <p class="card-content">人肉牛马想让AI当牛马。老板却不这么想，他想的是，都是牛马，为什么不选便宜的那个？</p>
            <div class="card-meta">
                <div class="card-tags">
                    <span class="card-tag">职场</span>
                    <span class="card-tag">AI</span>
                    <span class="card-tag">犀利吐槽</span>
                </div>
            </div>
        </article>

        <!-- 卡片 4 -->
        <article class="thought-card" data-tags="社会观察 犀利吐槽">
            <p class="card-content">不論什麼地方的人，對不配合人家文化，規則和法律就不用太親切對待，惡心死人！幫了牠們還會反咬你一口！</p>
            <div class="card-meta">
                <div class="card-tags">
                    <span class="card-tag">社会观察</span>
                    <span class="card-tag">犀利吐槽</span>
                </div>
            </div>
        </article>

        <!-- 卡片 5 -->
        <article class="thought-card" data-tags="哲学">
            <p class="card-content">道德是自律而不是他律。道德是要求自己的不是要求他人的。不要道德完美主义，完美你自己就可以了。</p>
            <div class="card-meta">
                <div class="card-tags">
                    <span class="card-tag">哲学</span>
                </div>
            </div>
        </article>

        <!-- 卡片 6 -->
        <article class="thought-card" data-tags="社会观察 职场">
            <p class="card-content">表演性学习。</p>
            <div class="card-meta">
                <div class="card-tags">
                    <span class="card-tag">社会观察</span>
                    <span class="card-tag">职场</span>
                </div>
            </div>
        </article>
    </main>

    <!-- 无内容提示 -->
    <div class="no-results" id="noResults">
        <p>没有找到匹配的条目。</p>
    </div>

    <script>
        // 简单的标签过滤功能
        document.addEventListener('DOMContentLoaded', function() {
            const filterButtons = document.querySelectorAll('.filter-btn');
            const thoughtCards = document.querySelectorAll('.thought-card');
            const noResults = document.getElementById('noResults');

            filterButtons.forEach(button => {
                button.addEventListener('click', function() {
                    // 更新按钮激活状态
                    filterButtons.forEach(btn => btn.classList.remove('active'));
                    this.classList.add('active');

                    const filterValue = this.getAttribute('data-filter');
                    let visibleCount = 0;

                    // 过滤卡片
                    thoughtCards.forEach(card => {
                        const cardTags = card.getAttribute('data-tags');
                        if (filterValue === 'all' || cardTags.includes(filterValue)) {
                            card.style.display = 'block';
                            visibleCount++;
                        } else {
                            card.style.display = 'none';
                        }
                    });

                    // 显示/隐藏“无结果”提示
                    noResults.style.display = visibleCount === 0 ? 'block' : 'none';
                });
            });
        });
    </script>
</body>
</html>