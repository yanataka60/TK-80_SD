# NEC TK-80にSD-Cardとのロード、セーブ機能

![TK-80_SD](https://github.com/yanataka60/TK-80_SD/blob/main/JPEG/TITLE.jpg)

　ARDUINO+SD-CARDをTK-80に接続することでNEC TK-80でSD-CARDとロード、セーブを実現するものです。

　実現するためにTK-80 MONITOR へのパッチ当て、SDアクセスルーチンを合わせてROMに焼く必要があります。

　拡張基板はTK-80BSと共用のEXT-BOARDを使います。

　ROMは27512、27256、2764、2732のどれでも使えるので安く入手できるものを使ってください。

　パッチを当てたTK-80 MONITOR及びSDアクセスルーチンを含めて焼きます。元のROM D454(D464)×3個はすべて引き抜きます。

　RAMは、8000h～81FFhの増設RAMを増設していない場合はSRAM 62256が使えますが、2101(5101)×4個と62256×1個のどちらか安い方を選択してください。

　また、SRAM 62256を選択した場合には4000h～7FFFhまでをRAMとすることもできますので合わせて4000h～83FFhまでがRAMとなります。

　TK-80標準装備のRAM D5101-E×4個(8200h～83FFh)は7SegLEDをDMA表示する為に必要ですので引き抜いてはいけません。(SRAM 62256では代わりになりません。)

## 回路図
　TK-80BS_SDと共通です。TK-80BS_SDリポジトリKiCadフォルダ内のTK-80_EXT-BOARD.pdfを参照してください。

　TK-80単体でTK-80_SDを使うときのTK-80BS_SDとの違いはROM、RAMの割り当てになります。

　ROMとRAMの状況によりGAL22V10への書込みプログラムが変わります。

[回路図](https://github.com/yanataka60/TK-80BS_SD/blob/main/KiCad/TK-80_EXT-BOARD.pdf)

![TK-80_SD](https://github.com/yanataka60/TK-80BS_SD/blob/main/KiCad/TK-80_EXT-BOARD_1.jpg)

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
|U7|SRAM 62256|1|RAMの増設をしないという選択も可|
|U10|Arduino_Pro_Mini_5V|1|Atmega328版を使用 168版は不可。(注3)|
|U2、U4、U9|Pin Header|40Pin×4、14Pin×2|秋月電子通商 細ピンヘッダーPHA-1x40SG又は基板用リードフレームBQ04-SN(注4)|
|C1-C3|積層セラミックコンデンサ 0.1uF|3||
|S1-S3|ピンヘッダ3Pin|3|秋月電子通商 ピンヘッダーPH-1x40SGなど|
|LS1|小型スピーカー|1|秋月電子通商 マイクロスピーカー又は圧電スピーカーPT08-Z185Rなど|
||ピンソケット(任意)|12Pin×2|Arduino_Pro_Miniを取り外し可能としたい場合に調達します 秋月電子通商 FHU-1x42SGなど|

### 注1)秋月電子通商　AE-microSD-LLCNVのJ1ジャンパはショートしてください。

### 注2)MicroSD Card Adapterを使う場合

J2に取り付けます。

MicroSD Card Adapterについているピンヘッダを除去してハンダ付けするのが一番確実ですが、J2の穴にMicroSD Card Adapterをぴったりと押しつけ、裏から多めにハンダを流し込むことでハンダ付けをする方法もあります。なお、この方法の時にはしっかりハンダ付けが出来たかテスターで導通を確認しておいた方が安心です。

ハンダ付けに自信のない方はJ1の秋月電子通商　AE-microSD-LLCNVをお使いください。AE-microSD-LLCNVならパワーLED、アクセスLEDが付いています。

![MicroSD Card Adapter1](https://github.com/yanataka60/TK-80BS_SD/blob/main/JPEG/MicroSD%20Card%20Adapter.jpg)

### 注3)TK-80BS_SD(TK-80_SD)ではArduino Pro MiniのA4、A5ピンは使用しません。

### 注4)8080Aは左側、8228と8255は上側がピンヘッダーです。基板表面に各ICのICソケットをハンダ付けする前に基板裏面からピンヘッダーを挿し、基板表面でハンダ付けします。

![Pin](https://github.com/yanataka60/TK-80BS_SD/blob/main/JPEG/PIN.jpg)

細ピンヘッダーと基板用リードフレームのどちらを選択しても大丈夫ですが、あえて言うなら

〇細ピンヘッダー

　ピンが固く曲がってしまうことは無いので嵌めるときに位置合わせだけ気を付ければ大丈夫。

　細ピンとはいえピンに厚みがあるので嵌めるのに結構力が必要です。ちゃんと嵌っていないと外れやすいです。

　リードフレームより外し易いです。(簡単に抜けてしまうわけではないです)

　扱いがリードフレームより楽です。

〇リードフレーム

　ピンがすぐ曲がってしまうため、嵌めるときに気を付けないとピンが折れ曲がってしまう危険があります。

　嵌ってしまうとかなり外し難いです。無理に外そうとするとピンが曲がってしまうため慎重に外す必要があります。

　外れ難いということは、使っている分には外れないという安心感があります。

## ROMへの書込み
　SDアクセス用プログラムはTK-80BS_SDと共用です。

　まず、MONITOR-ROM(0000h-02FFh)の内容を読み出し、バイナリエディタ等で以下の修正をします。

|ADDRESS|修正前|修正後|
| ------------ | ------------ | ------------ |
|003C|FF|F0|
|0052|FF|F0|
|0080|D5 00|00 03|
|0082|07 01|03 03|
|024B|EF|E0|
|0258|DF|D0|
|0265|BF|B0|

　次にMONITOR-ROMの後ろ(0300h～)にTK-80BS_SDリポジトリ8080フォルダ中のfile_trans_TK80BS.binを連結します。

　0000h～0977h(2424Byte)までのファイルになります。

　ROMに焼き、EXT-BOARDに装着します。

　ROMに27512、27256、2764、2732を使う場合のS1、S2、S3のショートピンの選択は以下のとおりです。

27512
![27512](https://github.com/yanataka60/TK-80_SD/blob/main/JPEG/ROM27512.JPG)

27256
![27256](https://github.com/yanataka60/TK-80_SD/blob/main/JPEG/ROM27256.JPG)

2764
![2764](https://github.com/yanataka60/TK-80_SD/blob/main/JPEG/ROM2764.JPG)

2732
![2732](https://github.com/yanataka60/TK-80_SD/blob/main/JPEG/ROM2732.JPG)
2732の場合、右に寄せて挿入します。

## Arduinoへの書込み
　Arduino用プログラムもTK-80BS_SDと共用です。

　Arduino IDEを使ってTK-80BS_SDリポジトリArduinoフォルダTK-80_SDフォルダ内TK-80_SD.inoを書き込みます。

　SdFatライブラリを使用しているのでArduino IDEメニューのライブラリの管理からライブラリマネージャを立ち上げて「SdFat」をインストールしてください。

　「SdFat」で検索すれば見つかります。「SdFat」と「SdFat - Adafruit Fork」が見つかりますが「SdFat」のほうを使っています。

注)Arduinoを基板に直付けしている場合、Arduinoプログラムを書き込むときは、EXT-BOARDをTK-80本体から外し、GAL22V10を外したうえで書き込んでください。

## GAL22V10への書込み
　WINCUPLファルダに二通りのプログラムがあります。増設RAMの状況によりどちらかのjedファイルを選択してROMライター(TL866II Plus等)を使ってGAL22V10に書き込んでください。

　(1)TK-80本体の増設RAM(8000h-81FFh)を増設済みの場合

　TK80_1(ROM 0000-3FFF,RAM 4000-7FFF)フォルダのTK80.jed

　(2)TK-80本体の増設RAM(8000h-81FFh)を未装着の場合

　TK80_2(ROM 0000-3FFF,RAM 4000-81FF)フォルダのTK80.jed

## SD-CARD
　出来れば8GB以下のSDカードを用意してください。

　ArduinoのSdFatライブラリは、SD規格(最大2GB)、SDHC規格(2GB～32GB)に対応していますが、SDXC規格(32GB～2TB)には対応していません。

　また、SDHC規格のSDカードであっても32GB、16GBは相性により動作しないものがあるようです。

　FAT16又はFAT32が認識できます。NTFSは認識できません。

## 扱えるファイル
　拡張子btkとなっているバイナリファイルです。

　ファイル名は0000～FFFFまでの16進数4桁を付けてください。(例:1000.btk)

　この16進数4桁がTK-80からSD-Card内のファイルを識別するファイルNoとなります。

　BTKファイルのフォーマットは、バイナリファイル本体データの先頭に開始アドレス、終了アドレスの4Byteのを付加した形になっています。

　パソコンのクロスアセンブラ等でTK-80用の実行binファイルを作成したらバイナリエディタ等で先頭に開始アドレス、終了アドレスの4Byteを付加し、ファイル名を変更したものをSD-Cardのルートディレクトリに保存すればTK-80から呼び出せるようになります。

## 操作方法
### Save
　4桁のファイルNo(xxxx)をデータ表示部のLEDに入力してSTORE DATAを押します。

　正常にSaveが完了するとアドレス部にスタートアドレス、データ部にエンドアドレスが表示されます。

　　　8000H～8390Hまでをxxxx.BTKとしてセーブします。セーブ範囲は固定となっていて指定はできません。

　「F0F0F0F0」と表示された場合はSD-Card未挿入です。確認してください。

### Load
　4桁のファイルNo(xxxx)をデータ表示部のLEDに入力してLOAD DATAキーを押します。

　　　xxxx.BTKをBTKヘッダ情報で示されたアドレスにロードします。ただし、8391H～83FFHまでの範囲はライトプロテクトされます。

　正常にLoadが完了するとアドレス部にスタートアドレス、データ部にエンドアドレスが表示されます。スタートアドレスが実行開始アドレスであればそのままRUNキーを押すことでプログラムが実行できます。

　「F0F0F0F0F0」と表示された場合はSD-Card未挿入、「F1F1F1F1F1」と表示された場合はファイルNoのファイルが存在しない場合です。確認してください。

　異常が無いと思われるのにエラーとなってしまう場合にはTK-80をリセットしてからやり直してみてください。

