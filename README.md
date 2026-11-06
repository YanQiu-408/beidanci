# 背单词 · 每日一词（PWA）

> 🌐 在线地址：**https://yanqiu-408.github.io/beidanci/**
> 手机浏览器打开 → 添加到主屏幕即可使用；源码托管在 GitHub Pages。

一个「打开即用」的英语背单词 PWA：每天自动生成今日单词和一篇 **用当天单词写的英语阅读**（带题目和翻译），并支持 **AI 批改作文 / 翻译 / 阅读讲解 / 拼写默写**。

手机浏览器打开 → 添加到主屏幕 → 像 App 一样使用。**无账号、无服务器、数据全部存在自己手机里。**

---

## 功能

### 每日任务闭环
- 新词学习 + 间隔重复复习（SM-2 改良：1/3/8/22/61 天递增，答错重排）
- 五种题型：看英选义、看义拼写、例句填空、听音辨词、**跟打**（逐字母实时判定对错）
- **「已掌握」一键跳过**：标记后不再进入学习/复习队列，可在单词详情取消
- 打卡、连续天数、正确率、今日/累计学习时长、词库进度、考试倒计时

### 每日阅读（AI 生成）
- 按考试难度自动调整篇幅：四级 150–200 / 六级 250–300 / 考研 350–400 / 雅思 350–450 / 托福 400–500 / GRE 420–500 词
- 自动嵌入当天 8–12 个重点词，文中高亮、点击查释义和发音
- 3–5 道阅读理解题 + 答案解析 + AI 逐题讲解
- 全文中文翻译、全文朗读

### 每日听力（AI 生成 + 神经语音）
- AI 用当天单词生成听力稿：四六级为男女对话（M/F 交替），雅思/托福为讲座或对话
- 逐段播放、分段重听、听力题作答、原文 + 中文翻译
- 音色：默认手机系统 TTS；配置 `deploy/tts-service` 后使用**微软神经语音（接近真题音色，男女声组合）**
- 语速 0.8x / 1.0x / 1.2x 可调

### AI 批改
- **作文批改**：AI 按当天词汇出题 → 手机输入 → 按考试标准评分（四六级 15 分制、考研 20 分制、雅思 Band 9、托福 30 分制、GRE 6 分制）+ 逐句修改 + 错误分类 + 提分建议 + 参考范文
- **翻译批改**：段落汉译英 → 逐句点评 + 参考译文
- **阅读讲解**：定位原文、正确答案逻辑、干扰项分析
- **拼写默写**：看中文拼单词，本地判分 + AI 记忆方法/易混词提示

### 词库与单词管理
- 内置**六套词库**：四级 / 六级 / 考研 / 雅思 / 托福 / GRE（含音标、中英文释义、词频、词根）
- 错词本（自动收录）、收藏夹、已掌握列表
- 手动加词 + **批量导入**（粘贴或上传 txt / json 词表）
- 数据导出 / 导入备份，清理浏览器数据也不怕

### 统计
- 已学 / 已掌握 / 待复习 / 正确率 / 连续打卡 / 打卡日历
- 今日学习时长、累计学习时长、近 7 天学习量、错词 Top
- 单词详情显示状态、正确率、答错次数
### 其它
- 清爽简约界面、深色模式
- PWA 离线可用（词库和界面缓存在本地，AI 功能需联网）
- 手机系统 TTS 发音（可开关）
---

## 技术栈

| 层 | 方案 |
|---|---|
| 前端 | React 18 + TypeScript + Vite 5 |
| 样式 | Tailwind CSS 3（自定义设计系统） |
| 路由 / 状态 | React Router 6 / Zustand |
| 本地数据库 | Dexie.js（IndexedDB） |
| PWA | vite-plugin-pwa + Workbox（离线预缓存） |
| AI | OpenAI 兼容接口，浏览器直连（默认 DeepSeek） |
| 语音（听力） | 自建 TTS 中转服务（Node + msedge-tts，微软神经语音）+ Cloudflare Tunnel 提供 HTTPS |
| 词库构建 | Node 脚本从 [ECDICT](https://github.com/skywind3000/ECDICT) 提取 |

---

## 本地运行

```bash
npm install          # 安装依赖
npm run dev          # 开发模式，http://localhost:5173
npm run build        # 生产构建，输出 dist/
npm run preview      # 本地预览生产版本，http://localhost:4173
```

### 测试脚本

```bash
npm run typecheck    # TypeScript 类型检查
npm run test:learn   # 学习引擎纯函数单测（38 项）
npm run test:ai      # 真实 AI 接口测试（需 scripts/ai-key.txt，会消耗少量 token）
node scripts/qa-features.mjs  # 新功能 UI 回归（跟打/已掌握/导入/时长/6 考试）
node scripts/qa-listening.mjs # 听力模块端到端（AI 生成 + TTS 播放 + 答题）
node scripts/qa-mock.mjs    # UI 冒烟（拦截词库，无需真实数据）
node scripts/qa-e2e.mjs     # 真实词库 + 真实 AI 端到端验收（会消耗 token）
node scripts/qa-pwa.mjs     # PWA / Service Worker / 离线验证
```

### 重新生成词库（可选）

```bash
node scripts/build-dicts.mjs           # 生成 public/dicts/{cet4,cet6,kaoyan,ielts,toefl,gre}.json
node scripts/build-dicts.mjs --verify  # 只校验已有产物
node scripts/build-dicts.mjs --force   # 强制重新下载 ECDICT（66MB）
```

---

## 目录结构

```
src/
  pages/            # 页面：引导 / 今日 / 背词 / 阅读 / 批改 / 统计 / 设置 / 生词
  components/       # 通用 UI 组件（按钮/卡片/弹层/进度环…）
  lib/
    ai/             # AI 能力层：client / prompts / generate / grade / normalize
    plan.ts         # 每日计划 + 学习进度落库
    srs.ts          # SM-2 间隔重复
    quiz.ts         # 四题型出题
    stats.ts        # 统计与打卡日历
    dicts.ts        # 词库下载与导入
    tts.ts          # 手机 TTS 发音
    backup.ts       # 备份导出/导入
  db/db.ts          # Dexie 表结构
  store/app.ts      # 全局设置
  types.ts          # 全项目类型契约
scripts/
  build-dicts.mjs   # ECDICT → 三套词库
  test-*.mjs        # 测试 / 验收脚本
public/dicts/       # 打包进应用的词库 JSON
public/icons/       # PWA 图标
```

---

## 数据与隐私

- 所有学习数据只存在手机浏览器的 IndexedDB 里，**没有任何服务器、账号、埋点**。
- AI Key 只保存在本机设置中；请求从浏览器直连你配置的 AI 服务商，不经过第三方。
- 把网址分享给同学即可使用，每个人的数据互相独立。
- 清空浏览器数据会丢进度，请定期在「设置 → 导出备份」。

## 词库来源与许可

词库数据来自开源词典 **ECDICT**（MIT License，Copyright (c) 2025 Linwei），本项目用其考试标签筛选出四/六/考研词表，并补齐音标、词根等字段。若需商用请自行确认词典数据的最终许可要求。

## 后续规划

- 听力模块（Azure 神经语音，最接近真题音色）
- 每日提醒通知
- 账号 + 云同步（可选）
- 更多词书（雅思 / 托福 / GRE）
