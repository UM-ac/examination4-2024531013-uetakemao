# 室温測定用センサと通信方式の調査ログ

- 調査日：2026年10月22日
- 使用基板：UIAPduino Pro Micro CH32V003 V1.4
- 対象：室内の空気の温度。

## 1. 調査した内容

UIAPduinoに3種類の温度センサを接続し、測定値を比較するため、以下を調査した。

- センサとの通信方式であるI²C・SPI・UARTの違い
- UIAPduinoの端子と電源電圧
- 国内で購入できるI²C対応の温度センサ

## 2. 分かったこととその根拠

### 通信方式

I²Cでは、複数のセンサを共通の2本の通信線につなげられる。

- **SDA：** 温度などのデータをやり取りする線
- **SCL：** 通信のタイミングを合わせる線

UIAPduinoは、センサごとの番号（アドレス）を指定して相手を選ぶ。そのため、番号が重ならなければ、3つのセンサを同じ通信線に接続できる。電源とGNDの配線は別に必要。

SPIでも複数接続は可能だが、通常はセンサごとに選択用の線が必要になる。UARTは基本的に1対1の通信であり、3台接続には追加の工夫が必要になる。

根拠：[NXP I²C仕様書](https://www.nxp.com/docs/en/user-guide/UM10204.pdf)、[Analog Devices SPI解説](https://www.analog.com/en/resources/analog-dialogue/articles/introduction-to-spi-interface.html)

### UIAPduinoの仕様

公式資料で、I²Cの端子はSDA＝D3、SCL＝D4と確認した。また、基板の動作電圧は3.3 V／5 Vに変更でき、初期設定は5 Vである。

根拠：[UIAPduino公式製品ページ](https://www.uiap.jp/uiapduino/pro-micro/ch32v003/v1dot4)、[V1.4回路図](https://www.uiap.jp/doc/UIAPduino-Pro-Micro-CH32V003-V1dot4-sch.pdf)

### 国内で購入できるセンサ候補

| センサ | 購入先 | 参考価格（税込） | 使用予定のアドレス |
|---|---|---:|---|
| ADT7410搭載基板（AE-ADT7410） | [秋月電子](https://akizukidenshi.com/catalog/g/g106675/) | 600円 | 0x48 |
| SHT31搭載基板（AE-SHT31） | [秋月電子](https://akizukidenshi.com/catalog/g/g112125/) | 950円 | 0x45 |
| MCP9808搭載基板（Adafruit #1782） | [スイッチサイエンス](https://www.switch-science.com/products/1708) | 1,210円 | 0x18 |

3候補ともI²C対応で、3.3 Vで使用できる仕様を持つ。アドレスも重ならない。調査時点で販売ページに在庫表示があったが、購入時に再確認する。

根拠：上記販売ページ、[ADT7410基板マニュアル](https://akizukidenshi.com/goodsaffix/AE-ADT7410_aw.pdf)、[SHT31基板マニュアル](https://akizukidenshi.com/goodsaffix/AE-SHT3x_manu_v1.0.pdf)、[MCP9808メーカー資料](https://ww1.microchip.com/downloads/en/DeviceDoc/MCP9808-0.5C-Maximum-Accuracy-Digital-Temperature-Sensor-Data-Sheet-DS20005095B.pdf)

## 3. 現時点での暫定結論

**センサとの通信方式はI²Cを暫定採用する。**

3種類のセンサで通信線を共有でき、配線と使用端子を少なくできるためである。

センサは**ADT7410・SHT31・MCP9808**を暫定候補とする。同じ室内環境で測り、以下を比較する。

- センサ同士の測定値の違い
- 温度が落ち着いているときの測定値のばらつき

ただし、基準となる温度計がなければ、どのセンサが本当の温度に近いかは判断できない。

将来的には、測定した温度に応じてLED表示を変え、「測定→状態の判断→表示」という状態監視の基本を学ぶ。

## 4. まだ分からないこと

- 研究室に比較の基準として使える温度計があるか
- UIAPduinoで各センサを読み取るプログラムが動作するか
- 3種類を接続した状態で安定して通信できるか
- インターフェース接続に必要な抵抗の組み合わせ
- センサを置く位置、測定間隔、測定時間をどうするか
- 必要な温度精度と、LEDを切り替える温度条件
- 実機の電圧設定、購入時の在庫・納期
