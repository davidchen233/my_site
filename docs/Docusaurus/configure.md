---
sidebar_position: 2
---

# Docusaurus - 設置

- `docusaurus.config.js`
- 在建立 docusaurus 專案時，就會給你預設的設置，分為以下幾類

## Site Metadata

- 用來設置全站主要的 MetaData
- Ex. `title`、`url`、`baseUrl`、`favicon`

## Deployment Configurations

- 用來設置如 `projectName`、`organizationName`
- `deploymentBranch` 則用於跑 `deploy` 指令時
- [更多 Deploy 相關](https://docusaurus.io/docs/deployment)

## Theme、Plugin、Preset Configuration

- 他們都只是 npm packages 而已

  ```javascript
  module.exports = {
    // ...
    plugins: [
      "@docusaurus/plugin-content-blog",
      "@docusaurus/plugin-content-pages",
    ],
    themes: ["@docusaurus/theme-classic"],
  };
  ```

  :::info
  Docusaurus 支援 [module shorthands](https://docusaurus.io/docs/using-plugins#module-shorthands)，能簡化設置。

  ```javascript=
  module.exports = {
    // ...
    plugins: ['content-blog', 'content-pages'],
    themes: ['classic'],
  };
  ```

  :::

- 也可以從本地資料夾載入

  ```javascript
  const path = require("path");

  module.exports = {
    // ...
    themes: [path.resolve(__dirname, "/path/to/docusaurus-local-theme")],
  };
  ```

- 如果想獨立設置 plugin 或 themes 的 options，可以用 Array，裝著 name 和 options object 來設置

  ```javascript
  module.exports = {
    // ...
    plugins: [
      [
        "content-blog",
        {
          path: "blog",
          routeBasePath: "blog",
          include: ["*.md", "*.mdx"],
          // ...
        },
      ],
      "content-pages",
    ],
  };
  ```

- 若想對會在 preset 中會被 bundle 的 plugin 或 theme 設置 options，可透過 `presets`

  ```javascript
  module.exports = {
    // ...
    presets: [
      [
        '@docusaurus/preset-classic',
        {
          // 對應到 @docusaurus/plugin-content-docs
          docs: {
            sidebarPath: require.resolve('./sidebars.js'),
          },

          // 對應到  @docusaurus/theme-classic
          theme: {
            customCss: [require.resolve('./src/css/custom.css')],
          },
        },
      ],
    ],
  };

  // 也可以用縮寫
  presets: [['classic', {...}]]
  ```

## Custom Configurations

- 會將客製化的設置和內部設置分開，放進 `customFields` 中

  ```javascript
  module.exports = {
    // ...
    customFields: {
      image: "",
      keywords: [],
    },
    // ...
  };
  ```

## 在 Components 中存取 Configuration

- 全站的 Components 都能自由透過 React Context 存取 `siteConfig`
- 基本範例

  ```jsx
  import React from "react";
  import useDocusaurusContext from "@docusaurus/useDocusaurusContext";

  const Hello = () => {
    const { siteConfig } = useDocusaurusContext();
    const { title, tagline } = siteConfig;

    return <div>{`${title} · ${tagline}`}</div>;
  };
  ```

  :::info
  如果只是要將設置用於 Client 端，可以自行建立 JS 檔並以 ES6 modules 的方式做 import，
  沒有必要將其放入 docusaurus.config.js
  :::

## 客製化 Babel Configuration

- 在 Docusaurus 的專案中，會自動幫你在 root 建立一個 `babel.config.js`
  ```javascript
  module.exports = {
    presets: [require.resolve("@docusaurus/core/lib/babel/preset")],
  };
  ```
- 大多情況這樣就夠了，但如果想進行客製化，可以直接編輯此檔案
  :::info
  修改此檔案時若想要看修改結果，須重啟 Docusaurus Dev Server
  :::
