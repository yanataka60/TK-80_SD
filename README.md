# NEC TK-80にSD-Cardとのロード、セーブ機能

![TK-80_SD](https://github.com/yanataka60/TK-80_SD/blob/main/JPEG/TITLE.jpg)

　ARDUINO+SD-CARDをTK-80に接続することでNEC TK-80でSD-CARDとロード、セーブを実現するものです。

　実現するためにTK-80 MONITOR へのパッチ当て、SDアクセスルーチンを合わせてROMに焼く必要があります。

　TK-80BSと共用できるEXT-BOARDを使います。

　ROMは27512、27256、2764、2732のどれでも使えるので安く入手できるものを使います。

　パッチを当てたTK-80 MONITOR及びSDアクセスルーチンを含めて焼きます。元のROM D454(D464)×3個はすべて引き抜きます。

　RAMは、8000h～81FFhの増設RAMを増設していない場合はSRAM 62256が使えますが、2101(5101)×4個と62256×1個のどちらか安い方を選択すれば良いです。

　また、SRAM 62256を選択した場合には4000h～7FFFhまでをRAMとすることもできますが、どう使うかはあなた次第です。

　TK-80標準装備のRAM D5101-E×4個(8200h～83FFh)は7SegLEDをDMA表示する為に必要ですので引き抜いてはいけません。(SRAM 62256では代わりになりません。)

　TK-80_SDとしてはROMのうち0000h～04FFまでを使います。27512、27256、2764、2732における未使用領域は自由に使って構いません。

　27512、27256:0500h～3FFFh   2764:0500h～1FFFh   2732:0500h～0FFFh

　

## 回路図
　KiCadフォルダ内のTK-80_EXT-BOARD.pdfを参照してください。

[回路図](https://github.com/yanataka60/TK-80_SD/blob/main/KiCad/TK-80_EXT-BOARD.pdf)

![TK-80_SD](https://github.com/yanataka60/TK-80_SD/blob/main/KiCad/TK-80_EXT-BOARD_1.jpg)

|番号|品名|数量|備考|
| ------------ | ------------ | ------------ | ------------ |
||J1、J2のいずれか|||
|J1|Micro_SD_Card_Kit|1|秋月電子通商 AE-microSD-LLCNV (注1)|
|J2|MicroSD Card Adapter|1|Arduino等に使われる5V電源に対応したもの(注2)|
|U1|8080A|1|TK-80から引き抜いたもの|
|U3|8228|1|TK-80から引き抜いたもの|
|U8|8255|1|TK-80から引き抜いたもの|
|U5|GAL22V10|1||
|U6|ROM|1|27512、27256、2764、2732のいずれか|
|U10|Arduino_Pro_Mini_5V|1|Atmega328版を使用 168版は不可。|
|U2、U4、U9|Pin Header|40Pin×4、14Pin×2|秋月電子通商 細ピンヘッダーPHA-1x40SG又は基板用リードフレームBQ04-SN(注3)|
|C1-C3|積層セラミックコンデンサ 0.1uF|3||
|S1-S3|ピンヘッダ3Pin分|3|秋月電子通商 ピンヘッダーPH-1x40SGなど|
|LS1|小型スピーカー|1|秋月電子通商 マイクロスピーカー|
||ピンソケット(任意)|26Pin分|Arduino_Pro_Miniを取り外し可能としたい場合に調達します 秋月電子通商 FHU-1x42SGなど|

　　　注1)秋月電子通商　AE-microSD-LLCNVのJ1ジャンパはショートしてください。

　　　注2)MicroSD Card Adapterを使う場合

　　　　　J2に取り付けます。

MicroSD Card Adapterについているピンヘッダを除去してハンダ付けするのが一番確実ですが、J2の穴にMicroSD Card Adapterをぴったりと押しつけ、裏から多めにハンダを流し込むことでハンダ付けをする方法もあります。なお、この方法の時にはしっかりハンダ付けが出来たかテスターで導通を確認しておいた方が安心です。

ハンダ付けに自信のない方はJ1の秋月電子通商　AE-microSD-LLCNVをお使いください。AE-microSD-LLCNVならパワーLED、アクセスLEDが付いています。

![MicroSD Card Adapter1](https://github.com/yanataka60/TK-80_SD/blob/main/JPEG/MicroSD%20Card%20Adapter.jpg)

　　　注3)細ピンヘッダーと基板用リードフレームの選択
![Pin](https://github.com/yanataka60/TK-80_SD/blob/main/JPEG/PIN.jpg)

どちらでも大丈夫ですが、あえて言うなら

〇細ピンヘッダー

　ピンが固く曲がってしまうことは無いので嵌めるときに位置合わせだけ気を付ければ大丈夫。

　細ピンとはいえピンに厚みがあるので嵌めるのに結構力が必要です。ちゃんと嵌っていないと外れやすいです。

　リードフレームより外し易いです。

〇リードフレーム

　ピンがすぐ曲がってしまうため、嵌めるときに気を付けないとピンが折れ曲がってしまう危険があります。

　嵌ってしまうとかなり外し難いです。無理に外そうとするとピンが曲がってしまうため慎重に外す必要があります。

　外れ難いため、使っている分には安心感があります。

## ROMの書込み

　MONITOR-ROMの内容を読み出し、バイナリエディタ等で以下の修正をします。

|ADDRESS|修正前|修正後|
| ------------ | ------------ | ------------ |
|003C|FF|F0|
|0052|FF|F0|
|0080|D5 00|00 03|
|0082|07 01|03 03|
|024B|EF|E0|
|0258|DF|D0|
|0265|BF|B0|

　次にMONITOR-ROMの後ろ0300hからに8080フォルダ中のfile_trans_TK80.binを連結します。

　0000h～04CEh(1231Byte)までのファイルになります。

　ROMに焼き、EXT-BOARDに装着します。

## Arduinoプログラム
　Arduino IDEを使ってArduinoフォルダのTK-80_SDフォルダ内TK-80_SD.inoを書き込みます。

　SdFatライブラリを使用しているのでArduino IDEメニューのライブラリの管理からライブラリマネージャを立ち上げて「SdFat」をインストールしてください。

　「SdFat」で検索すれば見つかります。「SdFat」と「SdFat - Adafruit Fork」が見つかりますが「SdFat」のほうを使っています。

注)Arduinoを基板に直付けしている場合、Arduinoプログラムを書き込むときは、EXT-BOARDをTK-80本体から外し、GAL22V10を外したうえで書き込んでください。

## 操作方法
　異常が無いと思われるのにエラーとなってしまう場合にはSD-CardアダプタのArduinoとTK-85の両方をリセットしてからやり直してみてください。

### Save
　MODEキー、SAVEキーを押してからファイルNo(xxxx)を4桁で入力してWR/ENTキーを押します。

　正常にSaveが完了するとアドレス部にスタートアドレス、データ部にエンドアドレスが表示されます。

　　　8000H～8390Hまでをxxxx.BTKとしてセーブします。セーブ範囲は固定となっていて指定はできません。

　「FFFFFFFF」と表示された場合はSD-Card未挿入です。確認してください。

### Load
　MODEキー、LOADキーを押してからファイルNo(xxxx)を4桁で入力してWR/ENTキーを押します。

　　　xxxx.BTKをBTKヘッダ情報で示されたアドレスにロードします。ただし、8391H～83FFHまでの範囲はライトプロテクトされます。

　正常にLoadが完了するとアドレス部にスタートアドレス、データ部にエンドアドレスが表示されます。スタートアドレスが実行開始アドレスであればそのままRUNキーを押すことでプログラムが実行できます。

　「F0F0F0F0F0」と表示された場合はSD-Card未挿入、「F1F1F1F1F1」と表示された場合はファイルNoのファイルが存在しない場合です。確認してください。

## 扱えるファイル
　拡張子btkとなっているバイナリファイルです。

　ファイル名は0000～FFFFまでの16進数4桁を付けてください。(例:1000.btk)

　この16進数4桁がTK-85からSD-Card内のファイルを識別するファイルNoとなります。

　構造的には、バイナリファイル本体データの先頭に開始アドレス、終了アドレスの4Byteのを付加した形になっています。

　パソコンのクロスアセンブラ等でTK-85用の実行binファイルを作成したらバイナリエディタ等で先頭に開始アドレス、終了アドレスの4Byteを付加し、ファイル名を変更したものをSD-Cardのルートディレクトリに保存すればTK-85から呼び出せるようになります。

## 実装
　実際に作成したアダプタです。

アダプタの表です。

![SD-Cardアダプタ(表)](https://github.com/yanataka60/TK-85_SD/blob/main/JPG/SD-Card%E3%82%A2%E3%83%80%E3%83%97%E3%82%BF(%E8%A1%A8).JPG)

アダプタの裏です。

![SD-Cardアダプタ(裏)](https://github.com/yanataka60/TK-85_SD/blob/main/JPG/SD-Card%E3%82%A2%E3%83%80%E3%83%97%E3%82%BF(%E8%A3%8F).JPG)

TK-85側のコネクタ部です。

![コネクタ](https://github.com/yanataka60/TK-85_SD/blob/main/JPG/%E3%82%B3%E3%83%8D%E3%82%AF%E3%82%BF.JPG)

実際に装着したところです。

![装着](https://github.com/yanataka60/TK-85_SD/blob/main/JPG/%E8%A3%85%E7%9D%80.JPG)

## 作成例
junk_sugaさんが作成された例です。本体の8255ソケットを利用してスマートに仕上がっています。

![装着](https://github.com/yanataka60/TK-85_SD/blob/main/JPG/junk_suga%E4%BD%9C%E6%88%90%E4%BE%8B(%E8%A1%A8).jpg)

![SD-Cardアダプタ(裏)](https://github.com/yanataka60/TK-85_SD/blob/main/JPG/junk_suga%E4%BD%9C%E6%88%90%E4%BE%8B(%E8%A3%8F).jpg)

## 追記

2021.10.30 TK-85のワークエリアがTK-80より拡大していたことに気付いたため、セーブ範囲とロード時の除外範囲を修正

2022.03.04 junk_sugaさんの作成例を載せました。
