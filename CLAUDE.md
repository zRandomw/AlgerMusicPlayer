# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 项目概述

Alger Music Player 是一个基于 Electron + Vue 3 的跨平台音乐播放器，支持网易云音乐API、本地音乐播放、歌词显示、均衡器、多语言等功能。

## 开发命令

### 基础开发命令
```bash
# 安装依赖
npm install

# 启动桌面端开发模式
npm run dev

# 启动网页端开发模式 (需要配置 .env.development.local)
npm run dev:web

# 构建项目
npm run build
```

### 代码质量检查
```bash
# 类型检查 - 分别检查主进程和渲染进程
npm run typecheck:node    # 主进程类型检查
npm run typecheck:web     # 渲染进程类型检查  
npm run typecheck         # 完整类型检查

# 代码规范检查和修复
npm run lint

# 代码格式化
npm run format
```

### 打包构建
```bash
# 各平台打包
npm run build:win     # Windows 打包
npm run build:mac     # macOS 打包
npm run build:linux   # Linux 打包
npm run build:unpack  # 构建但不打包
```

## 核心架构

### 技术栈
- **前端框架**: Vue 3 + TypeScript + Composition API
- **桌面框架**: Electron (主进程 + 渲染进程 + 预加载脚本)
- **UI组件**: naive-ui + Tailwind CSS
- **状态管理**: Pinia
- **构建工具**: electron-vite + Vite
- **国际化**: vue-i18n (支持中文/英文/日语/韩语/繁体中文)
- **音乐API**: netease-cloud-music-api + @unblockneteasemusic/server

### 项目结构
```
src/
├── i18n/           # 国际化配置和语言包
├── main/           # Electron主进程
│   ├── modules/    # 主进程功能模块 (窗口/托盘/快捷键/配置等)
│   ├── index.ts    # 主进程入口
│   ├── server.ts   # 内置API服务器
│   └── lyric.ts    # 歌词处理
├── preload/        # 预加载脚本 (主进程与渲染进程桥接)
└── renderer/       # Vue渲染进程
    ├── api/        # API请求封装
    ├── components/ # Vue组件
    ├── store/      # Pinia状态管理
    ├── views/      # 页面组件
    ├── hooks/      # Composition API hooks
    ├── utils/      # 工具函数
    └── types/      # TypeScript类型定义
```

### 关键组件说明

#### 主进程模块 (src/main/modules/)
- `window.ts` - 窗口管理 (主窗口/迷你模式/全屏歌词)
- `tray.ts` - 系统托盘功能
- `shortcuts.ts` - 全局快捷键
- `config.ts` - 配置管理 (基于electron-store)
- `remoteControl.ts` - 远程控制服务

#### 状态管理 (src/renderer/store/modules/)
- `music.ts` - 音乐播放相关状态
- `player.ts` - 播放器控制状态  
- `lyric.ts` - 歌词显示状态
- `settings.ts` - 应用设置状态
- `user.ts` - 用户账号状态

#### 核心功能组件
- `PlayBar.vue` - 底部播放控制栏
- `MusicFull.vue` - 全屏歌词模式
- `PlayingListDrawer.vue` - 播放列表抽屉
- `EQControl.vue` - 均衡器控制
- `LanguageSwitcher.vue` - 语言切换器

## 开发规范

### 命名约定
- 目录: kebab-case (`components/song-item`)
- Vue组件文件: PascalCase (`SongItem.vue`)
- Hooks: camelCase (`useMusicPlayer.ts`)
- 常量: UPPER_SNAKE_CASE

### 代码风格
- 使用 Vue 3 Composition API + `<script setup>`
- TypeScript 优先使用 `type` 而非 `interface`
- 组件间通信优先使用 Pinia store
- 样式使用 Tailwind CSS 类名
- 遵循现有的ESLint + Prettier配置

### 国际化
- 所有用户可见文本必须使用 `$t()` 进行国际化
- 语言包位于 `src/i18n/lang/` 下，按功能模块分文件
- 新增文本需要在所有支持的语言包中添加对应翻译

### 类型安全
- 严格的TypeScript配置，分为主进程和渲染进程两套配置
- API响应数据必须定义对应的TypeScript类型
- 组件props和emits必须定义类型

## 重要注意事项

### 开发环境配置
- 网页端开发需要在 `src/renderer/` 下配置 `.env.development.local`
- API服务地址和音乐解锁服务地址需要正确配置

### 跨进程通信
- 主进程与渲染进程通过 `src/preload/index.ts` 进行安全通信
- 使用 contextIsolation 确保安全性

### 音乐播放架构
- 音乐播放基于 Howler.js
- 支持多音源解析和切换
- 本地服务器提供网易云音乐API代理

### 构建和部署
- 桌面端使用 electron-builder 打包
- 网页端构建输出到 `out/renderer/`
- 支持 Windows/macOS/Linux 多平台构建