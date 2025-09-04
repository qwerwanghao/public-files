# public-files: 扫码直链下载（GitHub + jsDelivr）

本仓库用于公开分发小文件（<100MB），通过 jsDelivr 在国内可直连，并可将下载 URL 转为二维码供线下/线上扫码使用。

仓库信息：
- GitHub 用户：qwerwanghao
- 仓库名称：public-files
- 默认分支：main

---

## 目录结构

```
public-files/
├─ files/                 # 存放可下载文件（建议仅ASCII命名，语义化版本）
└─ manifest/              # （可选）清单与二维码
   ├─ urls.csv            # 每个文件的URL清单（分支/Tag/Commit）
   └─ qr/                 # 批量生成的二维码图片
```

初始占位：
- files/.gitkeep
- manifest/qr/.gitkeep

## 文件命名与版本规范

- 采用语义化版本：`name_vX.Y.Z.ext`（示例：`program_book_bride_preparation_v1.0.1.pdf`）
- 一经发布的版本不可变（不要在相同 tag 下替换文件）
- 文件名尽量使用 ASCII，不含空格与中文；如需中文展示，可在 README 解释
- 单文件 < 100MB；超过 50MB 建议压缩以提升命中与加载速度

## 操作流程（SOP）

1) 将本地待发布文件重命名为 ASCII + 语义版本，例如：
   `场刊--新妇的预备.pdf` -> `program_book_bride_preparation_v1.0.1.pdf`
2) 把文件放入 `files/` 目录，提交并推送到 `main`
3) 创建 tag：`v1.0.1` 并推送（后续修订用 `v1.0.2`、`v1.1.0` 等）
4) 生成三类直链（见下文）并登记到 `manifest/urls.csv`
5) 使用二维码工具将“固定 Tag 直链”转为二维码，并保存到 `manifest/qr/`

若需发布最新版二维码，可额外提供“分支最新”直链二维码，但请在对外文案中说明其会随更新变化。

## jsDelivr 直链模板

假设文件路径：`files/program_book_bride_preparation_v1.0.1.pdf`

- 分支最新（随 main 最新变动）：
  ```
  https://cdn.jsdelivr.net/gh/qwerwanghao/public-files/files/program_book_bride_preparation_v1.0.1.pdf
  ```
- 固定 Tag（推荐用于二维码，稳定可回溯）：
  ```
  https://cdn.jsdelivr.net/gh/qwerwanghao/public-files@v1.0.1/files/program_book_bride_preparation_v1.0.1.pdf
  ```
- 固定 Commit（最强不可变性，用于审计或回溯）：
  ```
  https://cdn.jsdelivr.net/gh/qwerwanghao/public-files@<COMMIT_SHA>/files/program_book_bride_preparation_v1.0.1.pdf
  ```

提示：
- 使用“版本化 URL”（@tag 或 @commit）可以避免缓存与一致性问题
- 不要在已发布的 tag 下替换同名文件；修订请发布新 tag

## 缓存与刷新

- 版本化 URL（@tag/@commit）一般无需刷新
- 分支最新 URL 若需强制刷新，可访问：
  ```
  https://purge.jsdelivr.net/gh/qwerwanghao/public-files/files/program_book_bride_preparation_v1.0.1.pdf
  ```

## 二维码策略与建议

- 默认用“固定 Tag 直链”生成二维码，保证扫码内容稳定
- 如需“总是最新”，再额外提供“分支最新”二维码，并在说明中标注
- 建议二维码错误纠正等级 M 或 Q；尺寸不小于 256×256（印刷建议 ≥ 512×512）
- 如果在微信内置浏览器中希望“保存而非在线预览”，可额外提供 ZIP 版本链接（本项目当前暂不提供）
- 生成二维码命令示例（直接使用字面量 URL，避免在 Windows 使用 %URL% 或在类 Unix 使用 $URL 变量）：
  ```
  npx --yes qrcode -o manifest/qr/program_book_bride_preparation_v1.0.1.png "https://cdn.jsdelivr.net/gh/qwerwanghao/public-files@v1.0.1/files/program_book_bride_preparation_v1.0.1.pdf"
  npx --yes qrcode -t svg -o manifest/qr/program_book_bride_preparation_v1.0.1.svg "https://cdn.jsdelivr.net/gh/qwerwanghao/public-files@v1.0.1/files/program_book_bride_preparation_v1.0.1.pdf"
  ```

## manifest/urls.csv 字段说明

```
filename,version,relative_path,branch_url,tag,commit_sha,commit_url,qr_image,notes
```
- filename：文件名（含扩展名）
- version：语义版本（如 1.0.0）
- relative_path：相对仓库路径（如 files/program_book_bride_preparation_v1.0.1.pdf）
- branch_url：分支直链（随 main 最新）
- tag：如 v1.0.0
- commit_sha：提交哈希（可选）
- commit_url：固定 commit 直链
- qr_image：二维码图片相对路径（如 manifest/qr/program_book_bride_preparation_v1.0.1.png）
- notes：备注

## 示例：制作 v1.0.1 版本

1) 将 `场刊--新妇的预备.pdf` 重命名并放入 `files/` 为 `program_book_bride_preparation_v1.0.1.pdf`
2) 提交并推送到 `main`
3) 打 tag：`v1.0.1` 并推送
4) 依据上面的模板填写 `manifest/urls.csv`
5) 生成二维码并放入 `manifest/qr/`
6) 使用手机（微信或系统浏览器）扫码验证可打开/保存

## 备用方案（不在本期实施）

- 如未来需要更高可控性或统计能力，可切换到 Cloudflare R2 + 自有域名 + Workers 跳转
- 或引入短链服务，在不改变二维码的前提下进行跳转路由与统计

-- END --