---
sidebar_position: 4
---

# Docusaurus - Docs

- 提供了能有效管理 markdown 檔案的方式
- 主要分為 4 個層級 - 各自頁面 / Sidebars / Versions / Plugins 實例
- [Docs-only Mode](https://docusaurus.io/docs/docs-introduction#docs-only-mode) - 網站只會有 Docs，或是想以 Docs 為優先而將其直接放在 root

## 🔆 建立一頁 Doc

:::warning
Pages 沒有 SideBars，只有 [Docs](https://docusaurus.io/docs/docs-introduction) 有。
:::

- 可以使用 React Components 或 Markdown 建立頁面
- 若要將頁面新增到 Header 的導覽，要去 docusaurus.config.js 設定

### 🛠️ 新增一 React 頁面

- 一個頁面會 export 一個 React Component，可以和平常一樣自行拼裝
- Ex. `/src/pages/helloReact.js`
  :::info
  可自行轉換成 .tsx
  :::

  ```jsx
  import React from "react";
  import Layout from "@theme/Layout";

  export default function HelloReactPage() {
    return (
      <Layout title="Hello" description="Hello React Page">
        <div>
          <p>
            Edit <code>pages/helloReact.js</code> and save to reload.
          </p>
        </div>
      </Layout>
    );
  }
  ```

  儲存後待 Server 自動 Reload 後，便可在 http://localhost:3000/helloReact 上查看此頁

- 此頁面本身不會自動帶入 header 和 footer，需自行從 `@theme/Layout` 引入 Layout 並加進去

### 🛠️ 新增一 Markdown 頁面

- Ex. 在 `docs` 資料夾中建立一個 `greeting.md`
  ```bash
  website # root directory of your site
  ├── docs
  │   └── greeting.md
  ├── src
  │   └── pages
  ├── docusaurus.config.js
  ├── ...
  ```
  :::info
  在 `docs` 底下所有加上 `_` 前綴的檔案都會被視為 Partial Pages，會自動被忽略
  -> import 方式參考 [Markdown Features](https://docusaurus.io/docs/markdown-features/react#importing-markdown)
  :::

### 🛠️ Routing

- 和其他 Static Site Generators ( Ex. Next、Jekyll ) 有相同的 Routing 機制
- 所有建立在 `/src/pages/` 資料夾底下的 **JavaScript** 檔案，皆會被自動轉換成一個頁面
- 參考範例
  - `/src/pages/index.js` → `[baseUrl]`
  - `/src/pages/foo.js` → `[baseUrl]/foo`
  - `/src/pages/foo/test.js` → `[baseUrl]/foo/test`
  - `/src/pages/foo/index.js` → `[baseUrl]/foo/`
- 針對 page 所撰寫的樣式，應集中放在 page 檔案旁邊

  - Ex. support 頁面的 css，應直接放在其旁邊，如下

    ```bash
    # 記得把 CSS Module 引入 Component 中

    my-website
    ├── src
    │   └── pages
    │       ├── styles.module.css
    │       ├── index.js
    │       ├── _ignored.js
    │       ├── _ignored-folder
    │       │   ├── Component1.js
    │       │   └── Component2.js
    │       └── support
    │           ├── index.js
    │           └── styles.module.css
    ```

    :::info
    預設情況下，
    由 `_` 開頭、`.test.js` 結尾、`__tests__` 資料夾中的檔案，
    將會被忽略而不會為其產生 Route ( 可透過 `exclude` option 調整 )
    :::

- 如果不小心加上重複的 Route，Docusaurus 會提醒你，但仍會成功產出，並以新建立的覆蓋舊的

### 🛠️ Doc Front Matter

- 用來幫 Doc Page 提供 Meta Data，就算沒有加，Docusaurus 也會自動幫你加上所有必需的 Meta Data 資訊 ( Ex. Doc Tags )
- 常用欄位 ( [更多](https://docusaurus.io/docs/api/plugins/@docusaurus/plugin-content-docs#markdown-front-matter) )
  - `id` - 用來對應這頁 Doc 用的，預設為從 `Docs` 開始的相對路徑
  - `title` - 用在多處顯示 (Ex. Sidebar)，用設為此頁 Doc 的 Title 或 id
  - `sidebar_position` - 可以調整在 sidebar 中顯示的順序位置
  - `sidebar_custom_props` - 傳入客製化的[設定](https://docusaurus.io/docs/sidebar#passing-custom-props)
  - `draft` - 只會運作於開發環境中作為草稿
- Example:

  ```md
  ---
  id: doc-markdown
  title: Docs Markdown Features
  hide_title: false
  hide_table_of_contents: false
  sidebar_label: Markdown
  sidebar_position: 3
  pagination_label: Markdown features
  custom_edit_url: https://github.com/facebook/docusaurus/edit/main/docs/api-doc-markdown.md
  description: How do I find you when I cannot solve this problem
  keywords:
    - docs
    - docusaurus
  image: https://i.imgur.com/mErPwqL.png
  slug: /myDoc
  last_update:
    date: 1/1/2000
    author: custom author name
  ---

  # Markdown Features

  My Document Markdown content
  ```

### 🛠️ Doc Tags

- 用來歸類 Doc Pages，也會被用來產生 [Docs 的 Sidebar](https://docusaurus.io/docs/sidebar)
  ```md
  ---
  id: doc-with-tags
  title: A doc with tags
  tags:
    - Demo
    - Getting started
  ---
  ```
- 也可以寫成 `tags: [Demo, Getting started]` ( 參考 [Yaml Array Syntax](https://www.w3schools.io/file/yaml-arrays/) )

## 🔆 規劃 Docs 存放的資料夾結構

- 在 Docs 中存放 Markdown 的方式，會影響最後內容的檢視方式
- 最好將其視為 Sidebar 的鏡相，避免後續還需要自行定義 `sidebar.js`
- 若想修改 URL，用 `slug` 調整就好
- **Document ID**
  - 每個 Document 都會有自己的 Unique ID
  - 預設為從 docs root 開始的相對路徑，不包含結尾的副檔名
    - Ex. `guide/hello.md` 的 ID 為 `guide/hello` / `greeting.md` 的 ID 為 `greeting`
      ```bash
      website # Root directory of your site
      └── docs
         ├── greeting.md
         └── guide
            └── hello.md
      ```
  - 也可以由使用者自行定義，會以使用者定義的 ID 優先於預設值
    - Ex. 延續上方範例，`guide/hello.md` 並定義如下，結果 ID 將為 `guide/part1`
      ```md
      ---
      id: part1
      ---
      ```
  - ID 的用途 - 在手寫 Sidebar 時做關連, or when using docs-related layout components or hooks.

## 🔆 Doc URLs

- 預設為從 docs 資料夾開始的相對路徑
  - Ex. `hello.md` 的 URL 將會是 `/docs/guide/hello`
    ```md
    website # Root directory of your site
    └── docs
    └── guide
    └── hello.md
    ```
- 可以用 `slug` 來自定義

  - Ex. 延續上方範例，如下方定義後的結果為 `/docs/bonjour`

    ```md
    ---
    slug: /bonjour
    ---

    # 撰寫方式

    # 絕對路徑 - `slug: /mySlug`、`slug: /`

    # 相對路徑 - `slug: mySlug`、`slug: ./../mySlug`
    ```

  - `slug` 會被加到 doc plugin 的 `routeBasePath` ( 預設為 /docs )
  - 也可以將其設置為 root
    ```md
    ---
    id: my-home-doc
    slug: /
    ---
    ```

## 🔆 建立 Multi Docs Instance

- `@docusaurus/plugin-content-docs` 能支援建立多個 Docs
- 使用情境 - 希望網站能有多個分開的 Docs，而各自永自己的版本、生命週期
  :::warning
  若 Doc 都非常大，應各自獨立建立 Docusaurus 專案
  :::
  - 區分不同裝置的 Documentation (Ex. Android & IOS)
  - 區分 versioned & unversioned 的 Doc
    - 可能有些部分就會一直在那邊，並不需要為他們增加 Version
    - Ex. [/docs/\*](https://docusaurus.io/docs) (Versioned) & [/community/\*](https://docusaurus.io/community/support) (Unversioned)

### 🛠️ 設置

- 假設分為 product (versioned) 和 community (unversioned) 的 Docs

  - 使用 `preset` (`@docusaurus/preset-classic` 已經涵蓋了一個 docs plugin 實例)

    ```javascript
    // docusaurus.config.js
    module.exports = {
      presets: [
        [
          "@docusaurus/preset-classic",
          {
            docs: {
              // id: 'product', // 省略 => 作為 default instance
              path: "product",
              routeBasePath: "product",
              sidebarPath: require.resolve("./sidebarsProduct.js"),
              // ... 其他 options
            },
          },
        ],
      ],
      plugins: [
        [
          "@docusaurus/plugin-content-docs",
          {
            id: "community", // Other Instance 要設置 id
            path: "community",
            routeBasePath: "community",
            sidebarPath: require.resolve("./sidebarsCommunity.js"),
            // ... 其他 options
          },
        ],
      ],
    };
    ```

  - 不使用 `preset`
    :::warning
    記得分別設置 id，不設置的會作為 default
    :::
    ```javascript
    // docusaurus.config.js
    module.exports = {
      plugins: [
        [
          "@docusaurus/plugin-content-docs",
          {
            // id: 'product', // 不指定 id => 作為預設 (較重要)
            path: "product",
            routeBasePath: "product",
            sidebarPath: require.resolve("./sidebarsProduct.js"),
            // ... 其他 options
          },
        ],
        [
          "@docusaurus/plugin-content-docs",
          {
            id: "community", // 其他的記得指定 id
            path: "community",
            routeBasePath: "community",
            sidebarPath: require.resolve("./sidebarsCommunity.js"),
            // ... 其他 options
          },
        ],
      ],
    };
    ```

### 🛠️ 指定 Doc Navbar Items 要指向的 Instance

- 各個 Docs 相關的 [Theme Navbar Items](https://docusaurus.io/docs/api/themes/configuration#navbar) 選擇性可以傳入 `docsPluginId` 來做指定
  ```javascript
  module.exports = {
    themeConfig: {
      navbar: {
        items: [
          {
            type: "docsVersionDropdown",
            docsPluginId: "ios",
          },
          {
            type: "docsVersionDropdown",
            docsPluginId: "android",
          },
        ],
      },
    },
  };
  ```
