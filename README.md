# wloc-vendor — 本地化/钉死的 Apple 网络定位修改模块

基于 https://github.com/Yu9191/wloc ，为安全敏感设备做的 vendored 副本。

## 安全处理
- `modules/wloc.module` 的两处 `script-path` 已钉死到 upstream commit
  `eec07a8dc8de6dbaee8eac1fb376e4d03020154a`（raw by SHA = 内容不可变，
  上游改 main 不会静默改到本设备执行的脚本）。
- `dist/wloc.js` / `dist/wloc-settings.js` 已本地审计：
  - 无任何 http(s) URL、无 `$httpClient`/`$task.fetch`/`fetch`/`XMLHttpRequest` 发包原语 → 无法外传数据。
  - 仅用 `$persistentStore.read/write` 读写本地 key `wloc_settings`。
  - 无 `eval`/`new Function`。
- **升级 SHA 前必须重新 review 两个脚本。**
- MITM 范围永远只保留 `gs-loc.apple.com` / `gs-loc-cn.apple.com`，绝不扩大。

## 用法
Shadowrocket 订阅本仓库的 `modules/wloc.module`（raw 建议也钉 SHA/tag）。
选点：iCloud 快捷指令 或 https://wloc-pages.pages.dev/ 。
