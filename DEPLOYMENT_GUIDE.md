# 🚀 部署指南

## 快速部署到 Vercel（1分钟）

### 前置条件
- ✅ GitHub 账户（你已有）
- ✅ Firebase 配置完成（参考 `FIREBASE_SETUP.md`）
- ✅ 更新了 `index.html` 中的 Firebase 配置

### 部署步骤

1. **访问 Vercel**
   - 打开 https://vercel.com
   - 点击右上角 **"Sign Up"** 或 **"Sign In"**
   - 选择 **"Continue with GitHub"**

2. **导入项目**
   - 点击 **"New Project"**
   - 搜索 `smspace` 仓库
   - 点击 **"Import"**

3. **配置项目**
   - 项目名称：`smspace`（默认）
   - Framework：`Other`
   - 其他设置保持默认
   - 点击 **"Deploy"**

4. **等待部署**
   - Vercel 开始构建（通常 1-2 分钟）
   - 看到 ✅ **"Congratulations"** 表示成功

5. **获取链接**
   - 点击 **"Visit"** 查看你的应用
   - 链接格式：`https://smspace-xxx.vercel.app`
   - 这就是你的应用 URL！

## ✨ 部署后的设置

### 1. 测试应用
- 用浏览器打开你的 Vercel URL
- 注册第一个账户
- 如果能成功登录 → ✅ 部署成功！

### 2. 分享给伴侣
- 复制应用 URL
- 分享给伴侣
- 伴侣在该 URL 上注册账户

### 3. 配对
- 你点击 **"邀请"** → 复制邀请码
- 分享邀请码给伴侣
- 伴侣输入邀请码 → 配对完成！

## 🔄 更新应用

如果你修改了代码（比如修改 Firebase 配置）：

1. **提交更改到 GitHub**
   ```bash
   git add .
   git commit -m "更新配置"
   git push
   ```

2. **Vercel 自动部署**
   - Vercel 会自动检测 GitHub 更新
   - 自动重新部署（通常 1-2 分钟）
   - 你无需做任何操作！

## 🌐 自定义域名（可选）

如果你有自己的域名：

1. 在 Vercel 项目设置中
2. 点击 **"Domains"**
3. 添加你的域名
4. 按照说明配置 DNS 记录

## 📊 监控应用

### 查看应用统计
1. Vercel Dashboard
2. 选择 `smspace` 项目
3. 查看访问量、性能等数据

### 查看日志
1. 点击 **"Analytics"** 标签
2. 查看应用的实时日志和错误

## ⚙️ 环境变量（高级）

如果需要添加环境变量（现在不需要）：

1. 项目设置 → **Environment Variables**
2. 添加变量
3. 重新部署

## 🆘 部署问题排查

### 问题：显示错误页面
**解决方案：**
- 检查 Firebase 配置是否正确
- 打开浏览器控制台（F12）查看错误
- 确保 `index.html` 已经上传

### 问题：应用加载很慢
**解决方案：**
- 这是正常的，Firebase 初始化需要时间
- 首次加载可能 3-5 秒

### 问题：无法注册/登录
**解决方案：**
- 检查 Firebase 中 Authentication 是否启用
- 检查 Firestore 安全规则是否正确
- 查看浏览器控制台的错误信息

### 问题：无法上传图片/视频
**解决方案：**
- 检查 Storage 是否启用
- 检查 Storage 安全规则是否正确
- 检查文件大小（图片 <10MB，视频 <50MB）

## 🔐 生产环境注意事项

当你准备正式使用时：

### 1. 修改 Firestore 安全规则
目前使用的是**测试模式**（允许所有访问）

**重要：** 在生产环境前替换为：
```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{userId} {
      allow read, write: if request.auth.uid == userId;
    }
    
    match /couples/{coupleId} {
      allow read, write: if request.auth.uid in resource.data.memberIds;
      
      match /checkins/{checkinId} {
        allow read, write: if request.auth.uid in get(/databases/$(database)/documents/couples/$(coupleId)).data.memberIds;
      }
      
      match /tasks/{taskId} {
        allow read, write: if request.auth.uid in get(/databases/$(database)/documents/couples/$(coupleId)).data.memberIds;
      }
      
      match /diaries/{diaryId} {
        allow read, write: if request.auth.uid in get(/databases/$(database)/documents/couples/$(coupleId)).data.memberIds;
      }
      
      match /anniversaries/{anniversaryId} {
        allow read, write: if request.auth.uid in get(/databases/$(database)/documents/couples/$(coupleId)).data.memberIds;
      }
    }
  }
}
```

### 2. 修改 Storage 安全规则
```javascript
rules_version = '2';
service firebase.storage {
  match /b/{bucket}/o {
    match /couples/{coupleId}/{allPaths=**} {
      allow read: if request.auth != null && 
                    request.auth.uid in get(/databases/(default)/documents/couples/$(coupleId)).data.memberIds;
      allow write: if request.auth != null && 
                     request.auth.uid in get(/databases/(default)/documents/couples/$(coupleId)).data.memberIds;
    }
  }
}
```

### 3. 启用 HTTPS
- Vercel 自动使用 HTTPS ✅

### 4. 设置 Firebase 支出限制
1. Firebase Console → 项目设置 → **结算**
2. 设置每月最大支出限制（可选）

## 💡 性能优化建议

1. **图片压缩**：在上传前压缩图片
2. **视频转换**：使用 MP4 格式，分辨率不要过高
3. **清理数据**：定期删除不需要的日记和任务

## 🎉 部署完成！

现在你的 SMSpace 应用已经在线了！

### 分享你的应用
- URL: `https://smspace-xxx.vercel.app`
- 分享给伴侣
- 开始使用！

### 后续维护
- Vercel 会自动处理服务器和 HTTPS
- Firebase 会自动备份数据
- 你只需关注添加功能和优化体验

---

**祝你们甜蜜使用！** 💕

有任何问题欢迎在 GitHub 提交 Issue。
