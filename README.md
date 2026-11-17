# xhr-materials

x.HR Launcher 的运行时物料制品仓。**方案 B：物料二进制不进 Git 历史，以 Release 附件形式按版本发布。**

## 目录约定（Git 内只保留骨架）

```
├── build/
│   ├── version.json              物料版本号（runtime_material_version）
│   └── material-manifest.json   物料内容清单与排除规则
├── scripts/
│   └── pack-material.ps1        本机物料打包脚本
└── .github/workflows/material-check.yml  清单/脚本完整性校验
```

`apps/WeChatRPA/`、`appr/matrixmedia/` 是运行时本体，被 `.gitignore` 排除，仅在本机组装打包时存在。

## 物料发布流程（本机）

```powershell
# 1. 在主仓源码树旁准备好 apps / appr 物料后，打包（产物在 build/）
pwsh -File scripts\pack-material.ps1 -MaterialVersion rpa-matrix-1.0.0

# 2. 提交骨架改动（脚本/清单/规则），打物料版本 tag
git add .
git commit -m "chore: 更新物料骨架 rpa-matrix-1.0.0"
git tag rpa-matrix-1.0.0
git push origin main
git push origin rpa-matrix-1.0.0

# 3. 把物料 zip 与 manifest 上传到该 tag 的 Release 附件
gh release create rpa-matrix-1.0.0 `
  build/material-rpa-matrix-1.0.0.zip `
  build/material-rpa-matrix-1.0.0.manifest.json `
  --repo Communist-t/xhr-materials --title "rpa-matrix-1.0.0" --notes "运行时物料包"
```

## 主仓如何取料

主仓 `.github/workflows/build-release.yml` 按 `build/version.json` 里的 `runtime_material_version`，
用 `gh release download <物料tag>` 从本仓 Release 拉取 `material-*.zip`，解压后并入发布包。

## 版本对应

| 物料 tag | 说明 |
|---|---|
| `rpa-matrix-1.0.0` | 首个物料版本：WeChatRPA Python 运行时 + matrixmedia Electron 目录 |

更新子程序时，重新打包、打新 tag，并回主仓 `build/version.json` 更新 `runtime_material_version`。
