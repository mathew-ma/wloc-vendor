# wloc-vendor — 本地化/钉死的 Apple 网络定位修改模块

基于 https://github.com/Yu9191/wloc ，为安全敏感设备做的 vendored 副本。

## 安全处理
- `modules/wloc.module` 的两处 `script-path` **指向本仓库自己的副本**，钉死 commit
  `c18856532ee6663d81ec38a923c960b31b8abe30`（raw by SHA = 内容不可变，任何人改
  main 都不会静默改到本设备执行的脚本）。自托管的额外好处：上游删库/改名不会让
  手机上的脚本 404 失效。
- 本仓库 `dist/` 与 upstream commit `eec07a8dc8de6dbaee8eac1fb376e4d03020154a`
  **逐字节一致**，SHA256 基线（升级时用来比对）：

  | 文件 | SHA256 |
  |---|---|
  | `dist/wloc.js` | `d385c624efd59bdd2cff56bf819a770b40c4abf0f970818877f1dca4174f256a` |
  | `dist/wloc-settings.js` | `b4e9d69e69c703b3fab485a559825aaedc9e3a1fd9c06e81cb35d10bbdcd13d2` |

- `dist/wloc.js` / `dist/wloc-settings.js` 已本地审计：
  - 无任何 http(s) URL、无 `$httpClient`/`$task.fetch`/`fetch`/`XMLHttpRequest` 发包原语 → 无法外传数据。
  - 仅用 `$persistentStore.read/write` 读写本地 key `wloc_settings`。
  - 无 `eval`/`new Function`。
- **升级 SHA 前必须重新 review 两个脚本。**
- MITM 范围永远只保留 `gs-loc.apple.com` / `gs-loc-cn.apple.com`，绝不扩大。

## 用法
Shadowrocket 订阅：

```
https://raw.githubusercontent.com/mathew-ma/wloc-vendor/main/modules/wloc.module
```

（订阅本身走 `main` 以便后续调参；真正被执行的两个脚本已按上面的 SHA 钉死。）

选点：iCloud 快捷指令 或 https://wloc-pages.pages.dev/ 。
`qr/` 下为各步骤的二维码，方便 iPhone 直接扫。

## 升级流程（换 upstream 版本时）
1. 拉上游新版 `dist/*.js`，**重新 review**（重点：是否出现 http URL、`$httpClient`/
   `$task.fetch`/`fetch`/`XMLHttpRequest`、`eval`/`new Function`，是否读写 `wloc_settings`
   以外的 key）。
2. 覆盖本仓库 `dist/`，commit，记下新 SHA256 与新 commit SHA。
3. 把 `modules/wloc.module` 两处 `script-path` 的 commit SHA 换成新的，更新本 README 表格。
4. `[MITM] hostname` 永远只保留 `gs-loc.apple.com` / `gs-loc-cn.apple.com`。
