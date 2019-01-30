# 地圖開發

- [地圖開發](#地圖開發)
  - [JavaScript 互動地圖套件](#javascript-互動地圖套件)
    - [Leaflet](#leaflet)
    - [Mapbox.js](#mapboxjs)
    - [Mapbox GL JS](#mapbox-gl-js)
  - [地圖底圖哪裡找](#地圖底圖哪裡找)
    - [Tilesets Provider Services](#tilesets-provider-services)
    - [Host OpenMapTiles by Yourself](#host-openmaptiles-by-yourself)
  - [GeoJSON](#geojson)

## JavaScript 互動地圖套件

### Leaflet

[Leaflet](https://leafletjs.com/index.html) 是一套開源的互動地圖 JavaScript library，它有合理的大小（38kb，未壓縮），廣泛的瀏覽器支援（包括 IE 7–11），已設計調校好的基本地圖 UI 元件（地圖控制、Markers、Popups、互動動畫）。它可以介接不同地圖底圖的 API（主要支援[點陣圖磚](https://docs.mapbox.com/help/glossary/tileset#raster-tilesets)的底圖）。

要快速瀏覽這個 library 可以做到什麼，可以看：

- [官網 features](https://leafletjs.com/index.html#features)
- [官網 tutorials (examples)](https://leafletjs.com/examples.html)
- [官網 plugins](https://leafletjs.com/plugins.html)

### Mapbox.js

[Mapbox.js](https://docs.mapbox.com/mapbox.js/) 是一套以 Leaflet 為基礎的開源 library，它和 Leaflet 的主要差別在於，它的地圖底圖僅支援 [TileJSON](https://docs.mapbox.com/help/glossary/tilejson/) 或是使用 Mapbox 的[底圖 API](https://docs.mapbox.com/mapbox.js/api/v3.1.1/l-mapbox-map/) ，以及它的 UI 元件是使用 Mapbox 所設計的樣式。

要快速瀏覽這個 library 可以做到什麼，可以看：

- [官網 examples](https://docs.mapbox.com/mapbox.js/example/v1.0.0/)

### Mapbox GL JS

[Mapbox GL JS](https://www.mapbox.com/mapbox-gl-js/) 也是一個開源互動地圖 library。它和前兩者的差別在於，它是使用 WebGL 來 render 地圖和地圖上的圖層，因此對於使用[向量圖磚（vector tilesets）](https://docs.mapbox.com/help/glossary/tileset#vector-tilesets)類型的地圖底圖有較好的支援，並且有較好的 2D、3D 動態效果。缺點是 [IE 和 Edge 瀏覽器對 WebGL 支援度較差](https://docs.mapbox.com/help/troubleshooting/mapbox-browser-support/#mapbox-gl-js)，開發者必須手動提供使用者 fallback solution。

另外一個要注意的是，它只能使用符合 [Mapbox Style Specification](https://docs.mapbox.com/help/glossary/style) 的地圖底圖。

要快速瀏覽這個 library 可以做到什麼，可以看：

- [官網 examples](https://docs.mapbox.com/mapbox-gl-js/examples/)

## 地圖底圖哪裡找

### Tilesets Provider Services

用來搭配 [Leaflet](#leaflet) 和 [Mapbox.js](#mapboxjs) 使用的免費點陣圖磚底圖，已經有人整理在 [Leaflet-providers](https://github.com/leaflet-extras/leaflet-providers) 這個 plugin 裡面。但其實使用這些地圖不一定要裝 plugin，它的 [preview](http://leaflet-extras.github.io/leaflet-providers/preview/index.html) 裡面有提供使用 pure JavaScript 的 url temmplate。

而符合 [Mapbox Style Specification](https://docs.mapbox.com/help/glossary/style) 的地圖底圖有以下兩家服務可以使用，都是有少量免費 quota，超過就要加錢：

- [Mapbox](https://www.mapbox.com/)：[非商業使用](https://docs.mapbox.com/help/account-faq/#which-plan-does-mapbox-require-for-commercial-applications)每個月有 5 萬免費 [map views](https://docs.mapbox.com/help/glossary/map-view/) 。網站有提供線上介面可以編輯地圖內容。

- [MapTiler Cloud](https://www.maptiler.com/cloud/)：只要符合官網的[使用條款](https://www.maptiler.com/terms/)，每個月 10 萬 requests 以內免費。（request 計算方式見 [FAQ](http://www.maptiler.com/cloud/plans/)）網站有提供線上介面可以編輯地圖內容。

### Host OpenMapTiles by Yourself

[OpenMapTiles](https://openmaptiles.org/about/) 計畫已經把 OpenStreetMap 圖資整理成 vector tiles ，開放給大家建置自己的地圖。但他的網站規劃不太好懂，不容易找到到底該如何使用。從 [openmaptiles.com](https://openmaptiles.com/) 和 [OpenMapTiles Documentation](https://openmaptiles.org/docs/) 兩個頁面可以找到，有三種軟體可以用來將 OpenMapTiles 資料架設成 map server，包括：

- [OpenMapTiles Map Server](https://openmaptiles.com/server/)：免費
- [TileServer GL](https://github.com/klokantech/tileserver-gl)：免費
- [MapTiler Desktop](https://www.maptiler.com/desktop/)：免費版有使用限制

而在授權方面，Open Street Map (OSM) 底圖，[如果符合官網使用條款上列出的三種條件之一，是可以免費使用的](https://openmaptiles.com/terms/)。而如果是要衛星圖和地形陰影檔案，則有一次全包的 package，或隨選付費這兩種選擇，在進入選擇地圖的畫面後可以看到價格。

## GeoJSON

[GeoJSON](https://docs.mapbox.com/help/glossary/geojson/) 是目前在網頁使用上較為通用的一個地理資訊資料格式，它是以 JSON 檔案格式為基礎，除了座標資料外，也可以儲存自訂的 properties。關於 GeoJSON 的格式說明，建議可以直接看 [SPEC](http://geojson.org/)。

而在操作和計算地理資訊資料方面，有一個好用的 library，[Turf.js](http://turfjs.org/)。可以把它看成是地理資訊資料的 lodash（工具箱）。

