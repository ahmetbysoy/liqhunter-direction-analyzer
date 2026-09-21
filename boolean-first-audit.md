# LiqHunter Boolean-First Audit

## 1. Tarama kapsamı

İncelenen dosyalar:

- `uploads/fe.html`
- `outputs/liqhunter-pwa.html`
- `outputs/manifest.webmanifest`
- `outputs/sw.js`
- `outputs/icon.svg`
- `outputs/liqhunter-plan.html`

`fe.html` temiz bir kaynak dosyası değildir. Arena sohbetinden kaydedilmiş yaklaşık 3.7 MB boyutunda bir MHTML/snapshot içinde aynı Python/Flask uygulamasının birden fazla sürümü, HTML kod blokları ve özetlenmiş bölümler birlikte bulunuyor.

## 2. Mevcut özellikler

### Kaynak taslağındaki özellikler

- Binance Futures REST ve WebSocket veri akışı
- Kline, aggTrade ve forceOrder takibi
- EMA, RSI ve ATR hesapları
- Alıcı/satıcı işlem akışı ve delta
- Likidasyon hacmi ve cascade analizi
- İki yönlü piramit sınıflandırması
- 5 saniyelik mikro mum scalping analizi
- Dinamik strateji skoru ve cooldown
- SQLite sinyal ve likidasyon kayıtları
- Strateji win-rate hesaplama
- Flask `/api/state`, `/api/switch` ve `/api/coins` uçları
- Mobil 4 sekmeli UI, sesli uyarı ve 1.5 saniyelik polling

### Mevcut PWA prototipindeki özellikler

- Keşfet, Piyasa, Tahminler ve Bot ekranları
- Alt navigasyon ve yön ufku sekmeleri
- BTC, ETH ve SOL demo varlıkları
- Yukarı/aşağı/nötr yön kartları
- Güven skoru, kanıtlar, RSI, ATR ve akış özeti
- Bot sohbeti ve hızlı soru butonları
- PWA manifest, service worker ve ikon
- Binance REST/WebSocket canlı veri denemesi
- CORS veya ağ problemi için demo veriye geri dönüş

## 3. Mevcut akış

1. Sayfa statik HTML ile açılıyor.
2. `DATA` içindeki demo varlıklar render ediliyor.
3. Canlı veri için REST ticker/klines ve iki WebSocket bağlantısı deneniyor.
4. Veriler `LIVE` nesnesine yazılıyor.
5. `renderAll()` doğrudan DOM'u güncelliyor.
6. Kullanıcı varlık, sekme, navigasyon veya bot etkileşimi yaptığında global değişkenler değişiyor.
7. Canlı bağlantı başarısız olursa `Demo veri` etiketi gösteriliyor.

## 4. Çakışan state'ler

`fe.html` içinde aynı kavramlar farklı isim ve tiplerle tutuluyor:

- `APP_STATE` ve `app_state`
- `APP_STATE["symbol"]` ve `current_symbol`
- `BUY`/`SELL` ve `LONG`/`SHORT`
- `AL`/`SAT`/`HAZIRLAN`/`BEKLE` ve `up`/`down`/`flat`
- `status=OPEN/CLOSED` ve `result=WIN/LOSS/PENDING`
- `pyramid_data` ve ikinci piramit uygulaması
- Birden fazla `get_scalping_state`, `execute_signal` ve WebSocket başlatma akışı
- Tam implementasyon iddiası ile `pass` içeren özet implementasyonlar

Bu çakışmalar doğrudan taşınırsa Boolean-first store güvenilir olmaz.

## 5. Boolean dönüşüm haritası

### UI

- `isDiscoverVisible`, `isMarketVisible`, `isForecastsVisible`, `isBotVisible`
- `isModalOpen`, `isSidebarOpen`, `isPanelVisible`, `isMenuOpen`
- `isTooltipVisible`, `isDialogOpen`, `isDragging`, `isResizing`
- `isFocused`, `isHovered`, `isSelected`

### Veri

- `hasData`, `hasError`, `hasUpdates`, `hasSignal`
- `hasPosition=false`, `hasOrders=false` ürün sınırı olarak sabit false
- `hasLiquidity`, `hasTrades`, `hasSnapshot`, `hasResponse`

### Ağ

- `isConnected`, `isConnecting`, `isReconnecting`
- `isSubscribed`, `isAuthenticated=false`, `isAuthorized=false`
- `isFetching`, `isStreaming`, `isOnline`, `isOffline`
- `isRateLimited`

### Motor

- `isRunning`, `isStopped`, `isPaused`, `isProcessing`
- `isCalculating`, `isFiltering`, `isScanning`, `isExecuting=false`
- `isValid`, `isReady`

### Strateji ve risk

- `isBullish`, `isBearish`, `isLongSignal=false`, `isShortSignal=false`
- `isBreakout`, `isReversal`, `isTrend`, `isRange`, `isMomentum`, `isVolatile`
- `isSafe`, `isRisky`, `isLiquidationRisk`, `isOverLeveraged=false`
- `isOverBought`, `isOverSold`

## 6. String state değerlendirmesi

Boolean'a dönüştürülecek string durumlar:

- `live/demo` bağlantı etiketi
- `up/down/flat` yön durumu
- `AL/SAT/BEKLE` benzeri işlem çağrışımlı sinyaller
- `OPEN/CLOSED/PENDING` işlem yaşam döngüsü
- `loading=yes/no` türü durumlar

Boolean olarak tutulmaması gereken domain değerleri:

- Seçili coin sembolü: `BTC`, `ETH`, `SOL`
- Zaman ufku: `15m`, `1h`, `4h`, `1d`
- Kullanıcıya gösterilecek metinler
- API olay türleri ve dış veri sembolleri

Bu değerler sabitler/enums olarak tutulacak; kontrol kararları onlardan türetilen boolean'larla yapılacak.

## 7. Sayısal state değerlendirmesi

Korunması gereken gerçek ölçümler:

- Fiyat, yüzde değişim, RSI, ATR, EMA
- Güven skoru, yön skoru ve hacim oranı
- Alıcı/satıcı yüzdesi
- Zaman damgaları ve veri dizileri

Magic number olarak ayrıştırılması gerekenler:

- EMA/RSI/ATR periyotları
- Yön eşikleri ve güven skoru katsayıları
- Hacim ve volatilite eşikleri
- WebSocket yeniden bağlanma ve REST yenileme süreleri

Bunlar `constants.js` veya strateji konfigürasyonu içinde isimlendirilmiş sabitler olacak.

## 8. Mimari sorunlar

- UI, state, API, WebSocket ve analiz tek HTML içindeki tek scriptte birleşmiş.
- DOM doğrudan event handler'larda güncelleniyor.
- REST ve WebSocket yaşam döngüsü merkezi değil.
- Demo ve canlı state aynı veri şekliyle ayrıştırılmıyor.
- Service worker cache-first çalışıyor; güncelleme ve offline hata durumu yok.
- Tahmin geçmişi demo değerlerden oluşuyor, kalıcı kayıt katmanı yok.
- CORS problemi için backend veri köprüsü bulunmuyor.
- Ürün işlem yapmamasına rağmen kaynak taslağında pozisyon, TP/SL ve emir mantığı var.

## 9. Önerilen yeniden yazım modülleri

```text
outputs/liqhunter-v2/
  index.html
  manifest.webmanifest
  sw.js
  styles.css
  src/
    main.js
    state/store.js
    state/initial-state.js
    core/constants.js
    data/api-client.js
    data/websocket-client.js
    data/market-adapter.js
    analysis/indicators.js
    analysis/strategy-engine.js
    analysis/risk-engine.js
    analysis/prediction-engine.js
    ui/event-manager.js
    ui/render-engine.js
```

Başlatma zinciri:

```text
main() -> init() -> createStore() -> initApi() -> initWebSocket()
       -> initStrategyEngine() -> initRiskEngine() -> bindEvents()
       -> render()
```

## 10. Refactor aşamaları

1. State sözleşmesi, boolean store ve sabitler.
2. API ve WebSocket adapter'ları.
3. İndikatör ve yön tahmin motoru.
4. Risk/belirsizlik motoru.
5. Render ve event modülleri.
6. PWA cache/update/offline davranışı.
7. Mock, canlı ve hata senaryosu testleri.

## 11. İlk onay gerektiren mimari karar

Boolean-first uygulanacak; ancak coin, zaman ufku, API olay tipi ve kullanıcıya gösterilen metinler boolean yapılmayacak. Bunlar domain değerleri olarak kalacak, kontrol durumları boolean alanlardan türetilecek.

Örnek:

```js
const selectedSymbol = 'BTC';
const isBullish = score > SCORE_LIMIT;
const isBearish = score < -SCORE_LIMIT;
const isNeutral = !isBullish && !isBearish;
```

Bu sınır onaylanmadan yeniden kodlamaya geçilmeyecek.
