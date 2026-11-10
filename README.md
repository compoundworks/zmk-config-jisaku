# Jisaku v3.9 の ZMK 設定（2026-11-10 Claude が書いた最初の版）

自作キーボード v3.9（左右分割・無線・XIAO nRF52840 Plus）のファームの元。右手が親（Mac とつながる・USB で ZMK Studio）、左手が子。

## 何がどこにあるか

| ファイル | 中身 |
|---|---|
| `config/west.yml` | ZMK v0.3（正式）＋部品3つ（玉のセンサー PMW3610・スティック・トラックパッド IQS525）。badjeff の2つは MeKaBu（te9no/zmk-config-MKB2）が使っている版にそろえた |
| `config/jisaku.keymap` | キーマップ（8層）。Keyball39 の4月のキーマップと Remap の大西配列から写した |
| `boards/shields/jisaku/jisaku.dtsi` | 左右共通＝キーの並び（62）・行の足・ローラーと横回しホイール・ZMK Studio に出す絵の位置 |
| `boards/shields/jisaku/jisaku_left.overlay` `.conf` | 左＝列の足・スティック（ADC）・ローラーとホイールを動かす |
| `boards/shields/jisaku/jisaku_right.overlay` `.conf` | 右＝列の足・玉のセンサー（SPI）・トラックパッド（I2C）・ZMK Studio |
| `build.yaml` `.github/workflows/build.yml` | GitHub で組み立てる時の一覧（右・左・設定の初期化の3つ） |

足の割り当ての正は `../ergogen/v3_9/routed/{L,R}.kicad_pcb`（線を引いた基板）。`jisaku.dtsi` の先頭に XIAO の D 番号と nRF の足の表を書いた。

## キーの並び（keymap の bindings の順）

```
1行目  左 c1..c7          | 右 c1..c5
2行目  左 c1..c7          | 右 c1..c6
3行目  左 c1..c7          | 右 c0..c6
4行目  左 c1 c2 c3 c4 c6 c7 c8 | 右 親指 t1 t2 t3・c4 c5 c6
5行目  左の5方向キー 5つ    | 右の5方向キー 5つ
```

- 左 c2..c6・右 c1..c5 の3行が Keyball39 の 5×3。左の4行目の c2 c3 c4 c6 c7 c8 が Keyball39 の左の親指の6つ、右の t1 t2 t3 が右の親指の3つ
- 左 c7_r2 はキーかローラー CKW12 の押し込み（同じ升）
- 空いている升（左 r5c1 r5c2・右 r1c0 r1c6 r2c0 r4c3 r5c5 r5c6）は今は並びに入れていない。何か足した時に入れる

## 層

| 番号 | 名前 | 開き方 | 元 |
|---|---|---|---|
| 0 | 基本（大西配列） | — | Remap の1枚目 |
| 1 | 数字・記号 | Esc・X・Enter を長押し | Keyball [1] |
| 2 | 矢印・移動 | かな（右親指の内側）を長押し | Keyball [2] |
| 3 | 記号と窓 | ; を長押し | Keyball [3] |
| 4 | アプリと曲 | Space を長押し | Keyball [4] |
| 5 | F キーと Bluetooth | Tab を長押し | Keyball [5]＋右に Bluetooth の切り替え・書き込み（bootloader） |
| 6 | スクロール | M を長押し | Keyball の6（玉がスクロール） |
| 7 | マウス | 玉を動かすと 0.7 秒 | Keyball [7]（J K L の所にクリック） |

## 仮に置いた物（届いてから ZMK Studio で直す）

- 新しく増えたキー: 左 c1 列（Q・Tab・Shift・Cmd）・左 c7 列（.・ローラーの押し込み＝中クリック・Delete）・右 c0_r3（Esc）・右 c6 列（BS・Enter）・右の4行目の3つ（左クリック・中クリック・右クリック）
- 5方向キーの向き: FW1＝上・FW2＝右・FW3＝下・FW4＝左・FW5＝押し込み と置いた。データシートの足の名前（F1〜F5）しか分かっていない。左＝デスクトップの移動（Ctrl＋矢印）と Alfred、右＝矢印と Enter
- 長押しの層の付き方: Keyball の memory では H と - も長押しの層を持つとあるが、どの層かが分からないので普通のキーにした
- 玉の速さ（cpi 800）・向き・スティックの真ん中の値（1600mV）・ローラーとホイールの1回の量は、MeKaBu と roBa の値のまま

## スティックの電気（10/14 の決まりと違う所）

10/14 に「スティックは使う時だけ電気を流す（BullyMulligan/zmk-driver-analog-stick・足1本で入切）」と決めた。しかし 10/16 に左の XIAO を寄せた時、入切に取っておいた D13 を4列目に回し、注文した基板ではスティックの V は 3V3 に直につながっている（左 W2 の 2番）。だから第1版では入切できない。今は MeKaBu と同じ badjeff の読み方（電気は流しっぱなし）で書いた。

- 流しっぱなしの量: JT8P の抵抗 10kΩ×2 に 3.3V で約 0.66mA。左の電池 60mAh だと、スティックだけで約 90 時間（4日弱）で空になる見込み（計算だけ・実測していない）
- 第1版で入切したくなったら: スティックの V の線を W2 ではなく W3 の「D6」（拡張の穴）へつなぎ、BullyMulligan の部品に替える。ただしこの部品は ZMK の新しい版（main）向けで、v0.3 で組み立てられるかは確かめていない
- 第2版の基板では、入切の足をはじめから引く

## 持ち越さなかった物

- メールアドレス2つ・学籍番号・英数に切り替えて数字4桁と Enter を打つマクロの4つ（ファームの中に平文で残るので）。要るなら ZMK Studio ではなくこのファイルにマクロで足す
- Keyball の同時押し D F（やり直し）。F D（削除）と同じ2つのキーなので、Keyball でも削除しか出ていなかった

## 組み立て方（2026-11-10 20時 GitHub で3つとも通った・右 FLASH 34%／左 22%）

GitHub に置くと、`.github/workflows/build.yml` が `build.yaml` の3つを組み立てて `.uf2` を作る。書き込みは XIAO のリセットを2回押して出るドライブに `.uf2` を置く（右＝`jisaku_right`、左＝`jisaku_left`。うまくいかない時は両方に `settings_reset` を入れてから入れ直す）。

## ZMK Studio

右手を USB で Mac につなぎ、https://zmk.studio をブラウザ（Chrome 系）で開く → 「Jisaku39」を選ぶ → キーを押して割り当てを変える → 保存。鍵は掛けていない（`CONFIG_ZMK_STUDIO_LOCKING=n`）。ローラー・スティックの速さ・同時押しは ZMK Studio では変えられないので、このファイルを直す。
