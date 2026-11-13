# 自动更新静态文件（独立仓库）

**不要**在本仓提交 `update-*.json` / `.tar.gz`（体积大）。Release CI 会推送到公共仓：

**[`hiyali/time-to-stand-releases`](https://github.com/hiyali/time-to-stand-releases)** → 分支 `gh-pages` → GitHub Pages。

客户端匿名拉 manifest（见 `mygo.json` → `updates.url`）：

- `https://hiyali.github.io/time-to-stand-releases/updates/update-darwin-amd64.json`
- `https://hiyali.github.io/time-to-stand-releases/updates/update-windows-amd64.json`

## 一次性搭建（独立仓 + 源码仓 secret）

### 1. 新建公共仓库

- 名称：`time-to-stand-releases`（或改 workflow 里 `UPDATES_REPOSITORY`）。
- **Public**；可只含一篇 README（说明内容由 CI 写入，勿手改）。

### 2. 在更新仓启用 Pages

**Settings → Pages → Build and deployment**

- Source：**Deploy from a branch**
- Branch：`gh-pages` / **/(root)**

（首次 Release 部署成功后 `gh-pages` 分支才会出现，可先 push 空分支或等 CI。）

### 3. 源码仓 secret

在 **`hiyali/time-to-stand`** → **Settings → Secrets and variables → Actions** 新增：

| Secret | 说明 |
|--------|------|
| `UPDATES_REPO_TOKEN` | 对 **`time-to-stand-releases`** 有 **Contents: Read and write** 的 fine-grained PAT（或 classic `repo` 仅限该仓） |

### 4. 发版

与往常一样 push `main` → tag `v0.3.3`；`deploy-updates` job 用 `peaceiris/actions-gh-pages` 写独立仓。

自检：

```sh
curl -fsSL "https://hiyali.github.io/time-to-stand-releases/updates/update-darwin-amd64.json" | head
```

## 改仓库名

编辑 `.github/workflows/release.yml` 的 `env.UPDATES_REPOSITORY`，并同步 `mygo.json` 的 `updates.url` 后 **再发一版**（嵌入 URL 随构建变化）。
