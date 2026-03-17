# StoryShare 创意工坊 - SillyTavern 扩展

[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![SillyTavern](https://img.shields.io/badge/SillyTavern-1.12.0+-orange.svg)](https://github.com/SillyTavern/SillyTavern)

> 为 [StoryShare 创意工坊](https://github.com/AlyceSingle/STCreative-Workshop) 提供的 SillyTavern 扩展

一个 SillyTavern 扩展，提供弹窗式创意工坊浏览器，支持直接订阅/退订模组并插入世界书。

---

## ✨ 功能特性

- ✅ **一键打开工坊** - 从 SillyTavern Extensions 面板打开创意工坊弹窗
- ✅ **弹窗式浏览器** - 无需离开 SillyTavern 主界面
- ✅ **自动插入世界书** - 订阅模组后自动插入世界书条目
- ✅ **自动移除条目** - 取消订阅后自动清理
- ✅ **智能扫描** - 自动扫描已安装模组，防止重复和冲突
- ✅ **自定义世界书** - 支持为每个工坊分区配置独立世界书名称
- ✅ **实时同步** - 订阅/退订结果即时反馈

---

## 📦 安装方法

### 方法一：通过 GitHub URL 安装（推荐）

1. **打开 SillyTavern**

2. **进入 Extensions 面板**
   - 点击顶部导航栏的 Extensions（扩展）图标

3. **点击 "Install Extension" 按钮**

4. **填写安装信息**：
   ```
   Repository URL: https://github.com/AlyceSingle/STCreation
   Branch: main
   Subfolder: (留空)
   ```

5. **点击 "Install"**

6. **重启 SillyTavern**（刷新浏览器 F5）

---

### 方法二：手动安装

#### Windows 用户

```powershell
# 1. 下载仓库
cd $env:TEMP
git clone https://github.com/AlyceSingle/STCreation.git

# 2. 复制到 SillyTavern（修改路径）
$stPath = "C:\SillyTavern"
Copy-Item -Recurse STCreation "$stPath\public\scripts\extensions\third-party\storyshare-workshop" -Force

# 3. 重启 SillyTavern
```

#### Linux/macOS 用户

```bash
# 1. 下载仓库
cd /tmp
git clone https://github.com/AlyceSingle/STCreation.git

# 2. 复制到 SillyTavern（修改路径）
cp -r STCreation /path/to/SillyTavern/public/scripts/extensions/third-party/storyshare-workshop

# 3. 重启 SillyTavern
```

---

## ⚙️ 配置和使用

### 1. 配置工坊 URL

安装完成后，在 SillyTavern 中：

1. 打开 **Extensions** 面板
2. 找到 **"StoryShare 创意工坊"** 折叠栏并展开
3. 在 **"工坊网址"** 输入框中填写你的工坊地址：
   ```
   https://your-domain.com/StoryShare/
   ```
   
   > ⚠️ **注意**：URL 必须包含 `/StoryShare/` 路径

4. 设置会自动保存

### 2. 打开创意工坊

点击 **"打开创意工坊"** 按钮：
- 弹出 960×700 的新窗口
- 自动加载工坊网站
- 自动进行握手连接

### 3. 订阅模组

在弹出的工坊窗口中：

1. 浏览模组列表
2. 点击模组卡片查看详情
3. 点击 **"订阅到 ST"** 按钮
4. 扩展自动：
   - 将模组条目插入 SillyTavern 世界书
   - 显示成功提示
   - 更新订阅状态

### 4. 检查世界书

在 SillyTavern 中：
1. 打开 **World Info** 编辑器
2. 选择对应的世界书（如 "steampunk"）
3. 查看新插入的条目

---

## 🔧 工作原理

### 通信协议

扩展和工坊弹窗使用 `window.postMessage` 进行跨窗口通信：

```
SillyTavern Extension ←→ 工坊弹窗 (Vue)
     ↓                         ↓
loadWorldInfo()          检测 window.opener
saveWorldInfo()          发送 postMessage
reloadWorldInfoEditor()  接收结果
```

### 消息类型

| 方向 | 消息类型 | 功能 |
|-----|---------|------|
| 工坊 → 扩展 | `workshop_ping` | 握手请求 |
| 扩展 → 工坊 | `workshop_pong` | 握手响应 |
| 工坊 → 扩展 | `workshop_scan` | 扫描已订阅模组 |
| 工坊 → 扩展 | `workshop_subscribe` | 订阅模组 |
| 工坊 → 扩展 | `workshop_unsubscribe` | 取消订阅 |
| 扩展 → 工坊 | `*_result` | 操作结果反馈 |

### 安全性

- ✅ 扩展验证 `event.source === workshopWindow`
- ✅ 工坊验证 `event.source === window.opener`
- ✅ 所有请求 5 秒超时保护
- ✅ 条目标记 `extra.source = 'storyshare_workshop'`

---

## 🐛 常见问题

### 问题 1：点击"打开创意工坊"没反应

**原因**：浏览器拦截弹窗

**解决**：
1. 检查浏览器地址栏右侧弹窗拦截图标
2. 允许 SillyTavern 的弹窗
3. 重新点击按钮

---

### 问题 2：弹窗显示"未连接到 SillyTavern 扩展"

**原因**：握手失败

**解决**：
1. 关闭弹窗
2. 重新从扩展面板打开
3. 检查浏览器控制台（F12）错误

---

### 问题 3：订阅提示"请求超时"

**原因**：postMessage 未正确传递

**解决**：
1. 确认工坊 URL 配置正确
2. 必须从扩展按钮打开（不是直接访问）
3. 刷新两个窗口重试

---

### 问题 4：订阅成功但世界书没条目

**原因**：编辑器未刷新

**解决**：
1. 关闭世界书编辑器重新打开
2. 或重启 SillyTavern
3. 检查世界书名称是否正确

---

## 📝 技术规格

- **扩展名称**: StoryShare 创意工坊
- **版本**: 1.0.0
- **兼容性**: SillyTavern 1.12.0+
- **文件大小**: < 20KB
- **依赖**: 无

---

## 🔗 相关链接

- **主项目**: [StoryShare 创意工坊](https://github.com/AlyceSingle/STCreative-Workshop)
- **问题反馈**: [Issues](https://github.com/AlyceSingle/STCreation/issues)
- **SillyTavern**: [官方仓库](https://github.com/SillyTavern/SillyTavern)

---

## 📄 License

根据主项目许可协议分发。

---

## 👥 作者

**StoryShare Team**

- 主项目: [@AlyceSingle](https://github.com/AlyceSingle)
