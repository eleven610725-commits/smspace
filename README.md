# 💕 SMSpace - 双人亲密关系空间（Pro版）

一个功能完整的移动端网页应用，为情侣打造的私密专属空间。

## ✨ 核心功能

### 🔐 用户系统
- 📧 邮箱注册/登录
- 👤 个人资料管理
- 🔑 密码安全存储

### 💑 配对与邀请
- 生成唯一邀请码
- 邀请伴侣加入
- 配对确认机制

### 💌 亲密空间
- **爱情誓言** - 记录你们的誓言
- **共享相册** - 上传珍藏美好回忆（图片/视频）
- **纪念日计数** - 记录重要日期

### 📍 每日打卡
- 🌅 早上打卡
- 🌙 晚上打卡
- 😊 心情记录
- 📊 月度统计

### 🎯 甜蜜任务 + 媒体反馈（⭐ 新功能）
- 创建任务清单
- **图片上传反馈** - 上传任务完成证明
- **视频上传反馈** - 录制完成过程
- 完成度追踪
- 双人互动反馈

### 📔 每日日记
- ✍️ 记录每天的想法
- 😍 心情表达（5种情绪）
- 📸 插入图片
- 📚 历史日记查看

## 🌐 云端存储方案

### Firebase 完整集成
- ✅ 实时数据库（Firestore）
- ✅ 文件存储（Cloud Storage）
- ✅ 用户认证（Authentication）
- ✅ 完全免费（Google 免费额度充足）

### 自动云同步
- 📱 手机、平板、电脑无缝同步
- 🔄 实时更新
- 💾 自动备份
- 🔐 数据加密传输

## 📁 媒体上传功能

### 支持格式
- **图片**: JPG, PNG, WebP（最大 10MB）
- **视频**: MP4, WebM（最大 50MB）
- **自动压缩**: 降低文件大小以加快上传

### 任务反馈系统
```
任务 → 创建者发起
    ↓
任务 → 被分配者查看
    ↓
任务 → 添加反馈（图片/视频）
    ↓
任务 → 创建者确认
    ↓
任务 → 标记完成 + 成就奖励
```

## 🎨 主题配色

- 💗 粉色（默认）
- 💙 蓝色
- 💜 紫色
- 💚 绿色
- ⬛ 深色

## 🚀 部署指南

### 前置要求
1. GitHub 账号（已有 ✓）
2. Firebase 账号（免费）
3. Vercel 账号（免费）

### 第一步：设置 Firebase

1. **访问** https://console.firebase.google.com
2. **创建新项目**
   - 项目名称：`smspace`
   - Google Analytics：关闭（可选）
3. **启用 Firestore 数据库**
   - 位置：选择离你最近的地区
   - 安全规则：选择"测试模式"
4. **启用 Cloud Storage**
   - 位置：同上
5. **获取配置**
   - 项目设置 → 常规 → 复制整个配置
   - 替换代码中的 `firebaseConfig`

### 第二步：更新代码中的 Firebase 配置

编辑 `index.html`，找到这行：
```javascript
const firebaseConfig = {
    apiKey: "YOUR_API_KEY",
    authDomain: "your-app.firebaseapp.com",
    projectId: "your-project-id",
    storageBucket: "your-project.appspot.com",
    messagingSenderId: "xxx",
    appId: "1:xxx:web:xxx"
};
```

替换为你的 Firebase 配置。

### 第三步：设置 Firestore 安全规则

在 Firebase Console 中，进入 **Firestore Database → Rules**，替换为：

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // 用户集合
    match /users/{userId} {
      allow read, write: if request.auth.uid == userId;
    }
    
    // 配对集合
    match /couples/{coupleId} {
      allow read, write: if request.auth.uid in resource.data.memberIds;
    }
    
    // 任务集合
    match /couples/{coupleId}/tasks/{taskId} {
      allow read, write: if request.auth.uid in get(/databases/$(database)/documents/couples/$(coupleId)).data.memberIds;
    }
    
    // 日记集合
    match /couples/{coupleId}/diaries/{diaryId} {
      allow read, write: if request.auth.uid in get(/databases/$(database)/documents/couples/$(coupleId)).data.memberIds;
    }
  }
}
```

### 第四步：设置 Cloud Storage 安全规则

在 Firebase Console 中，进入 **Storage → Rules**，替换为：

```javascript
rules_version = '2';
service firebase.storage {
  match /b/{bucket}/o {
    match /couples/{coupleId}/{allPaths=**} {
      allow read: if request.auth != null;
      allow write: if request.auth.uid in resource.data.memberIds;
    }
  }
}
```

### 第五步：部署到 Vercel

1. 访问 https://vercel.com/new
2. 导入 GitHub 仓库 `smspace`
3. 点击 "Deploy"
4. 等待部署完成，获得 URL

---

## 📱 详细功能说明

### 注册登录
```
邮箱 → Firebase 认证 → 创建用户
密码 → 加密存储 → 云端
```

### 邀请伴侣
```
发起者 → 生成邀请码 → 分享给伴侣
伴侣 → 输入邀请码 → 自动配对 → 云端同步
```

### 任务 + 媒体反馈
```
创建任务
  ├─ 标题 + 描述
  ├─ 分配给伴侣
  └─ 云端保存

被分配者查看
  ├─ 任务详情
  ├─ 上传图片/视频证明
  └─ 添加备注

创建者审核
  ├─ 查看反馈媒体
  ├─ 确认完成
  └─ 标记成就

自动同步到两个设备
```

### 日记 + 图片
```
写日记 → 选心情 → 上传图片 → 云端保存
伴侣可见 → 留言评论（可选后续功能）
```

## 🛠️ 技术栈

| 层 | 技术 | 说明 |
|---|------|------|
| 前端 | HTML5 + CSS3 + JS | 原生开发，无框架 |
| 认证 | Firebase Auth | 邮箱/密码登录 |
| 数据库 | Firestore | 实时云数据库 |
| 存储 | Cloud Storage | 图片/视频存储 |
| 部署 | Vercel | 静态托管 |

## 📊 数据库结构

```
Firestore
├── users/
│   └── {userId}
│       ├── email
│       ├── name
│       ├── avatar
│       └── createdAt
│
├── couples/
│   └── {coupleId}
│       ├── user1Id
│       ├── user2Id
│       ├── memberIds []
│       ├── vow
│       ├── createdAt
│       └── inviteCode
│
├── couples/{coupleId}/tasks/
│   └── {taskId}
│       ├── title
│       ├── description
│       ├── creatorId
│       ├── status
│       ├── feedback []
│       │   ├── imageUrls
│       │   ├── videoUrls
│       │   ├── note
│       │   └── uploadedAt
│       └── createdAt
│
├── couples/{coupleId}/diaries/
│   └── {diaryId}
│       ├── userId
│       ├── content
│       ├── mood
│       ├── imageUrls []
│       └── createdAt
│
└── couples/{coupleId}/checkins/
    └── {checkinId}
        ├── userId
        ├── type (morning/evening)
        ├── mood
        ├── note
        └── createdAt
```

## 🔒 隐私与安全

- ✅ Firebase 服务器端加密
- ✅ 用户认证隔离
- ✅ 安全规则限制访问
- ✅ 媒体文件权限管理
- ✅ 自动备份

## 💡 使用建议

1. **文件大小**：图片 < 10MB，视频 < 50MB
2. **网络**：确保网络稳定以便上传媒体
3. **隐私**：避免上传包含隐私信息的内容
4. **备份**：Firebase 自动备份，无需手动操作

## 🎯 后续增强功能

- [ ] 评论和点赞系统
- [ ] 共享相册浏览
- [ ] 成就勋章系统
- [ ] 消息推送通知
- [ ] 视频通话集成
- [ ] 情侣小游戏
- [ ] 数据导出

## 🐛 常见问题

### Q: Firebase 免费额度够用吗？
A: 完全够用。免费额度包括：
- 50K 读/天
- 20K 写/天
- 20K 删除/天
- 5GB 存储
- 对于个人使用绝对足够

### Q: 如何备份数据？
A: Firebase 会自动备份。你也可以在 Firebase Console 中导出数据。

### Q: 两个人用不同设备可以同步吗？
A: 完全可以！只要用同一个配对 ID 登录，所有数据实时同步。

---

## 📞 需要帮助？

- 查看本 README
- 检查 Firebase Console 的错误日志
- 在 GitHub 提交 Issue

---

**让你们的爱情在云端闪闪发光！** ☁️💕

Made with ❤️ by Copilot
