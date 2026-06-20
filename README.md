# スイング その場チェック（スマホ版） 配置手順

`swing_check.html` は **スマホ単独・オフライン** で動く即時EEチェックです（仕様書 Phase 3）。
MediaPipe(JS/wasm)とモデル(`vendor/`・`models/`)は同梱済みなので、初回ロード後の外部通信は不要です。

> ⚠ **`file://` で直接開くと動きません。** Android Chrome は ES モジュール＋ローカル wasm/model を
> `file://` から読めないため、必ず下記いずれかの方法で「配信」して開いてください。

---

## (a) すぐ試す：PCからLAN配信（最も簡単）

PCとスマホを**同じWi-Fi**に接続した状態で：

1. PCで（このフォルダに移動して）配信を開始
   ```
   cd C:\Users\n-pon\swing_analyzer\phone
   python -m http.server 8780
   ```
2. スマホのChromeで次を開く（IPはPCのLAN IP。例: 現在のPCは `192.168.1.10`）
   ```
   http://192.168.1.10:8780/swing_check.html
   ```
   ※PCのIPが変わったら読み替え。確認: PowerShellで `ipconfig`（IPv4 アドレス）。
3. 「動画を選択」で**1球分のスロー動画**を選ぶ → 「解析する」。

- 録画ファイルを選んで解析する流れは **http(LAN) で動作**します。
- その場撮影（カメラ起動＝getUserMedia）だけは https が必須なので、(a) では「録画→ファイル選択」を使ってください。
- PCを切るとアクセスできません（PC配信のため）。完全に持ち出す/オフライン常用は (b)。

---

## (b) スマホ単体・完全オフライン：PWA（インストール済み）

`manifest.webmanifest` と `sw.js` が追加済みなので、以下の手順でホーム画面アプリとして使えます。

**Android Chrome:**
1. GitHub Pages の URL（`https://xeroxuser365-droid.github.io/swing-check/swing_check.html`）を Chrome で開く。
2. Chrome メニュー（右上 ⋮）→「**ホーム画面に追加**」→ 追加。
3. 初回読み込みで Service Worker が HTML・`vendor/`・`models/` を全てキャッシュします。
4. 以降はホーム画面のアイコンから起動すれば **機内モード（オフライン）** でも動作します。

**iOS Safari:**
1. 同じ URL を Safari で開く。
2. 共有ボタン（□↑）→「**ホーム画面に追加**」→ 追加。
3. Service Worker のキャッシュにより、以降はオフラインで起動できます。

> 初回は必ず Wi-Fi 等でオンライン状態で開いてください（SW キャッシュ構築のため）。

---

## 使い方（共通）

- **設定**（⚙）: 身長(cm)・利き手・モデル(full=標準/lite=軽量)・間引き(stride 2/3)。
  URLパラメータでも指定可: `?height=172&hand=right&model=lite&stride=3`
- **撮影**: DTL（後方・飛球線の延長）から、**全身（足首〜頭）が入る縦画面**・三脚固定・スロー(120fps)。
  → 詳細は別途の撮影ガイド参照。
- 結果: 早期伸展(EE)の大表示＋判定、アドレス/トップ/インパクトのキーフレーム（骨格＋基準線＋骨盤）、
  前傾・テンポ・手の通り道・頭の動き。「もう1球」で繰り返し。

## 同梱物
```
phone/
├── swing_check.html              # 本体（単一HTML）
├── manifest.webmanifest          # PWA マニフェスト
├── sw.js                         # Service Worker（オフラインキャッシュ）
├── icon-192.png                  # アプリアイコン 192×192
├── icon-512.png                  # アプリアイコン 512×512
├── vendor/vision_bundle.js       # MediaPipe Tasks Vision (JS)
├── vendor/wasm/*                 # MediaPipe wasm バイナリ
└── models/pose_landmarker_{full,lite}.task   # 骨格モデル
```
精度・履歴・IMU・複数球の自動切り出しは PC版（`analyze.py`）の担当です。
