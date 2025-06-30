<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>诗词社投稿评分系统</title>
    <style>
        * { box-sizing: border-box; margin: 0; padding: 0; font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif; }
        body { background-color: #f5f5f5; color: #333; line-height: 1.6; }
        .container { max-width: 800px; margin: 0 auto; padding: 20px; }
        .header { background-color: #8e44ad; color: white; padding: 15px 20px; border-radius: 8px 8px 0 0; margin-bottom: 20px; text-align: center; }
        .tab-container { display: flex; margin-bottom: 20px; border-bottom: 1px solid #ddd; }
        .tab { padding: 10px 20px; cursor: pointer; background-color: #f1f1f1; border: 1px solid #ddd; border-bottom: none; border-radius: 5px 5px 0 0; margin-right: 5px; }
        .tab.active { background-color: white; border-bottom: 1px solid white; margin-bottom: -1px; }
        .content { display: none; background-color: white; padding: 20px; border-radius: 0 0 8px 8px; box-shadow: 0 2px 5px rgba(0,0,0,0.1); }
        .content.active { display: block; }
        .form-group { margin-bottom: 15px; }
        label { display: block; margin-bottom: 5px; font-weight: bold; }
        input[type="text"], input[type="password"], input[type="number"], textarea, select { width: 100%; padding: 10px; border: 1px solid #ddd; border-radius: 4px; font-size: 16px; }
        textarea { min-height: 100px; resize: vertical; }
        button { background-color: #8e44ad; color: white; border: none; padding: 10px 15px; border-radius: 4px; cursor: pointer; font-size: 16px; }
        button:hover { background-color: #732d91; }
        .poem-list { margin-top: 20px; }
        .poem-item { border: 1px solid #ddd; padding: 15px; margin-bottom: 15px; border-radius: 5px; background-color: #f9f9f9; }
        .poem-title { font-weight: bold; font-size: 18px; margin-bottom: 5px; color: #8e44ad; }
        .poem-author { color: #666; margin-bottom: 10px; }
        .poem-content { white-space: pre-line; margin-bottom: 10px; }
        .poem-type { display: inline-block; background-color: #e0d6e9; padding: 2px 8px; border-radius: 10px; font-size: 12px; margin-bottom: 10px; }
        .score-input { width: 80px; display: inline-block; margin-right: 10px; }
        .modal { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background-color: rgba(0,0,0,0.5); z-index: 1000; }
        .modal-content { background-color: white; margin: 10% auto; padding: 20px; width: 80%; max-width: 500px; border-radius: 8px; }
        .close-modal { float: right; cursor: pointer; font-size: 24px; }
        .admin-table { width: 100%; border-collapse: collapse; margin-top: 20px; }
        .admin-table th, .admin-table td { border: 1px solid #ddd; padding: 8px; text-align: left; }
        .admin-table th { background-color: #f2f2f2; }
        .admin-table tr:nth-child(even) { background-color: #f9f9f9; }
        .error-message { color: #e74c3c; margin-top: 5px; }
        .success-message { color: #27ae60; margin-top: 5px; }
        .activity-status { display: inline-block; padding: 3px 8px; border-radius: 3px; font-size: 12px; font-weight: bold; }
        .status-not-started { background-color: #f39c12; color: white; }
        .status-ongoing { background-color: #2ecc71; color: white; }
        .status-ended { background-color: #e74c3c; color: white; }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>诗词社投稿评分系统</h1>
        </div>

        <!-- 入口选择 -->
        <div id="entry-selection" class="content active">
            <h2>请选择您的身份</h2>
            <div class="form-group">
                <button id="user-entry">用户入口</button>
                <button id="admin-entry">管理员入口</button>
                <button id="president-entry">社长入口</button>
            </div>
        </div>

        <!-- 用户主界面 -->
        <div id="user-interface" class="content">
            <div class="tab-container">
                <div class="tab active" data-tab="submit-poem">投稿</div>
                <div class="tab" data-tab="view-ranking">排名</div>
            </div>
            
            <div id="submit-poem" class="content active">
                <h2>诗词投稿</h2>
                <div id="activity-info"></div>
                <form id="poem-form">
                    <div class="form-group">
                        <label for="nickname">飞花昵称</label>
                        <input type="text" id="nickname" required>
                    </div>
                    <div class="form-group">
                        <label for="title">题目</label>
                        <input type="text" id="title" required>
                    </div>
                    <div class="form-group">
                        <label for="poem-type">诗词类型</label>
                        <select id="poem-type" required>
                            <option value="">请选择</option>
                            <option value="近体诗">近体诗</option>
                            <option value="古体诗">古体诗</option>
                            <option value="现代诗">现代诗</option>
                            <option value="打油诗">打油诗</option>
                            <option value="词">词</option>
                        </select>
                    </div>
                    <div class="form-group">
                        <label for="content">内容</label>
                        <textarea id="content" required></textarea>
                    </div>
                    <button type="submit">提交投稿</button>
                </form>
            </div>
            
            <div id="view-ranking" class="content">
                <h2>作品排名</h2>
                <div id="ranking-info"></div>
                <div id="poem-ranking" class="poem-list"></div>
            </div>
        </div>

        <!-- 管理员主界面 -->
        <div id="admin-interface" class="content">
            <h2>管理员评分</h2>
            <div id="admin-activity-info"></div>
            <div id="scoring-standard"></div>
            <div id="poems-to-score" class="poem-list"></div>
        </div>
    <!-- 社长主界面 -->
        <div id="president-interface" class="content">
            <div class="tab-container">
                <div class="tab active" data-tab="manage-admins">管理员管理</div>
                <div class="tab" data-tab="set-standard">评分标准</div>
                <div class="tab" data-tab="manage-activity">活动管理</div>
                <div class="tab" data-tab="view-scores">评分查看</div>
            </div>
            
            <div id="manage-admins" class="content active">
                <h2>管理员管理</h2>
                <div class="form-group">
                    <button id="generate-admin">生成新管理员</button>
                </div>
                <div id="admin-list"></div>
            </div>
            
            <div id="set-standard" class="content">
                <h2>评分标准设置</h2>
                <form id="standard-form">
                    <div class="form-group">
                        <label for="standard-content">评分标准</label>
                        <textarea id="standard-content" required></textarea>
                    </div>
                    <button type="submit">保存标准</button>
                </form>
            </div>
            
            <div id="manage-activity" class="content">
                <h2>活动管理</h2>
                <form id="activity-form">
                    <div class="form-group">
                        <label for="activity-name">活动名称</label>
                        <input type="text" id="activity-name" required>
                    </div>
                    <div class="form-group">
                        <label for="start-time">开始时间</label>
                        <input type="datetime-local" id="start-time" required>
                    </div>
                    <div class="form-group">
                        <label for="end-time">结束时间</label>
                        <input type="datetime-local" id="end-time" required>
                    </div>
                    <button type="submit">创建活动</button>
                    <button type="button" id="end-activity">强制结束当前活动</button>
                </form>
            </div>
            
            <div id="view-scores" class="content">
                <h2>评分详情</h2>
                <div id="president-activity-info"></div>
                <div id="score-details"></div>
            </div>
        </div>
    </div>

    <!-- 管理员账号生成模态框 -->
    <div id="admin-modal" class="modal">
        <div class="modal-content">
            <span class="close-modal">&times;</span>
            <h2>新管理员账号</h2>
            <p>用户名: <span id="generated-username"></span></p >
            <p>密码: <span id="generated-password"></span></p >
            <p>请妥善保存此信息，关闭后将无法查看密码</p >
        </div>
    </div>

    <!-- 社长密码输入模态框 -->
    <div id="president-modal" class="modal">
        <div class="modal-content">
            <span class="close-modal">&times;</span>
            <h2>社长登录</h2>
            <div class="form-group">
                <label for="president-password">密码</label>
                <input type="password" id="president-password" required>
            </div>
            <button id="submit-president-password">登录</button>
            <p id="president-login-error" class="error-message"></p >
        </div>
    </div>

    <script>
        // 数据存储结构
        const DATA_KEYS = {
            POEMS: 'poems',
            ADMINS: 'admins',
            SCORES: 'scores',
            STANDARD: 'scoring_standard',
            CURRENT_ACTIVITY: 'current_activity',
            PAST_ACTIVITIES: 'past_activities',
            CURRENT_ADMIN: 'current_admin'
        };

        // 初始化数据存储
        function initStorage() {
            if (!wusiStorage.getItem(DATA_KEYS.POEMS)) {
                wusiStorage.setItem(DATA_KEYS.POEMS, []);
            }
            if (!wusiStorage.getItem(DATA_KEYS.ADMINS)) {
                wusiStorage.setItem(DATA_KEYS.ADMINS, []);
            }
            if (!wusiStorage.getItem(DATA_KEYS.SCORES)) {
                wusiStorage.setItem(DATA_KEYS.SCORES, {});
            }
            if (!wusiStorage.getItem(DATA_KEYS.STANDARD)) {
                wusiStorage.setItem(DATA_KEYS.STANDARD, '');
            }
            if (!wusiStorage.getItem(DATA_KEYS.CURRENT_ACTIVITY)) {
                wusiStorage.setItem(DATA_KEYS.CURRENT_ACTIVITY, null);
            }
            if (!wusiStorage.getItem(DATA_KEYS.PAST_ACTIVITIES)) {
                wusiStorage.setItem(DATA_KEYS.PAST_ACTIVITIES, []);
            }
            if (!wusiStorage.getItem(DATA_KEYS.CURRENT_ADMIN)) {
                wusiStorage.setItem(DATA_KEYS.CURRENT_ADMIN, null);
            }
        }

        // 页面初始化
        document.addEventListener('DOMContentLoaded', function() {
            initStorage();
            setupEventListeners();
            showEntrySelection();
            
            // 检查是否有已登录的管理员
            const currentAdmin = wusiStorage.getItem(DATA_KEYS.CURRENT_ADMIN);
            if (currentAdmin) {
                showAdminInterface(currentAdmin);
            }
        });

        // 设置事件监听器
        function setupEventListeners() {
            // 入口选择
            document.getElementById('user-entry').addEventListener('click', showUserInterface);
            document.getElementById('admin-entry').addEventListener('click', showAdminInterface);
            document.getElementById('president-entry').addEventListener('click', showPresidentModal);

            // 用户界面
            document.querySelectorAll('[data-tab]').forEach(tab => {
                tab.addEventListener('click', switchTab);
            });
            document.getElementById('poem-form').addEventListener('submit', submitPoem);

            // 社长界面
            document.getElementById('generate-admin').addEventListener('click', generateAdmin);
            document.getElementById('standard-form').addEventListener('submit', saveStandard);
            document.getElementById('activity-form').addEventListener('submit', createActivity);
            document.getElementById('end-activity').addEventListener('click', endActivity);
            document.getElementById('submit-president-password').addEventListener('click', presidentLogin);
 // 模态框
            document.querySelectorAll('.close-modal').forEach(btn => {
                btn.addEventListener('click', function() {
                    this.closest('.modal').style.display = 'none';
                });
            });
        }

 // 显示入口选择
        function showEntrySelection() {
            hideAllContent();
            document.getElementById('entry-selection').classList.add('active');
        }

        // 显示用户界面
        function showUserInterface() {
            hideAllContent();
            document.getElementById('user-interface').classList.add('active');
            updateUserInterface();
        }

        // 显示管理员界面
        function showAdminInterface(adminUsername = null) {
            if (!adminUsername) {
                const admins = wusiStorage.getItem(DATA_KEYS.ADMINS);
                if (admins.length === 0) {
                    alert('当前没有可用的管理员账号，请社长先生成管理员账号');
                    return;
                }
                
                // 随机选择一个管理员
                const randomAdmin = admins[Math.floor(Math.random() * admins.length)];
                wusiStorage.setItem(DATA_KEYS.CURRENT_ADMIN, randomAdmin.username);
                adminUsername = randomAdmin.username;
            }
            
            hideAllContent();
            document.getElementById('admin-interface').classList.add('active');
            updateAdminInterface(adminUsername);
        }

        // 显示社长密码输入模态框
        function showPresidentModal() {
            document.getElementById('president-modal').style.display = 'block';
        }

        // 社长登录
        function presidentLogin() {
            const password = document.getElementById('president-password').value;
            const errorElement = document.getElementById('president-login-error');
            
            if (password === 'Qaz,92706002.') {
                hideAllContent();
                document.getElementById('president-interface').classList.add('active');
                document.getElementById('president-modal').style.display = 'none';
                document.getElementById('president-password').value = '';
                errorElement.textContent = '';
                updatePresidentInterface();
            } else {
                errorElement.textContent = '密码错误';
            }
        }

        // 隐藏所有内容区域
        function hideAllContent() {
            document.querySelectorAll('.content').forEach(content => {
                content.classList.remove('active');
            });
        }

        // 切换标签页
        function switchTab(e) {
            const tabId = e.target.getAttribute('data-tab');
            const tabContainer = e.target.parentElement;
            const contentContainer = tabContainer.parentElement;

            // 更新标签状态
            tabContainer.querySelectorAll('.tab').forEach(tab => {
                tab.classList.remove('active');
            });
            e.target.classList.add('active');

            // 更新内容状态
            contentContainer.querySelectorAll('.content').forEach(content => {
                content.classList.remove('active');
            });
            document.getElementById(tabId).classList.add('active');

            // 更新特定标签内容
            if (tabId === 'view-ranking') {
                updateRankingView();
            } else if (tabId === 'view-scores') {
                updateScoreDetails();
            }
        }

        // 更新用户界面
        function updateUserInterface() {
            const currentActivity = wusiStorage.getItem(DATA_KEYS.CURRENT_ACTIVITY);
            const poems = wusiStorage.getItem(DATA_KEYS.POEMS);
            const activityInfo = document.getElementById('activity-info');
            const rankingInfo = document.getElementById('ranking-info');

            if (currentActivity) {
                const now = new Date();
                const startTime = new Date(currentActivity.startTime);
                const endTime = new Date(currentActivity.endTime);
                let statusText = '';

                if (now < startTime) {
                    statusText = `<span class="activity-status status-not-started">未开始</span>`;
                } else if (now >= startTime && now <= endTime) {
                    statusText = `<span class="activity-status status-ongoing">进行中</span>`;
                } else {
                    statusText = `<span class="activity-status status-ended">已结束</span>`;
                }

                activityInfo.innerHTML = `
                    <h3>${currentActivity.name}</h3>
                    <p>活动时间: ${formatDate(startTime)} 至 ${formatDate(endTime)}</p >
                    <p>状态: ${statusText}</p >
                `;

                rankingInfo.innerHTML = `
                    <h3>${currentActivity.name}</h3>
                    <p>活动时间: ${formatDate(startTime)} 至 ${formatDate(endTime)}</p >
                `;
            } else {
                activityInfo.innerHTML = '<p>当前没有进行中的活动</p >';
                rankingInfo.innerHTML = '<p>当前没有可查看的活动</p >';
            }
        }

        // 提交诗词投稿
        function submitPoem(e) {
            e.preventDefault();
            
            const currentActivity = wusiStorage.getItem(DATA_KEYS.CURRENT_ACTIVITY);
            if (!currentActivity) {
                alert('当前没有进行中的活动');
                return;
            }

            const now = new Date();
            const startTime = new Date(currentActivity.startTime);
            const endTime = new Date(currentActivity.endTime);

            if (now < startTime) {
                alert('活动尚未开始');
                return;
            }

            if (now > endTime) {
                alert('活动已结束');
                return;
            }

            const nickname = document.getElementById('nickname').value;
            const title = document.getElementById('title').value;
            const poemType = document.getElementById('poem-type').value;
            const content = document.getElementById('content').value;

            const poems = wusiStorage.getItem(DATA_KEYS.POEMS);
            const existingPoem = poems.find(poem => 
                poem.activityId === currentActivity.id && 
                poem.nickname === nickname
            );

            if (existingPoem) {
                alert('您已经在本活动中投稿过了');
                return;
            }

            const newPoem = {
                id: generateId(),
                activityId: currentActivity.id,
                nickname,
                title,
                type: poemType,
                content,
                submitTime: new Date().toISOString()
            };

            poems.push(newPoem);
            wusiStorage.setItem(DATA_KEYS.POEMS, poems);

            alert('投稿成功！');
            document.getElementById('poem-form').reset();
        }

        // 更新排名视图
        function updateRankingView() {
            const currentActivity = wusiStorage.getItem(DATA_KEYS.CURRENT_ACTIVITY);
            const poemList = document.getElementById('poem-ranking');
            poemList.innerHTML = '';

            if (!currentActivity) {
                poemList.innerHTML = '<p>当前没有可查看的活动</p >';
                return;
            }

            const now = new Date();
            const endTime = new Date(currentActivity.endTime);
            if (now < endTime) {
                poemList.innerHTML = '<p>活动尚未结束，排名暂不公开</p >';
                return;
            }

            const poems = wusiStorage.getItem(DATA_KEYS.POEMS).filter(
                poem => poem.activityId === currentActivity.id
            );
            const scores = wusiStorage.getItem(DATA_KEYS.SCORES)[currentActivity.id] || {};

            // 计算平均分
            const poemsWithScores = poems.map(poem => {
                const poemScores = scores[poem.id] || {};
                const scoreValues = Object.values(poemScores).map(s => s.score);
                const average = scoreValues.length > 0 ? 
                    (scoreValues.reduce((a, b) => a + b, 0) / scoreValues.length).toFixed(1) : 
                    '未评分';
                return {
                    ...poem,
                    averageScore: average
                };
            });

            // 按分数排序
            poemsWithScores.sort((a, b) => {
                if (a.averageScore === '未评分') return 1;
                if (b.averageScore === '未评分') return -1;
                return b.averageScore - a.averageScore;
            });

            if (poemsWithScores.length === 0) {
                poemList.innerHTML = '<p>本活动暂无投稿作品</p >';
                return;
            }
poemsWithScores.forEach((poem, index) => {
                const poemItem = document.createElement('div');
                poemItem.className = 'poem-item';
                poemItem.innerHTML = `
                    <div class="poem-type">${poem.type}</div>
                    <div class="poem-title">${poem.title}</div>
                    <div class="poem-author">作者: ${poem.nickname}</div>
                    <div class="poem-content">${poem.content}</div>
                    <div>平均分: <strong>${poem.averageScore}</strong></div>
                `;
                poemList.appendChild(poemItem);
            });
        }

        // 更新管理员界面
        function updateAdminInterface(adminUsername) {
            const currentActivity = wusiStorage.getItem(DATA_KEYS.CURRENT_ACTIVITY);
            const activityInfo = document.getElementById('admin-activity-info');
            const scoringStandard = document.getElementById('scoring-standard');
            const poemsToScore = document.getElementById('poems-to-score');
            
            if (currentActivity) {
                const now = new Date();
                const startTime = new Date(currentActivity.startTime);
                const endTime = new Date(currentActivity.endTime);
                let statusText = '';

                if (now < startTime) {
                    statusText = `<span class="activity-status status-not-started">未开始</span>`;
                } else if (now >= startTime && now <= endTime) {
                    statusText = `<span class="activity-status status-ongoing">进行中</span>`;
                } else {
                    statusText = `<span class="activity-status status-ended">已结束</span>`;
                }

                activityInfo.innerHTML = `
                    <h3>${currentActivity.name}</h3>
                    <p>活动时间: ${formatDate(startTime)} 至 ${formatDate(endTime)}</p >
                    <p>状态: ${statusText}</p >
                `;

                // 显示评分标准
                const standard = wusiStorage.getItem(DATA_KEYS.STANDARD);
                scoringStandard.innerHTML = standard ? 
                    `<h3>评分标准</h3><p>${standard}</p >` : 
                    '<p>社长尚未设置评分标准</p >';

                // 显示待评分作品
                const poems = wusiStorage.getItem(DATA_KEYS.POEMS).filter(
                    poem => poem.activityId === currentActivity.id
                );
                const scores = wusiStorage.getItem(DATA_KEYS.SCORES)[currentActivity.id] || {};
                
                if (poems.length === 0) {
                    poemsToScore.innerHTML = '<p>暂无投稿作品</p >';
                    return;
                }

                poems.forEach(poem => {
                    const poemItem = document.createElement('div');
                    poemItem.className = 'poem-item';
                    
                    const adminScore = scores[poem.id]?.[adminUsername];
                    
                    poemItem.innerHTML = `
                        <div class="poem-type">${poem.type}</div>
                        <div class="poem-title">${poem.title}</div>
                        <div class="poem-author">作者: ${poem.nickname}</div>
                        <div class="poem-content">${poem.content}</div>
                        <div class="form-group">
                            <label>评分 (0-10分):</label>
                            <input type="number" min="0" max="10" step="0.1" 
                                   class="score-input" 
                                   value="${adminScore ? adminScore.score : ''}"
                                   data-poem-id="${poem.id}"
                                   placeholder="输入0-10分">
                            <button class="save-score" data-poem-id="${poem.id}">保存评分</button>
                        </div>
                        <div class="form-group">
                            <label>评语 (可选):</label>
                            <textarea class="comment-input" 
                                      data-poem-id="${poem.id}"
                                      placeholder="请输入评语 (可选)">${adminScore ? adminScore.comment || '' : ''}</textarea>
                        </div>
                    `;
                    
                    poemsToScore.appendChild(poemItem);
                });

                // 添加评分保存事件
                document.querySelectorAll('.save-score').forEach(button => {
                    button.addEventListener('click', function() {
                        const poemId = this.getAttribute('data-poem-id');
                        const scoreInput = document.querySelector(`.score-input[data-poem-id="${poemId}"]`);
                        const commentInput = document.querySelector(`.comment-input[data-poem-id="${poemId}"]`);
                        
                        const score = parseFloat(scoreInput.value);
                        if (isNaN(score)) {
                            alert('请输入有效的分数');
                            return;
                        }
  if (score < 0 || score > 10) {
                            alert('评分必须在0-10分之间');
                            return;
                        }
                        
                        saveAdminScore(adminUsername, currentActivity.id, poemId, score, commentInput.value);
                        alert('评分已保存');
                    });
                });
            } else {
                activityInfo.innerHTML = '<p>当前没有进行中的活动</p >';
                poemsToScore.innerHTML = '';
            }
        }

        // 保存管理员评分
        function saveAdminScore(adminUsername, activityId, poemId, score, comment) {
            const scores = wusiStorage.getItem(DATA_KEYS.SCORES);
            
            if (!scores[activityId]) {
                scores[activityId] = {};
            }
            
            if (!scores[activityId][poemId]) {
                scores[activityId][poemId] = {};
            }
            
            scores[activityId][poemId][adminUsername] = {
                score,
                comment: comment || '',
                timestamp: new Date().toISOString()
            };
            
            wusiStorage.setItem(DATA_KEYS.SCORES, scores);
        }

        // 生成管理员账号
        function generateAdmin() {
            const username = 'admin_' + Math.random().toString(36).substring(2, 8);
            const password = Math.random().toString(36).substring(2, 10);
            
            const admins = wusiStorage.getItem(DATA_KEYS.ADMINS);
            admins.push({ username, password });
            wusiStorage.setItem(DATA_KEYS.ADMINS, admins);
            
            // 显示生成的账号
            document.getElementById('generated-username').textContent = username;
            document.getElementById('generated-password').textContent = password;
            document.getElementById('admin-modal').style.display = 'block';
            
            updateAdminList();
        }

        // 更新管理员列表
        function updateAdminList() {
            const adminList = document.getElementById('admin-list');
            const admins = wusiStorage.getItem(DATA_KEYS.ADMINS);
            
            if (admins.length === 0) {
                adminList.innerHTML = '<p>暂无管理员账号</p >';
                return;
            }
            
            let html = '<table class="admin-table"><tr><th>用户名</th><th>操作</th></tr>';
            admins.forEach(admin => {
                html += `
                    <tr>
                        <td>${admin.username}</td>
                        <td><button class="delete-admin" data-username="${admin.username}">删除</button></td>
                    </tr>
                `;
            });
            html += '</table>';
            
            adminList.innerHTML = html;
            
            // 添加删除事件
            document.querySelectorAll('.delete-admin').forEach(button => {
                button.addEventListener('click', function() {
                    const username = this.getAttribute('data-username');
                    deleteAdmin(username);
                });
            });
        }

        // 删除管理员
        function deleteAdmin(username) {
            if (!confirm(`确定要删除管理员 ${username} 吗？`)) return;
            
            const admins = wusiStorage.getItem(DATA_KEYS.ADMINS);
            const filteredAdmins = admins.filter(admin => admin.username !== username);
            wusiStorage.setItem(DATA_KEYS.ADMINS, filteredAdmins);
            
            updateAdminList();
        }

        // 保存评分标准
        function saveStandard(e) {
            e.preventDefault();
            
            const standard = document.getElementById('standard-content').value;
            wusiStorage.setItem(DATA_KEYS.STANDARD, standard);
            
            alert('评分标准已保存');
        }

        // 创建活动
        function createActivity(e) {
            e.preventDefault();
            
            const currentActivity = wusiStorage.getItem(DATA_KEYS.CURRENT_ACTIVITY);
            if (currentActivity) {
                if (!confirm('当前已有进行中的活动，创建新活动将结束当前活动。是否继续？')) {
                    return;
                }
            }
            
            const name = document.getElementById('activity-name').value;
            const startTime = document.getElementById('start-time').value;
            const endTime = document.getElementById('end-time').value;
            
            if (!name || !startTime || !endTime) {
                alert('请填写完整活动信息');
                return;
            }
            
            const startDate = new Date(startTime);
            const endDate = new Date(endTime);
            
            if (endDate <= startDate) {
                alert('结束时间必须晚于开始时间');
                return;
            }
            
            const now = new Date();
            if (endDate < now) {
                alert('结束时间不能早于当前时间');
                return;
         }
            
            const newActivity = {
                id: generateId(),
                name,
                startTime: startDate.toISOString(),
                endTime: endDate.toISOString(),
                createdAt: now.toISOString()
            };
            
            wusiStorage.setItem(DATA_KEYS.CURRENT_ACTIVITY, newActivity);
            
            // 添加到历史活动
            if (currentActivity) {
                const pastActivities = wusiStorage.getItem(DATA_KEYS.PAST_ACTIVITIES);
                pastActivities.push(currentActivity);
                wusiStorage.setItem(DATA_KEYS.PAST_ACTIVITIES, pastActivities);
            }
            
            alert('活动创建成功');
            document.getElementById('activity-form').reset();
        }

        // 结束当前活动
        function endActivity() {
            const currentActivity = wusiStorage.getItem(DATA_KEYS.CURRENT_ACTIVITY);
            if (!currentActivity) {
                alert('当前没有进行中的活动');
                return;
            }
            
            if (!confirm('确定要强制结束当前活动吗？')) return;
            
            const pastActivities = wusiStorage.getItem(DATA_KEYS.PAST_ACTIVITIES);
            pastActivities.push(currentActivity);
            wusiStorage.setItem(DATA_KEYS.PAST_ACTIVITIES, pastActivities);
            wusiStorage.setItem(DATA_KEYS.CURRENT_ACTIVITY, null);
            
            alert('活动已结束');
        }

        // 更新社长界面
        function updatePresidentInterface() {
            updateAdminList();
            updateStandardForm();
            updateActivityForm();
            updateScoreDetails();
        }

        // 更新评分标准表单
        function updateStandardForm() {
            const standard = wusiStorage.getItem(DATA_KEYS.STANDARD);
            document.getElementById('standard-content').value = standard || '';
        }

        // 更新活动表单
        function updateActivityForm() {
            const currentActivity = wusiStorage.getItem(DATA_KEYS.CURRENT_ACTIVITY);
            const activityInfo = document.getElementById('president-activity-info');
            
            if (currentActivity) {
                const now = new Date();
                const startTime = new Date(currentActivity.startTime);
                const endTime = new Date(currentActivity.endTime);
                let statusText = '';

                if (now < startTime) {
                    statusText = `<span class="activity-status status-not-started">未开始</span>`;
                } else if (now >= startTime && now <= endTime) {
                    statusText = `<span class="activity-status status-ongoing">进行中</span>`;
                } else {
                    statusText = `<span class="activity-status status-ended">已结束</span>`;
                }

                activityInfo.innerHTML = `
                    <h3>当前活动: ${currentActivity.name}</h3>
                    <p>活动时间: ${formatDate(startTime)} 至 ${formatDate(endTime)}</p >
                    <p>状态: ${statusText}</p >
                `;
            } else {
                activityInfo.innerHTML = '<p>当前没有进行中的活动</p >';
            }
        }
        // 更新评分详情
        function updateScoreDetails() {
            const currentActivity = wusiStorage.getItem(DATA_KEYS.CURRENT_ACTIVITY);
            const scoreDetails = document.getElementById('score-details');
            scoreDetails.innerHTML = '';

            if (!currentActivity) {
                scoreDetails.innerHTML = '<p>当前没有进行中的活动</p >';
                return;
            }

            const poems = wusiStorage.getItem(DATA_KEYS.POEMS).filter(
                poem => poem.activityId === currentActivity.id
            );
            const scores = wusiStorage.getItem(DATA_KEYS.SCORES)[currentActivity.id] || {};
            const admins = wusiStorage.getItem(DATA_KEYS.ADMINS);

            if (poems.length === 0) {
                scoreDetails.innerHTML = '<p>本活动暂无投稿作品</p >';
                return;
            }

            let html = '<table class="admin-table"><tr><th>作品</th>';

            // 添加管理员列标题
            admins.forEach(admin => {
                html += `<th>${admin.username}评分</th>`;
            });

            html += '<th>平均分</th></tr>';

            poems.forEach(poem => {
                const poemScores = scores[poem.id] || {};
                let totalScore = 0;
                let scoreCount = 0;
                let adminScoresHtml = '';

                admins.forEach(admin => {
                    const adminScore = poemScores[admin.username];
                    if (adminScore) {
                        adminScoresHtml += `<td>${adminScore.score}<br><small>${adminScore.comment || '无评语'}</small></td>`;
                        totalScore += adminScore.score;
                        scoreCount++;
                    } else {
                        adminScoresHtml += '<td>未评分</td>';
                    }
                });

                const averageScore = scoreCount > 0 ? (totalScore / scoreCount).toFixed(1) : '未评分';

                html += `
                    <tr>
                        <td>${poem.title}<br><small>${poem.nickname}</small></td>
                        ${adminScoresHtml}
                        <td>${averageScore}</td>
                    </tr>
                `;
            });

            html += '</table>';
            scoreDetails.innerHTML = html;
        }

        // 生成唯一ID
        function generateId() {
            return Date.now().toString(36) + Math.random().toString(36).substring(2);
        }

        // 格式化日期
        function formatDate(date) {
            const d = new Date(date);
            return `${d.getFullYear()}年${d.getMonth() + 1}月${d.getDate()}日 ${d.getHours()}:${d.getMinutes().toString().padStart(2, '0')}`;
        }
    </script>
</body>
</html>
