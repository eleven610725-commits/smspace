# Firebase 配置指南

## 🚀 快速开始（5分钟）

### 步骤 1：创建 Firebase 项目

1. 访问 [Firebase Console](https://console.firebase.google.com/)
2. 点击 **"创建项目"**
3. 输入项目名称：`smspace`
4. 取消勾选 "启用 Google Analytics"（可选）
5. 点击 **"创建项目"**

### 步骤 2：启用认证服务

1. 在 Firebase Console 中，选择你的项目
2. 左侧菜单 → **Build** → **Authentication**
3. 点击 **"开始使用"**
4. 选择 **"Email/Password"** 登录方式
5. 启用 **"Email/Password"** 选项
6. 点击 **"保存"**

### 步骤 3：创建 Firestore 数据库

1. 左侧菜单 → **Build** → **Firestore Database**
2. 点击 **"创建数据库"**
3. 位置选择：离你最近的地区
4. 安全规则：选择 **"以测试模式启动"**
5. 点击 **"创建"**

### 步骤 4：启用 Cloud Storage

1. 左侧菜单 → **Build** → **Storage**
2. 点击 **"开始使用"**
3. 位置：选择与 Firestore 相同的区域
4. 安全规则：选择 **"以测试模式启动"**
5. 点击 **"完成"**

### 步骤 5：获取 Firebase 配置

1. 点击左上角的 **齿轮图标** ⚙️
2. 选择 **"项目设置"**
3. 下拉到 **"你的应用"** 部分
4. 点击 **"</>"** (Web) 图标
5. 注册应用名称（如 `SMSpace Web`）
6. 复制显示的配置代码

配置看起来像这样：
```javascript
const firebaseConfig = {
  apiKey: "AIzaSyDxxxxxxxxxxxxxxxxxxxxx",
  authDomain: "smspace-xxxxx.firebaseapp.com",
  projectId: "smspace-xxxxx",
  storageBucket: "smspace-xxxxx.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123456789:web:abcdefghijklmnop"
};
```

### 步骤 6：更新应用配置

1. 打开 `index.html`
2. 找到文件中的这一行（约在第 645 行）：
```javascript
const firebaseConfig = {
    apiKey: "AIzaSyDemoKeyForSmspace123456789",
    authDomain: "smspace-demo.firebaseapp.com",
    projectId: "smspace-demo",
    storageBucket: "smspace-demo.appspot.com",
    messagingSenderId: "123456789",
    appId: "1:123456789:web:abcdef1234567890"
};
```

3. **完全替换**为你从 Firebase 复制的配置
4. 保存文件

### 步骤 7：配置 Firestore 安全规则

1. 在 Firebase Console 中，进入 **Firestore Database** → **Rules**
2. 替换所有内容为以下代码：

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // 用户集合 - 只能访问自己的数据
    match /users/{userId} {
      allow read, write: if request.auth.uid == userId;
    }
    
    // 配对集合 - 只有配对成员可以访问
    match /couples/{coupleId} {
      allow read, write: if request.auth.uid in resource.data.memberIds;
      
      // 打卡记录
      match /checkins/{checkinId} {
        allow read, write: if request.auth.uid in get(/databases/$(database)/documents/couples/$(coupleId)).data.memberIds;
      }
      
      // 任务
      match /tasks/{taskId} {
        allow read, write: if request.auth.uid in get(/databases/$(database)/documents/couples/$(coupleId)).data.memberIds;
      }
      
      // 日记
      match /diaries/{diaryId} {
        allow read, write: if request.auth.uid in get(/databases/$(database)/documents/couples/$(coupleId)).data.memberIds;
      }
      
      // 纪念日
      match /anniversaries/{anniversaryId} {
        allow read, write: if request.auth.uid in get(/databases/$(database)/documents/couples/$(coupleId)).data.memberIds;
      }
    }
  }
}
```

3. 点击 **"发布"**

### 步骤 8：配置 Cloud Storage 安全规则

1. 在 Firebase Console 中，进入 **Storage** → **Rules**
2. 替换所有内容为以下代码：

```javascript
rules_version = '2';
service firebase.storage {
  match /b/{bucket}/o {
    // 允许认证用户读取
    match /{allPaths=**} {
      allow read: if request.auth != null;
    }
    
    // 允许在自己的文件夹中写入
    match /couples/{coupleId}/{allPaths=**} {
      allow write: if request.auth != null;
    }
  }
}
```

3. 点击 **"发布"**

## ✅ 完成验证

现在你的 Firebase 已配置完成！

1. 在本地用浏览器打开 `index.html`（或用 Python 启动服务器）
2. 注册一个新账户
3. 应该能成功登录并看到主界面

## 🛠️ 常见问题

### Q: Firebase 免费额度是多少？

A: Firebase 的免费额度非常慷慨：
- **Firestore**: 50K 读/天，20K 写/天，20K 删除/天
- **Cloud Storage**: 5GB 存储，1GB/月下载流量
- **Authentication**: 无限制注册和登录

对于个人使用完全足够。

### Q: 如何增加配额？

A: 升级到付费计划：
1. Firebase Console → **升级**
2. 选择 **Blaze 计划**（按使用量付费）
3. 设置每月支出限制（可选）

### Q: 如何检查使用情况？

A: 
1. Firebase Console → **报告** → **使用情况**
2. 可以看到各项服务的使用统计

### Q: 密钥会泄露吗？

A: 不会。Firebase 密钥是公开的（客户端密钥），使用 Firestore 安全规则和 Cloud Storage 权限来保护数据。

### Q: 如何备份数据？

A:
1. Firebase Console → **数据库** → 菜单 → **导出集合**
2. 选择要导出的集合
3. 下载到本地

## 🚀 部署到 Vercel

配置好 Firebase 后，部署应用：

1. 访问 [Vercel](https://vercel.com)
2. 点击 **"New Project"**
3. 导入 GitHub 仓库 `smspace`
4. 点击 **"Deploy"**
5. 等待 1-2 分钟

部署完成后，你会获得一个 URL，分享给伴侣即可！

## 📱 使用应用

### 第一次使用

1. **User A** 在应用中注册账户
2. **User B** 也注册一个账户
3. **User A** 点击 **"邀请"** → 获得邀请码
4. **User A** 复制邀请码，分享给 **User B**
5. **User B** 点击 **"邀请"** → 输入邀请码 → 加入
6. 配对完成！开始使用应用

### 数据同步

- 所有数据实时同步到 Firestore
- 两个人可以在不同设备上使用同一账户
- ���何一个人的更新会立即显示在另一个人的界面上

## 🔐 安全建议

1. **不要分享** Firebase 配置中的 `apiKey`
   - 实际上这个密钥是公开的，但最好还是谨慎
2. **使用强密码** 注册账户
3. **定期检查** Firebase 使用情况
4. **设置支出限制** 避免意外费用

## 📞 遇到问题？

如果配置有问题：

1. 检查 Firebase Console 是否有错误提示
2. 打开浏览器控制台（F12）查看错误信息
3. 确保 Firestore 和 Storage 都已启用
4. 确保安全规则已发布

常见错误及解决方案：

| 错误 | 原因 | 解决 |
|------|------|------|
| `auth/operation-not-allowed` | 邮箱密码认证未启用 | 在 Authentication 中启用 Email/Password |
| `permission-denied` | 安全规则限制 | 检查 Firestore 和 Storage 的安全规则 |
| `storage/invalid-config` | Storage 未启用 | 在 Storage 中启用服务 |
| 注册后页面卡住 | Firebase 配置错误 | 确认 `firebaseConfig` 配置正确 |

---

**配置完成后，你就可以开始使用 SMSpace 了！** 🎉

有任何问题欢迎在 GitHub 提交 Issue。
