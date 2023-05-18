---
sidebar_position: 1
---

# Docusaurus - 介紹

##### link: https://docusaurus.io/

## 關於 Docusaurus

- 快速幫你產生優美的 Document Site
- 使用 Markdown & MDX (方便直接 Github 等服務編輯、管理)
- 擁有良好的 SEO
- 提供許多功能 Ex. versioning、i18n、search...
- 使用 React 開發 (可以自由建立自己的 Components)
- 可以使用內建的樣板，再加上進階的功能和擴充
- 為 Static-site Generator，產出的靜態檔能輕易部屬到 Github Pages、Netlify、Vercel 等服務

## 安裝 & 初始化

- 需安裝 Node.js 16.14 以上的版本
- 建立專案

  ````bash
  npx create-docusaurus@latest my-website classic

  # 若使用 TypeScript (加上 --typescript)

  > 也可以直接在原專案做初始化
  ```bash
  npm init docusaurus
  ````

  :::warning
  目前使用 `pnpm` 安裝會有 Type Error，
  建議先用 `npm` 或 `yarn`
  :::

- 在本地以開發模式啟動專案 (使用 classic 主題)

  ```bash
  cd my-website
  npm run start

  # http://localhost:3000
  # 支援 Hot Reload，能及時看到修改結果
  ```

  > 此時畫面上就會有最基本的功能，包含了 ` @docusaurus/preset-classic` 所提供的 Blog、Custom Pages 和一個 CSS Framework (涵蓋支援 Dark Mode)

## 專案結構

- 初始化後的專案結構如下
  ```powershell=
  my-website
  ├── blog
  │   ├── 2019-05-28-hola.md
  │   ├── 2019-05-29-hello-world.md
  │   └── 2020-05-30-welcome.md
  ├── docs
  │   ├── doc1.md
  │   ├── doc2.md
  │   ├── doc3.md
  │   └── mdx.md
  ├── src
  │   ├── css
  │   │   └── custom.css
  │   └── pages
  │       ├── styles.module.css
  │       └── index.js
  ├── static
  │   └── img
  ├── docusaurus.config.js
  ├── package.json
  ├── README.md
  ├── sidebars.js
  └── yarn.lock
  ```
- `/blog/` - 拿來放 Blog 的 Markdowns
  - 若已把 Blog 的 Plugin disable 掉，則可以刪掉
  - 若要改名，記得要去調整 `path`
  - [詳情參考](https://docusaurus.io/docs/blog)
- `/docs/` - 拿來放 Docs 的 Markdowns

  - 可以在 `sidebar.js` 來調整導覽顯示順序
  - 若已把 Doc 的 Plugin disable 掉，則可以刪掉
  - 若要改名，記得要去調整 `path`
  - [詳情參考](https://docusaurus.io/docs/docs-introduction)

- `/src/` - 用來放非 Documentation 的檔案

  - Ex. pages、custom React Component
  - 沒有嚴格規定要放這裡，但能幫助集中管理、設置
  - `/src/pages/` 在這裡的 `JSX / TSX / MDX` 檔，都會被轉換成實際頁面
  - [詳情參考](https://docusaurus.io/docs/creating-pages)

- `/static/` - 放靜態檔案

  - 這裡的東西會被複製到最後產生之 `build` 資料夾中的 root

- `/docusaurus.config.js` - 網站設定檔

- `/package.json` - 因為只是一般的 React App，因此可以使用自己想用的 package

- `/sidebars.js` - 用來定義 Documentation 導航的順序

## 採用 Monorepos

- 如果要將 Docusaurus 加上現行專案，使用 Monorepo 就是一種解決方式
- Monorepo 讓相關的專案能放在相同的 Repo 中共享 Dependencies
- Ex. 開發過程中會使用本地的 packages 來展示新功能，而非依賴釋出的版本
  此時協作者如文件維護，可以共享使用相同的 package 來更新文件
  ```bash
  my-monorepo
  ├── package-a # Another package, your actual project
  │   ├── src
  │   └── package.json # Package A's dependencies
  ├── website   # Docusaurus root
  │   ├── docs
  │   ├── src
  │   └── package.json # Docusaurus' dependencies
  ├── package.json # Monorepo's shared dependencies
  ```
  > 這樣的情況下，應在 `./my-monorepo` 中跑 `npx create-docusaurus`
  - 如果要使用 Hosting Provider 如 Netlify 或 Vercel，會需要將網站的 `Base Directory`
    改為 Docusaurus 的 root，以上述範例而言就是 `./website` ([設置詳情](https://docusaurus.io/docs/deployment#deploying-to-netlify))
- 可以參考 [yarn 文件](https://yarnpkg.com/features/workspaces) 來建置 monorepo
  - 並非只能用 yarn，只是這是常見的方式
  - 其他相關工具有 pnpm、lerna、Nx、TurboRepo...
- 相關文章
  - [Monolith vs Multi-Repo vs Monorepo](https://www.cythilya.tw/2023/01/28/monolith-vs-multi-repo-vs-mono-repo/)

## Build

- Docusaurus 為 Static Website Generator
  - 會將專案檔案 build 成靜態檔到指定資料夾中 (預設為 `/build`)
  - 接著將這些靜態檔放到 web server 上被瀏覽 Ex. [GitHub pages](https://pages.github.com/)、[Vercel](https://vercel.com/) 或 [Netlify](https://www.netlify.com/)
- 打包指令

  ```bash
  npm run build

  # 或是使用其他 Package Manager Ex. yarn、pnpm
  ```

- [更多關於 Depoyment](https://docusaurus.io/docs/deployment)

## 更新 Docusaurus 版本

1.  直接修改 `package.json` 中的版本號
    > ⚠️ 注意，`@docusaurus/-namespaced packages` 應使用相同的版本號
    ```json
    {
      "dependencies": {
        "@docusaurus/core": "2.4.0",
        "@docusaurus/preset-classic": "2.4.0"
        // ...
      }
    }
    ```
2.  用 Package Manager 做 install
    ```bash
    npm install
    ```
3.  檢查版本是否正確更新
    `bash
npx docusaurus --version
` > 若使用 yarn，可以使用
    `yarn upgrade @docusaurus/core@latest @docusaurus/preset-classic@latest`

## Docusaurus & TypeScript

### 初始化

- 使用 `--typescript` 初始化 Docusaurus 專案
  ```bash
  npx create-docusaurus@latest my-website classic --typescript
  ```

### 設置

- 將 `@docusaurus/module-type-aliases` 加進專案
  ```bash
  npm install --save-dev typescript @docusaurus/module-type-aliases @tsconfig/docusaurus
  # 也可以使用其他 Package Mangament
  ```
- 新增 `tsconfig.json`，並新增以下內容
  ```json
  {
    "extends": "@tsconfig/docusaurus/tsconfig.json",
    "compilerOptions": {
      "baseUrl": "."
    }
  }
  ```
  > 並非用來編譯，而是用來提供更好的編輯器開發體驗

### Typing Config File

- 推薦使用 [JSDoc type annotations](https://www.typescriptlang.org/docs/handbook/jsdoc-supported-types.html)
  > 在 Docusaurus 專案中不能使用 TypeScript 的 Config File 進行編譯上的設置，
        因此加上 `@ts-check` 來確保執行 `npx tsc` 時，型別能正確被檢查
- `docusaurus.config.js`

  ```typescript
  // @ts-check

  /** @type {import('@docusaurus/types').Plugin} */
  function MyPlugin(context, options) {
    return {
      name: "my-plugin",
    };
  }

  /** @type {import('@docusaurus/types').Config} */
  const config = {
    title: "Docusaurus",
    tagline: "Build optimized websites quickly, focus on your content",
    organizationName: "facebook",
    projectName: "docusaurus",
    plugins: [MyPlugin],
    presets: [
      [
        "@docusaurus/preset-classic",
        /** @type {import('@docusaurus/preset-classic').Options} */
        {
          docs: {
            path: "docs",
            sidebarPath: "sidebars.js",
          },
          blog: {
            path: "blog",
            postsPerPage: 5,
          },
        },
      ],
    ],
    themeConfig:
      /** @type {import('@docusaurus/preset-classic').ThemeConfig} */
      {
        colorMode: {
          defaultMode: "dark",
        },
        navbar: {
          hideOnScroll: true,
          title: "Docusaurus",
          logo: {
            alt: "Docusaurus Logo",
            src: "img/docusaurus.svg",
            srcDark: "img/docusaurus_keytar.svg",
          },
        },
      },
  };

  module.exports = config;
  ```

### Swizzling TypeScript theme components ([連結](https://docusaurus.io/docs/typescript-support#swizzling-typescript-theme-components))
