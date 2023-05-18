---
sidebar_position: 6
---

# Docusaurus - Docs の Versions

:::info
💡**Docusaurus 名詞解釋**

**Current Version** (開發中) - 放在 `./docs` 資料夾底下，會被標記為 Next，能從 `/docs/next/*` 的路由進入

**Latest Version** (最新版) - 從網頁進入 Docs 預設所顯示的版本，通常能從 `/docs` 的路由進入
:::

## 🔆 關於 Versioning

- 一般情況並不需使用，除非專案的更新迅速，否則只是增加維護上的困難和增加 Build Time

## 🔆 Versioning 行為模式

1. 釋出 v1，並馬上開始開發 v2
   :::info
   Docusaurus 預設較符合第一種方式，開發中的 version 會被標記為 `next`，
   也可以選擇不發布此版本
   :::
   - v2 ( **Current** ) 會在 `./docs` 中，路由為 `example.com/docs/next`
   - v1 ( **Latest** ) 會在 `./versioned_docs/version-1` 中，路由為 `example.com/docs`
2. 釋出 v1 後，會先持續維護他，而不會馬上進行 v2
   - 與其凍結 v1 並需要同時維護兩份 docs (`./docs` + `./versioned_docs/version-1.0.0`)，
     可以考慮假裝 current 就是 v1，並調整標籤名稱
     ```javascript title="docusaurus.config.js"
     module.exports = {
       presets: [
         '@docusaurus/preset-classic',
         docs: {
           lastVersion: 'current',
           versions: {
             // 在這裡做調整
             current: {
               label: '1.0.0',
               path: '1.0.0',
             },
           },
         },
       ],
     };
     ```
   - 這樣 `./docs` 會被服務於 `/docs/1.0.0` (而不是 `/docs/next`)，
     而 `1.0.0` 會變成預設顯示的版本

## 🔆 Versioned Docs 專案結構範例

- 檔案結構

  ```bash
  website
  ├── sidebars.json        # Current Version 所使用的 Sidebar
  ├── docs                 # 存放 Current Version Docs 的資料夾
  │   ├── foo
  │   │   └── bar.md       # https://mysite.com/docs/next/foo/bar
  │   └── hello.md         # https://mysite.com/docs/next/hello
  ├── versions.json        # 用來記錄可用的 Versions
  ├── versioned_docs
  │   ├── version-1.1.0
  │   │   ├── foo
  │   │   │   └── bar.md   # https://mysite.com/docs/foo/bar
  │   │   └── hello.md
  │   └── version-1.0.0
  │       ├── foo
  │       │   └── bar.md   # https://mysite.com/docs/1.0.0/foo/bar
  │       └── hello.md
  ├── versioned_sidebars
  │   ├── version-1.1.0-sidebars.json
  │   └── version-1.0.0-sidebars.json
  ├── docusaurus.config.js
  └── package.json
  ```

- `versions.json` - 各可用版本的資訊，由新到舊排列

  | Path                                  | Version        | URL               |
  | ------------------------------------- | -------------- | ----------------- |
  | versioned_docs/version-1.0.0/hello.md | 1.0.0          | /docs/1.0.0/hello |
  | versioned_docs/version-1.1.0/hello.md | 1.1.0 (latest) | /docs/hello       |
  | docs/hello.md                         | current        | /docs/next/hello  |

## 🔆 Versioning 操作流程

### 🛠️ 建立新版本

1. 確認 Current Version (`./docs` 資料夾) 的檔案已經準備好被凍結
2. 跑 CLI 並輸入新版號
   ```bash
   npm run docusaurus docs:version 1.1.0
   ```
   :::info
   建立新版號後，背後會發生的事
   1. 把 `docs/` 資料夾整個複製到新的 `versioned_docs/version-[versionName]/` 資料夾中
   2. 根據你 sidebar 的設置建立 `versioned_sidebars/version-[versionName]-sidebars.json`
   3. 將新版號加入 `versions.json`
      :::
3. 繼續建立新文件

   - 把檔案放進對應版本的資料夾
   - 設置 sidebar 來關連到新建立的檔案頁面

     ```bash
     # Current Version
     docs/new.md # 幫 Currrent Version 建立的新檔案
     sidebars.js # 編輯 Current Version 的 Sidebar

     # Older Version
     versioned_docs/version-1.0.0/new.md # 幫 Old Version 建立的新檔案
     versioned_sidebars/version-1.0.0-sidebars.json # 編輯 Old Version 的 Sidebar
     ```

### 🛠️ 更新既有的版本

- 直接更改當個版本中的檔案並提交 => 只會影響到對應版本而已
  - Ex. 修改了 `versioned_docs/version-2.6/` 中的檔案，只會影響 `2.6` 版
- 若有增減檔案，記得調整對應版本的 Sidebar

### 🛠️ 移除既有版本

1. 從 `version.json` 中移除此版本
   ```json
   [
     "2.0.0",
     "1.9.0"
     // "1.8.0"
   ]
   ```
2. 把此版本的資料夾整個刪除 ( Ex. `versioned_docs/version-1.8.0` )
3. 將此版本的 sidebars file 移除 ( Ex. `versioned_sidebars/version-1.8.0-sidebars.json` )

## 🔆 Versioning 注意事項

1. 只在真的需要的情況下建立 Version (Ex. 只在大版號更新時)
2. 儘量將存在的 Version 數量限制在 <mark>10</mark> 個以內，很多版本可能被捨棄沒人閱讀了
3. 在 Docs 中必須使用 <mark>絕對路徑</mark> !否則建立 Version 時會導致路徑錯誤
   => 可以用 Docusaurus 提供的 `@site` (指向 website 資料夾) alias
   ```javascript
   // import Foo from '../src/components/Foo'
   import Foo from "@site/src/components/Foo";
   ```
4. 需關連其他 Docs 頁面時使用 <mark>相對路徑</mark>，並加上副檔名，讓 Docusaurus 在 build 的時候能夠正確改寫他們的 URL

   ```markdown
   The [@hello](hello.mdx#paginate) document is great!

   See the [Tutorial](../getting-started/tutorial.mdx) for more info.
   ```

5. 要分辨 assets 要放在全域或是分別放在各版本中

   - 版本中 - 放在 docs version 中，並使用 <mark>相對路徑</mark>

     ```markdown
     ![img alt](./myImage.png)

     [download this file](./file.pdf)
     ```

   - 全域 - 放在 `/static` 資料夾中並使用 <mark>絕對路徑</mark>

     ```markdown
     ![img alt](/myImage.png)

     [download this file](/file.pdf)
     ```

## 🔆 Versioning 相關 Configuration 參數

- [Configuration Example](https://docusaurus.io/docs/next/api/plugins/@docusaurus/plugin-content-docs#ex-config)

:::info
以下為 `@docusaurus/plugin-content-docs` 所提供的 Configuration
:::

- `disableVersioning` ( type: `boolean` | default: `false` )

  - Disable versioning 的機制，讓網頁上只包含 Current Version
  - 若設為 `false`，記得 `includeCurrentVersion` 要設為 `true`

- `includeCurrentVersion` ( type: `boolean` | default: `true` )

  - 控制是否發布 Current Version (`./docs`) 到頁面上
  - 如果 Current Version 還在開發中，記得關掉

- `lastVersion` ( type: `string` | default: `versions.json` 中的第一筆 )

  - 設置 `latest version` (`/docs` Route) 應指向哪個版本

- `onlyIncludeVersions` ( type: `string[]` | default: all )

  - 設置紀錄於 `versions.json` 要發布的版本
  - 開發時可以先將他減少到 2~3 個 version 當作 preview，來減少 build time

- `versions` ( type: `{[version]: VersionsConfig}` | default: `{}` )

  - 定義各 versions 的 MetaData
  - type `VersionsConfit`

    ```typescript
    type VersionConfig = {
      /**
       * The base path of the version, will be appended to `baseUrl` +
       * `routeBasePath`.
       */
      path?: string;
      label?: string;
      banner?: "none" | "unreleased" | "unmaintained";
      badge?: boolean;
      /** Prevents search engines from indexing this version */
      noIndex?: boolean;
      className?: string;
    };

    type VersionsConfig = { [versionName: string]: VersionConfig };
    ```

    - `label` - 在 Versions Dropdown、Banner、Badges 上顯示的 label
    - `path` - 設置此版本所使用的 path
      - 預設 - latest:`/`、current:`/next`、[version]:`/[version]`
    - `banner` - 定義在 doc 頁面最上面顯示的 banner
      ![Unmaintained Banner](https://hackmd.io/_uploads/B1M7mqOVn.png)
      ![Unreleased Banner](https://hackmd.io/_uploads/rJ4Dwz9N2.png)
      - 預設 lastest 版本以上的，會是 `unreleased`，以下的會是 `unmaintained`
    - `badge` - 在 doc 頁面上方是否顯示對應版本的標籤
      ![](https://hackmd.io/_uploads/Hy0HQqu42.png)
    - `className` - 在 `<html>` 標籤上針對此版本加上客製化的 className

## 🔆 Versioned Navbar Items [🔗](https://docusaurus.io/docs/api/themes/configuration#navbar-items)

- 下列 items 可以會依照不同 version 產生對應的 routes，不須自行設置
  - items 對應判斷順序: active ( 瀏覽中 ) => preferred ( 上次瀏覽 ) => latest
- [`doc`](https://docusaurus.io/docs/api/themes/configuration#navbar-doc-link) - 到 doc 頁面的連結
- [`docSidebar`](https://docusaurus.io/docs/api/themes/configuration#navbar-doc-sidebar)- 到 Sidebar 第一個 item 的連結
- [`docsVersion`](https://docusaurus.io/docs/api/themes/configuration#navbar-docs-version) - 到此 Version 的 Main Doc (`/`)
- [`docsVersionDropdown`](https://docusaurus.io/docs/api/themes/configuration#navbar-docs-version-dropdown) - 包含所有 Version 的 Dropdown

## 🔆 針對 Multi Docs Instance 分別做版本控管

### 🛠️ 不同 Instance 的 Versions 存放路徑

- 各個 plugin 的 instance，都會分別用獨立的資料夾存放 versioned docs
  - 預設 plugin instance 的存放方式 (沒有設定 `id` 的)
    - `website/versions.json`
    - `website/versioned_docs`
    - `website/versioned_sidebars`
  - 其他 (有設定 `id` 的)
    - `website/[pluginId]_versions.json`
    - `website/[pluginId]_versioned_docs`
    - `website/[pluginId]_versioned_sidebars`

### 🛠️ 針對不同 Instance 建立新 versions

- 各個 plugin instance 會有自己的 CLI 指令來建立自己的新 versions
  - 查看對應的指令跑
    ```powershell
    npm run docusaurus -- --help
    yarn docusaurus --help
    pnpm run docusaurus -- --help
    ```
  - 對預設的 plugin instance 新增 version
    ```powershell
    npm run docusaurus docs:version 1.0.0
    yarn docusaurus docs:version 1.0.0
    pnpm run docusaurus docs:version 1.0.0
    ```
  - 對其他 plugin instance 新增 version
    ```powershell
    npm run docusaurus docs:version:community 1.0.0
    yarn docusaurus docs:version:community 1.0.0
    pnpm run docusaurus docs:version:community 1.0.0
    ```
