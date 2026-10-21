# Evolve for LazyCat

将 [Evolve](https://github.com/pmotschmann/Evolve)（进化 / Evolve Idle）打包为 LazyCat LPK v2。这是一款网页放置类游戏：先在原生质阶段选择进化方向、培育出智慧生物，再带领文明发展科技，最终迈向星空。

## 镜像来源

上游不提供预构建镜像，也没有发布 tag，因此镜像由 [dockers-x/evolve](https://github.com/dockers-x/evolve) 直接从上游源码构建：

- `ghcr.io/dockers-x/evolve:latest`
- 构建过程：`node:22` 执行 `npm ci && npm run build`（esbuild + less + csso）→ `nginx:1.27-alpine` 托管静态站点
- 每天检查上游 `master` 新提交，有新提交时重新构建并推送

本仓库使用 `ghcr.1ms.run/dockers-x/evolve` 加速地址，发布前校验 `linux/amd64` 源镜像与加速地址 digest 一致。

## 数据

- 纯静态站点，没有后端与数据库
- 游戏存档保存在浏览器 `localStorage`，更换浏览器或清理数据会丢失；游戏内可导出/导入存档文本备份

## 本地构建

```bash
lzc-cli project build -o dist/application.lpk
lzc-cli lpk info dist/application.lpk
```

## 自动发布

`.github/workflows/lazycat.yml` 使用 `ca-x/lazycat-github-action`，跟踪 `ghcr.io/dockers-x/evolve:latest` 的 digest 变化（变化时递增 patch 版本），创建版本化 GitHub Release Asset 并只发布到喵喵私有商店。需要为仓库配置：

- `APPSTORE_URL`
- `APPSTORE_TOKEN`
- `APP_ID`，可选
- `PRIVATE_STORE_GROUP_CODES`，可选，用于私有分组
