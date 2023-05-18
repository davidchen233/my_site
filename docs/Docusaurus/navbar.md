---
sidebar_position: 3
---

# Docusaurus - Navbar

- 在 `docusaurus.config.js` 中調整

## 相關欄位

| Name           | Type                                                                    | Default     | Description               |
| -------------- | ----------------------------------------------------------------------- | ----------- | ------------------------- |
| `title`        | `string`                                                                | `undefined` | Navbar 的標題             |
| `logo`         | [查看](https://docusaurus.io/docs/api/themes/configuration#navbar-logo) | `undefined` | 客製化 Logo 物件          |
| `items`        | `NavbarItem[]`                                                          | `[]`        | 要顯示的 Item，詳情看下面 |
| `hideOnScroll` | `boolean`                                                               | `false`     | 是否往下滾動時隱藏        |
| `style`        | `primary` 或 `dark`                                                     | 同 theme    | 無視 dark/light 獨立設置  |

## [Navbar Items](https://docusaurus.io/docs/api/themes/configuration#navbar-items)

- Example
  ```javascript
  // Edit <themeConfig.navbar.items>
  module.exports = {
    themeConfig: {
      navbar: {
        items: [
          {
            type: "doc",
            position: "left",
            docId: "introduction",
            label: "Docs",
          },
          { to: "blog", label: "Blog", position: "left" },
          {
            type: "docsVersionDropdown",
            position: "right",
          },
          {
            type: "localeDropdown",
            position: "right",
          },
          {
            href: "https://github.com/facebook/docusaurus",
            position: "right",
            className: "header-github-link",
            "aria-label": "GitHub repository",
          },
        ],
      },
    },
  };
  ```
  :::warning
  items 會因 `type` 的不同而有不同的行為
  :::

### Navbar Link

- 預設情況下，Navbar Items 都是一般的連結而已 (內部 | 外部)
- 相關屬性 ([更多](https://docusaurus.io/docs/api/themes/configuration#navbar-link))

  | Name        | Type              | Default  | Description                               |
  | ----------- | ----------------- | -------- | ----------------------------------------- |
  | `type`      | `default`         | 選填     | 將此 Item 種類設為 Link                   |
  | `label`     | `string`          | 不得為空 | 此 Item 的顯示名稱                        |
  | `html`      | `string`          | 選填     | 和 `label` 相同，只是選染 html 而非純文字 |
  | `to`        | `string`          | 不得為空 | 內部 client-side routing                  |
  | `href`      | `string`          | 不得為空 | 用來設置外部連結                          |
  | `position`  | `left` 或 `right` | `left`   | 顯示在左或右側                            |
  | `className` | `string`          | `''`     | 客製化連結樣式                            |

- Example
  ```javascript
  // docusaurus.config.js
  module.exports = {
    themeConfig: {
      navbar: {
        items: [
          {
            to: "docs/introduction",
            // Only one of "to" or "href" should be used
            // href: 'https://www.facebook.com',
            label: "Introduction",
            // Only one of "label" or "html" should be used
            // html: '<b>Introduction</b>'
            position: "left",
            activeBaseRegex: "docs/(next|v8)",
            target: "_blank",
          },
        ],
      },
    },
  };
  ```

### Navbar Dropdown ( `type = dropdown` )

- 下拉會有自己的一個 Items 陣列 (只能使用 **Link-Like** Item)
  :::info
  以下這些也可以使用一般 Default Navbar Link 的屬性 (參考上面)
  :::
  - [Navbar Link](https://docusaurus.io/docs/api/themes/configuration#navbar-link)
  - [Navbar Doc Link](https://docusaurus.io/docs/api/themes/configuration#navbar-doc-link)
  - [Navbar Docs Version](https://docusaurus.io/docs/api/themes/configuration#navbar-docs-version)
  - [Navbar Doc Sidebar](https://docusaurus.io/docs/api/themes/configuration#navbar-doc-sidebar)
  - [Navbar with custom HTML](https://docusaurus.io/docs/api/themes/configuration#navbar-with-custom-html)
- 相關屬性

  | Name       | Type              | Default  | Description                 |
  | ---------- | ----------------- | -------- | --------------------------- |
  | `type`     | `dropdown`        | 選填     | 將此 Item 種類設為 Dropdown |
  | `label`    | `string`          | 不得為空 | 此 Item 的顯示名稱          |
  | `items`    | `LinkLikeItem[]`  | 不得為空 | Dropdown 中要放的連結       |
  | `position` | `left` 或 `right` | `left`   | 顯示在左或右側              |

  ```javascript
  // docusaurus.config.js
  module.exports = {
    themeConfig: {
      navbar: {
        items: [
          {
            type: "dropdown",
            label: "Community",
            position: "left",
            items: [
              {
                label: "Facebook",
                href: "https://www.facebook.com",
              },
              {
                type: "doc",
                label: "Social",
                docId: "social",
              },
              // ... more items
            ],
          },
        ],
      },
    },
  };
  ```

### Navbar Doc Link ( `type = doc` )

- 會連到指定 `docId` 的 Doc 頁面去，只要是在有同個 Sidebar 的 Doc 中瀏覽，都會維持 class `navbar__link--active`
- 相關屬性

  | Name           | Type              | Default   | Description                 |
  | -------------- | ----------------- | --------- | --------------------------- |
  | `type`         | `doc`             | 不得為空  | 將此 Item 種類設為 Doc Link |
  | `dockId`       | `string`          | 不得為空  | 要連到的 Docs 之 ID         |
  | `label`        | `string`          | docId     | 此 Item 的顯示名稱          |
  | `position`     | `left` 或 `right` | `left`    | 顯示在左或右側              |
  | `docsPluginId` | `string`          | `default` | 對應到的 Docs Plugin ID     |

  ```javascript
  // docusaurus.config.js
  module.exports = {
    themeConfig: {
      navbar: {
        items: [
          {
            type: "doc",
            position: "left",
            docId: "introduction",
            label: "Docs",
          },
        ],
      },
    },
  };
  ```

### Navbar Linked to a Sidebar ( `type = docSidebar` )

- 會連到指定 sidebar 的第一份 Doc 頁面
- 相關屬性

  | Name           | Type              | Default   | Description                          |
  | -------------- | ----------------- | --------- | ------------------------------------ |
  | `type`         | `docSidebar`      | 不得為空  | 種類設為 Sidebar 的第一頁 Doc        |
  | `sidebarId`    | `string`          | 不得為空  | 要連到的 Sidebar 之 ID               |
  | `label`        | `string`          | docId     | 此 Item 的顯示名稱                   |
  | `position`     | `left` 或 `right` | `left`    | 顯示在左或右側                       |
  | `docsPluginId` | `string`          | `default` | 對應到 Sidebar 所屬的 Docs Plugin ID |

  ```javascript
  // docusaurus.config.js
  module.exports = {
    themeConfig: {
      navbar: {
        items: [
          {
            type: "docSidebar",
            position: "left",
            sidebarId: "api",
            label: "API",
          },
        ],
      },
    },
  };
  ```

  ```javascript
  // sidebars.js
  module.exports = {
    tutorial: [
      {
        type: "autogenerated",
        dirName: "guides",
      },
    ],
    api: [
      "cli", // The navbar item will be linking to this doc
      "docusaurus-core",
      {
        type: "autogenerated",
        dirName: "api",
      },
    ],
  };
  ```
