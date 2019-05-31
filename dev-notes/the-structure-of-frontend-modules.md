# The structure of Frontend modules

## 出發點：不同的 frontend server 會區分成不同的 git repo

為什麼我們會這樣分？

- 分開的好處：
  - 可將 Github 上設定 hooks，綁定 CI/CD
  - 不同的 server 本身就預設功能上是分離的，所以 source code、build process 等劃分成不同 repo 很直觀可以理解

- 分開的壞處：
  - 跨 repo、同時要對多個 repo 進行的作業比較麻煩，例如：
    - 跨 repo 的測試要分別起 dev-server（但如果有跨語言的 server，就不可能放在同一個 repo，應該要另寫一個程式處理這種需求）
    - refactoring（但我們現在少有邏輯跨 frontend server 的程式需要 refactor）
    - 如果互相間有 dependencies，更動會需要很多重複作業和檢查（Given repos A , B, C, D all depend on X, if we update X due to a requirement of B, we may need to update A, C, D simultaneously）（但同樣的，這需求不限於 NodeJS sever，且我們現在不同 repo 的 NodeJS server 互相沒有依賴關係）

## 為什麼要把某些 source code 拆出去成獨立的 npm package？

- 案例：
  - twreporter-react - 我們把 react components 和 redux 都拆到其他的 packages
  - narwhale - 大部分用到的 code 都寫在 frontend server 所在的這個 repo 上
  - membership-fe - 大部分用到的 code 都寫在 frontend server 所在的這個 repo 上
  - plate - 大部分寫在 frontend server 所在的這個 repo 上 （計畫 deprecate 這個 server）

- 要把某些 source code 拆出去成獨立的 npm package，主要是**因為需要跨 server「重用」（reuse）某些部分的 code**

- 各 server 目前會使用到的 packages：

  

  |                                | twreporter-react | membership-fe | 多媒體 | plate | narwhale |
  | ------------------------------ | ---------------- | ------------- | ------ | ----- | -------- |
  |                                | 前台             | 前台          | 前台   | 後台  | 後台     |
  | @twreporter/redux              | O                | O             | O      |       |          |
  | @twreporter/universal-header   | O                | O             | O      |       |          |
  | @twreporter/article-components | O                |               | O      | O     | O        |
  | ＠twreporter/react-components  | O                | O             | O      |       |          |
  | bookmark widget                | O                | O             | O      |       |          |

## 多個 packages？一個 package？多個 repos？一個 repo？

### multi-packages vs. single-package 的優缺點比較

從 Consumer 的角度來看：

|                              | multi-packages                                               | single-package                                               |
| ---------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 安裝時會不會下載沒用到的部分 | 看怎麼切、切多細                                             | 一定會                                                       |
| consumer bundle size         | 如果不同套件有共用的部分沒有抽出來，會有重複的 code 在不同 packages，增加了 bundle size | **只要不是用整包 import，那應該是 consumer bundle size 會最小** |

從套件開發的角度來看：

|                         | multi-packages                                               | single-package                                               |
| ----------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| dependencies 的版本管理 | 好處是**不同 module 是各自有單獨的 dependencies 清單，彈性很高**。壞處是如果要不同 modules dependencies 全部一致時，必須手動管理。 | 不同的 module 是用同一份 package.json，因此**一定是用同樣的 dependencies**，但缺點就是假設我今天某一部份要單獨做一個不同的版本，不好切出來 |
| dependencies            | **無論在 dev 或 prod，都只需要安裝使用到的 dependencies。** 但缺點是在 dev 時，如果同時開發多個 package，可能會有重複的 dependencies 被安裝在每個 repo，需要使用 monorepo 的管理工具解決。 | 要開發一個 part 卻要安裝全部的 dependencies。但在 dev 時也，就算要開發不同 part ，也只會安裝一份。 |
| development & refactor  | 跨套件開發時需要  `yarn link` 數個 packages，如果 packages 之間互相有依賴會很麻煩，需要用 monorepo 解決。 | **只需要 `yarn link` 一個 package**                          |
| testing                 | **只要跑開發到的套件的測試，時間較短**。但跨套件測試較麻煩，需要透過 monorepo 解決。 | 任何部分的改動都可能要跑全部測試。但**跨越不同部分的測試較容易**。 |
| 套件區分                | 切分時需要思考要怎麼分套件，而且之後若要調動套件區分成本較高 | 需要思考套件內的資料夾結構和 build 出來給外部使用的 interface |

### 小結：

#### 確定該切出獨立 package 的 modules（切獨立 package 明顯利大於弊）

- 完全符合以下條件的 module，適合切出獨立的 package：
  - 使用情境明確，沒有多餘的部分，才不會下載安裝沒用的部分
  - consumer 使用的 interface 已經確定不太會改
  - 很少和其他套件有重複的 code
  - 不太需要跟其他 module 一起測試
- 或是 module dependencies 中有需要在 npm install hook build binary 的套件，應該要獨立切出來，減少不必要的 npm install 時間（ex: `node-sass`）
- 或是 module 有可能會獨立擁有自己特殊的 dependencies 時，應該要獨立切出來。（例如多媒體可能需要客製化的 dependency 版本）
- 適合作成獨立 packages 的例子：
  - @twreporter/redux：consumer 只使用 `createStore()` 創造 instance 來使用
  - @twreporter/react-components/cinemagraph：
  - @twreporter/react-components/fullpage-slide

#### 其他不確定是否該切出獨立 package 的 modules（有利有弊需權衡）

包括 @twreporter/react-components、@twreporter/article-components、bookmark widget…等 modules，有兩種方案：

##### multi-packages、mono-repo

- 使用法 @twreporter/react-components, @twreporter/universal-header,  @twreporter/core/utils/media-query, @twreporter/redux….
- 優點：不會浪費時間跑多餘的測試；consumer 只需要安裝要用的部分；可以獨立開發特殊版本的部分（ex: `header` 想使用跟別人不同版本的 react，不需要把其他 modules 也升版）
- 缺點：consumer 如果安裝多個不同套件有可能有 duplicate code 造成 bundle size 變大；如果手動作業變得繁雜，可能需要多一個管理工具（如 `lerna`）；package 切分完成後不太能改動
- 範例：https://github.com/braxtonchristensen/react-monorepo

預想的 repo structure：

```
twreporter-npm-packages/
  package.json
  packages/
    core/
      package.json
      lib/
      src/
        main.js
        constants/
          typography.js
        utils/
          media-query.js
    react-header/
      package.json
      lib/
      src/
        main.js
    react-footer/
      package.json
      lib/
      src/
        main.js
    react-article/
      package.json
      lib/
      src/
        main.js
    react-bookmark-widget/
      package.json
      lib/
      src/
        main.js
    react-components/
      package.json
      lib/
      src/
        main.js
    redux/
      package.json
      src/
        main.js
        actions/
        reducers/
        store/
```



##### single-package、mono-repo

  - @twreporter/core/components/article, @twreporter/core/components/header, @twreporter-react/core/components/bookmark-widget, @twreporter/core/utils/media-query…

預想的 repo structure：

```
twreporter-npm-packages/
  package.json
  packages/
    core/
      package.json
      lib/
      src/
        main.js
        constants/
        typography.js
        utils/
          media-query.js
    react-components/
      package.json
      lib/
      src/
        main.js
        article/
        header/
        footer/
        bookmark-widget/
        bookmark-list/
        list/
        pagination/
    redux/
      package.json
      src/
        main.js
        actions/
        reducers/
        store/
```

