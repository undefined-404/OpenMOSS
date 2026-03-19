---
description: 从 dev 分支构建并发布到 main 分支
---

# 发布流程：dev → main

## 前提

- `dev` 分支：只有源码，`static/` 已被 `.gitignore` 排除
- `main` 分支：包含源码 + 编译后的 `static/` 目录（用于部署）
- 编译产物不在 dev 上跟踪，避免合并冲突

## 步骤

### 1. 确认 dev 分支代码就绪

```bash
git checkout dev
git status  # 确保工作区干净
```

### 2. 构建前端静态文件

```bash
cd webui
npm run build   # 输出到 ../static/
cd ..
```

// turbo
### 3. 切换到 main 并合并 dev

```bash
git checkout main
git merge dev -m "merge: dev → main"
```

### 4. 提交构建产物

```bash
# 此时 static/ 已被 build 更新，但在 dev 上被 gitignore
# main 上 static/ 是被跟踪的，所以 git add 会检测到变化
git add static/
git commit -m "build: update static assets"
```

### 5. 推送

```bash
git push origin main
```

// turbo
### 6. 切回 dev 继续开发

```bash
git checkout dev
```

## 首次设置（只需做一次）

如果 dev 分支还在跟踪 `static/`，需要先清理：

```bash
git checkout dev

# 从 git 跟踪中移除 static/（保留本地文件）
git rm -r --cached static/

# 确保 dev 的 .gitignore 包含 static/
echo "static/" >> .gitignore

git add .gitignore
git commit -m "chore: stop tracking static/ on dev branch"
```

> ⚠️ 注意：main 分支的 `.gitignore` 不要排除 `static/`，因为 main 需要跟踪它。
> 可以在合并后手动检查确保 main 的 `.gitignore` 没有 `static/` 这一行。
