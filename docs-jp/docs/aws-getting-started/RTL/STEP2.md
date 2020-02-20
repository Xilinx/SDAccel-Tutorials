# AWS F1 で最初のプログラムを実行

このチュートリアルでは、SDAccel™ カーネルとして RTL デザインをパッケージする方法と、ホスト アプリケーションをアクセラレートする目的でこのカーネルを使用する方法を説明します。このチュートリアルでは、DAccel GitHub リポジトリから [**vadd\_kernel**](https://github.com/Xilinx/SDAccel_Examples/tree/master/getting_started/rtl_kernel/rtl_vadd) 例を使用し、次の内容を学びます。

1. [SDAccel カーネル インターフェイスの要件に準拠した RTL デザインを作成](STEP2.md#1-sdaccel-カーネル-インターフェイスの要件に準拠した-rtl-デザインを作成)
2. [RTL デザインを SDAccel カーネル (XO ファイル) としてパッケージ](STEP2.md#2-rtl-デザインを-sdaccel-カーネル-(xo-ファイル)-としてパッケージ)
3. [ホスト アプリケーションおよび RTL カーネルを含んだ FPGA バイナリをコンパイル](STEP2.md#3-ホスト-アプリケーションおよび-rtl-カーネルを含んだ-fpga- バイナリをコンパイル)
4. [Amazon FPGA イメージを作成](STEP2.md#4-amazon-fpga-イメージを作成)
5. [Amazon FPGA イメージを使用してホスト アプリケーションを実行](STEP2.md#5-amazon-fpga-イメージを使用してホスト-アプリケーションを実行)

> **注記**: このチュートリアルでは SDAccel **RTL カーネル ウィザード**は使用しません。SDAccel RTL カーネル ウィザードは、RTL デザインを SDAccel カーネルとしてパッケージしやすくするための機能です。このウィザードでは、必須 XML ファイル、サンプル プロジェクト デザイン、スクリプト セット (サンプル デザインを XO ファイルにビルドする) が生成されます。RTL カーネル ウィザードの使用方法の詳細は、[オンライン ビデオ](https://www.youtube.com/watch?v=IZQ1A2lPXZk)をご覧ください。

## サンプルの概要

このサンプルは単純な vector-add (ベクター加算) デザインです。ホスト アプリケーションにより、任意の長さの 2 つのベクター (A および B) が FPGA カーネルに書き込まれ、この 2 つのベクターの和が計算されると、出力ベクター (C) が出力されます。その後、ホスト アプリケーションにより結果がリードバックされます。

#### ハードウェア カーネルの概要

ハードウェア カーネルには AXI メモリ マップド マスター インターフェイスおよび AXI4-Lite スレーブ インターフェイスがあります。

- AXI マスター インターフェイスは、グローバル メモリから値 A および B を読み出し、値 C をライトバックするために使用されます。
- AXI4-Lite スレーブ インターフェイスは次のようにパラメーターを渡し、カーネルを制御するために使用されます。
  - オフセット 0x00: 制御およびステータス レジスタ
  - オフセット 0x10: グローバル メモリのベクター A のベース アドレス
  - オフセット 0x1C: グローバル メモリのベクター B のベース アドレス
  - オフセット 0x28: グローバル メモリのベクター C のベース アドレス
  - オフセット 0x34: ベクターの長さ

制御レジスタのビット 0 が 1 にセットされていると、カーネルが実行を開始します。AXI マスター インターフェイスは、グローバル メモリから値 A および B を読み出すバースト リクエストを出力し、2 つの FIFO にこれらの値をストリーミングします (A および B をそれぞれの FIFO に分けてストリーミングする)。加算モジュールは、この両方の FIFO から値を読み出して、`C[i] = A[i] + B[i]` を計算するためにこれらの値を加算し、その結果を出力 FIFO に書き込みます。この FIFO は、vector-add の結果をグローバル メモリにバースト ライトバックするため、AXI マスターによって読み出されます。ベクターが処理されると、カーネルにより、処理の完了を知らせるため制御レジスタのビット 1 がアサートされます。

#### ホスト アプリケーションの概要

host.cpp ファイルには、vector-add カーネルを実行するための非常に単純なアプリケーションが含まれています。FPGA 側の操作はすべて、次の標準 OpenCL™ API 呼び出しを使用してトリガーされます。

- `cl::Buffer` を使用して、バッファーは FPGA に作成されます。
- `<command_queue>.enqueueMigrateMemObjects` を使用してデータが FPGA にコピーされ、また FPGA から読み出されます。
- `<kernel>.setArg` を使用してカーネル引数 (ベクターの長さ、ベース アドレス A、B、C) が渡されます。
- `<command_queue>.enqueueTask` を使用してカーネルが実行されます。

ユーティリティ関数 `xcl::find_binary_file` および `xcl::import_binary_file` を使用して、FPGA デバイスが初期化される点に注意してください。`xcl::find_binary_file` 関数により、必要な FPGA バイナリ ファイルが非常に検索しやすくなります。この関数により、次のいずれかの名前に一致するバイナリ ファイルの 4 つのあらかじめ定義されたディレクトリが検索されます。

* `\<name>.\<target>.\<device>.(aws)xclbin`
* `\<name>.\<target>.\<device_versionless>.(aws)xclbin`
* `binary_container_1.(aws)xclbin`
* `\<name>.(aws)xclbin`

## チュートリアルを実行するための準備

- RDP クライアントを使用して、FPGA Developer AMI と一緒に読み込まれた AWS EC2 インスタンスに接続します。詳細は、[AWS F1 インスタンスの作成、設定、テスト](STEP1.md)を参照してください。

- AWS インスタンスのターミナルで次のコマンドを実行し、SDAccel 環境を設定します。

  ```bash
  cd $AWS_FPGA_REPO_DIR
  source sdaccel_setup.sh
  ```

- サンプルが保存されているディレクトリに移動します。

  ```bash
  cd $AWS_FPGA_REPO_DIR/SDAccel/examples/xilinx/getting_started/rtl_kernel/rtl_vadd
  ```

- サンプルでは共通のヘッダー ファイルが使用されます。このファイルは使用しやすくするためにローカル プロジェクトのソース フォルダーにコピーしておく必要があります。**make local-files** コマンドを実行してローカル ディレクトリに必要なファイルをすべてコピーします。

  ```
  make local-files
  ```

## 1\. SDAccel カーネル インターフェイスの要件に準拠した RTL デザインを作成

SDAccel カーネルとして使用するには、RTL デザインは次の信号およびインターフェイス要件に準拠している必要があります。

* クロック。
* アクティブ Low のリセット。
* グローバル メモリにアクセスするための 1 つ以上の AXI4 メモリ マップド (MM) マスター インターフェイス。AXI4-Lite MM マスター インターフェイスにはすべて 64 ビット アドレスが必要です。
  - グローバル メモリ空間の分割はユーザーが実行します。グローバル メモリの各パーティションがカーネル引数になります。各パーティションのメモリ オフセットは、AXI4-Lite MM スレーブ インターフェイスを介してプログラム可能な制御レジスタにより設定される必要があります。
* 1 つの AXI4-Lite MM スレーブ制御インターフェイス。AXI4-Lite インターフェイス名は **S\_AXI\_CONTROL** である必要があります。
  - AXI4-Lite MM スレーブのオフセット 0 には次の信号が必要です。
    - `Bit 0`: 開始信号 - このビットがセットされていると、カーネルがデータ処理を開始します。
    - `Bit 1`: 完了信号 - 処理が完了すると、この信号がアサートされます。
    - `Bit 2`: アイドル信号 - どのデータも処理されていない場合にアサートされます。
* カーネル間でデータを転送するための AXI4-Stream インターフェイス 1 つ以上。

インターフェイス要件の詳細は、[SDAccel ユーザー ガイド](https://japan.xilinx.com/html_docs/xilinx2018_3/sdaccel_doc/creating-rtl-kernels-qnk1504034323350.html#qnk1504034323350)を参照してください。

この例では、RTL は既にインターフェイス要件に準拠しているので、変更の必要はありません。

このサンプルの RTL コードは `./src/hdl` ディレクトリにあります。

## 2\. RTL デザインを SDAccel カーネル (XO ファイル) としてパッケージ

完全にパッケージされた RTL カーネルは XO ファイル (拡張子は `.xo`) として配布されます。このファイルは、Vivado® IP オブジェクト (RTL ソース ファイルを含む) およびカーネルを記述した XML ファイルを含むコンテナーです。XO ファイルはプラットフォームにコンパイルでき、SDAccel ハードウェアまたはハードウェア エミュレーション フローで実行できます。

カーネルをパッケージし、XO ファイルを作成するには、次の作業を実行する必要があります。

- カーネルを記述した XML ファイルを作成。
- RTL デザインを IP インテグレーターで使用するには、Vivado IP としてパッケージ。
- `package_xo` コマンドを実行してキーを生成。

#### カーネルを記述した XML ファイルを作成

RTL カーネルのインターフェイス プロパティを記述するには、特別な XML ファイルが必要です。カーネル XML ファイルのフォーマットは、このガイドの[カーネルを記述した XML ファイル](https://japan.xilinx.com/html_docs/xilinx2018_3/sdaccel_doc/creating-rtl-kernels-qnk1504034323350.html#rzv1504034325561)のセクションで説明されています。

この XML ファイルは、手動で、または RTL カーネル ウィザードを使用して作成できます。この例では、XML ファイルは既に提供されています (`./src/kernel.xml`)。

- XML ファイルを開き、このファイルにどういう情報が記述されているのかを確認します。

#### RTL デザインを IP インテグレーターで使用するため Vivado IP としてパッケージ

このサンプルには `./scripts/package_kernel.tcl` スクリプトがあり、既存の RTL デザインを Vivado IP としてパッケージします。このスクリプトにより、RTL デザインが `./packaged_kernel_${suffix}` という IP ディレクトリに保存されます。この `suffix` はユーザー引数として指定されます。    

#### package\_xo コマンドを実行して XO ファイルを生成

- `SDAccel/examples/xilinx/getting_started/rtl_kernel/rtl_vadd` ディレクトリで次のコマンドを実行して RTL デザインをパッケージし、XO ファイルを作成します。

  ```bash
  vivado -mode tcl  

  # Set suffix for the directory for RTL-IP import   
  Vivado% set suffix rtl_ip    

  # Import the RTL to the “packaged_kernel_{$suffix}” IP directory   
  Vivado% source scripts/package_kernel.tcl   

  # Create the XO file
  Vivado% package_xo -xo_path ./src/rtl_vadd.xo \
                 -kernel_name krnl_vadd_rtl \
                 -ip_directory ./packaged_kernel_rtl_ip \
                 -kernel_xml ./src/kernel.xml
  # Exit Vivado
  Vivado% exit
  ```

`./src/rtl_vadd.xo` ファイルが生成されます。このファイルには、SDAccel でカーネルを使用するのに必要な情報がすべて含まれています。

## 3\. ホスト アプリケーションおよび RTL カーネルを含んだ FPGA バイナリをコンパイル

このセクションでは、次のステップについて説明します。

* SDAccel GUI で新しいプロジェクトを作成
* あらかじめ生成された XO ファイルを含むデザイン ファイルをインポート
* ハードウェア エミュレーション フローを使用してアプリケーションを検証
* ハードウェア実行用にホスト アプリケーションおよび FPGA バイナリをコンパイル

このサンプルのアプリケーション コードは `./src/host.cpp` ディレクトリにあります。

SDAccel フローでは、FPGA との通信にホスト コードで OpenCL API が使用されます。

### SDAccel GUI で新しいプロジェクトを作成

- 次のコマンドを実行して SDx GUI を開きます。
  ```bash
  sdx -worskpage Test_dir
  ```
- \[**Welcome**] ウィンドウで \[**Create SDx Project**] を選択します。
- \[**Project Type**] ページで \[**Application**] をオンにして \[**Next**] をクリックします。
- プロジェクト名を「**TEST\_RTL\_KERNEL**」に設定して \[**Next**] をクリックします。
- \[**Platform**] ページで \[**Add Custom Platform**] をクリックし、`/home/centos/src/project_data/aws-fpga/SDAccel/aws_platform` ディレクトリを指定して \[**OK**] をクリックします。
- 新しく追加した AWS VU9P F1 カスタム プラットフォームを選択して、\[**Next**] をクリックします。
- \[**System Configuration**] ダイアログ ボックスはデフォルトの設定のままにして \[**Next**] をクリックします。
- \[**Templates**] ページで \[**Empty Application**] を選択し、\[Finish] をクリックします。

### アプリケーション ホスト コードおよびカーネル XO ファイルをインポート

- GUI の左側にある \[**Project Explorer**] ペインで \[**Import Sources]** ボタン ![](./images/ImportSRC.png) をクリックします。
- \[**Import Sources**] 画面で \[**Browse**] ボタンをクリックし、**rtl\_vadd/src** ディレクトリを選択して、\[**OK**] をクリックします。
- \[**Import Sources**] 画面の右側で、次にリストされているファイルを選択し、\[**Finish**] をクリックします。
  * `host.cpp`
  * `xcl2.cpp`
  * `xcl2.h`
  * `rtl_vadd.xo`

![](./images/STEP2-ImportFiles.png)

これでデザイン ソースがプロジェクトに追加されました。\[**Project Explorer**] にある \[**TEST\_RTL\_KERNEL**] を \[src] まで展開すると、ソースが追加されていることを確認できます。

### カーネル実行ファイル用にバイナリ コンテナーを指定

デザイン ファイルをインポートしたので、次は、バイナリ コンテナーおよび関連のハードウェア ファンクションをプロジェクトに追加する必要があります。バイナリ コンテナーは FPGA コンパイル プロセスの出力を含む出力ファイル (`.xclbin`) です。ハードウェア ファンクションは、実質的にはアクセラレーション カーネルです。バイナリ コンテナーには、ハードウェア ファンクションを 1 つ以上含めることができます。このチュートリアルでは、1 つのみです。

- \[**Add Hardware Function**] ボタン ![](./images/AddHW.png) をクリックします。このボタンはメインの \[**Project Settings**] ウィンドウの \[**Hardware Functions**] セクションの中央にあります。

![](./images/STEP2-AddHW.png)

- SDAccel によりすべての使用可能なカーネルの入力ソースが解析され、XO ファイルからの **krnl\_vadd\_rtl** カーネルが認識されます。\[**OK**] をクリックします。

バイナリ コンテナーがプロジェクトに追加され、**krnl\_vadd\_rtl** カーネルがこのコンテナーに追加されています。このバイナリ コンテナーのデフォルト名は `binary_container_1` です。ホスト アプリケーションでは `xcl::find_binary_file` ユーティリティ関数が使用されるので、このデフォルト名の付いたファイルを検索すると、コンテナーが自動的に検出されます。

### ハードウェア エミュレーション フローを使用してアプリケーションを検証

SDAccel には次の 3 つのビルド コンフィギュレーションがあります。

* ソフトウェア エミュレーション (`Emulation-SW`)
* ハードウェア エミュレーション (`Emulation-HW`)
* ハードウェア (`System`)

**Emulation-SW** モードでは、ホスト アプリケーションをカーネルの C/C++ または OpenCL モデルを使用して実行します。このモードは、主に、アプリケーションが機能的に正しいことを確認するために使用されます。

> **注記**: このモードは RTL カーネル用には現在サポートされていません。

**Emulation-HW** モードでは、ホスト アプリケーションをカーネルの RTL モデルを使用して実行します。また、カスタム演算ユニット用に生成されたロジックが正しいかを確認し、パフォーマンスを見積もることができます。

**System** モードでは、ホスト アプリケーションを実際の FPGA 使用して実行します。

- ハードウェア エミュレーションを実行するには、メインの \[**Project Settings**] ウィンドウに移動し、\[**Active build configuration**] が `Emulation-HW` に設定されていることを確認します。

![](./images/STEP2-BuildConfig.png)

- ビルド プロセスを開始するには、\[**Build**] ボタン ![](./images/Build.png) をクリックします。このプロセスが完了するまで約 3 ～ 4 分かかります。

- このエミュレーション ビルド プロセスが完了したら、\[**Run**] ボタン ![](./images/Run.png) をクリックしてハードウェア エミュレーションを実行します。

- このサンプルは実行にわずか数秒しかかかりません。\[**Console**] ウィンドウには、この実行が完了したことを示す次のようなメッセージが表示されるはずです。

  ```
  Found Platform
  Platform Name: Xilinx
  XCLBIN File Name: vadd
  INFO: Importing ../binary_container_1.xclbin
  Loading: '../binary_container_1.xclbin'
  INFO: [SDx-EM 01] Hardware emulation runs simulation underneath. Using a large data set will result in long simulation times. It is recommended that a small dataset is used for faster execution. This flow does not use cycle accurate models and hence the performance data generated is approximate.
  TEST PASSED
  INFO: [SDx-EM 22] [Wall clock time: 13:05, Emulation time: 0.00385346 ms] Data transfer between kernel(s) and global memory(s)
  BANK0          RD = 2.000 KB               WR = 1.000 KB        
  BANK1          RD = 0.000 KB               WR = 0.000 KB        
  BANK2          RD = 0.000 KB               WR = 0.000 KB        
  BANK3          RD = 0.000 KB               WR = 0.000 KB  
  ```

- エミュレーション実行中に生成された **Profile Summary** および **Application Timeline** レポートは、GUI の左下にある \[**Assistant**] ウィンドウからアクセスできます。

![](./images/STEP2-Assistant.png)

### ハードウェア実行用にホスト アプリケーションおよび FPGA バイナリをコンパイル

- ハードウェアを実行するには、メインの \[**Project Settings**] ウィンドウで \[**Active build configuration**] を \[**System**] に設定します。
- \[**Build**] アイコンをクリックして、ハードウェア ビルド プロセスを開始します。
  - このサンプルの場合、ハードウェア ビルドが終了するまでには約 1 時間かかります。
  - ホスト実行ファイル (`TEST_RTL_KERNEL.exe`) および FPGA バイナリ (`binary_container_1.xclbin`) は `Test_dir/TEST_RTL_KERNEL/System` ディレクトリに生成されます。
- SDAccel GUI を終了します。

## 4\. Amazon FPGA イメージを作成

F1 上でアプリケーションを実行するには、Amazon FPGA イメージ (AFI) をまず FPGA バイナリ (`.xclbin`) から作成する必要があります。

> **注記**: 現在このステップは SDAccel GUI からは実行できません。AFI は AWS `create_sdaccel_afi.sh` コマンド ライン スクリプトを使用して作成されます。

- S3 バケット、S3 dcp フォルダー、および S3 log フォルダーの情報を使用して、次のコマンドを実行します。

```bash
cd ./Test_dir/TEST_RTL_KERNEL/System
SDACCEL_DIR/tools/create_sdaccel_afi.sh \
         -xclbin=binary_container_1.xclbin \
         -o=binary_container_1 \
         -s3_bucket=<bucket-name> \
         -s3_dcp_key=<dcp-folder-name> \
         -s3_logs_key=<logs-folder-name>
```

上記のステップで、`.awsxclbin` ファイルと、AFI ID を含んだ `_afi_id.txt` ファイルが生成されます。AFI 生成プロセスのステータスを確認するには、AFI ID を使用します。

- AFI ID をメモしておきます。

```bash
  cat <timestamp>_afi_id.txt
```

- AFI 生成プロセスのステータスを確認します。

```bash
  aws ec2 describe-fpga-images --fpga-image-ids <AFI ID>
```

AFI が生成されて登録され、使用できるようになると、コマンドにより **Available** が戻されます。	そうでない場合は、**Pending** が戻されます。

```json
  State: {
      "Code" : Available
  }
```

## 5\. Amazon FPGA イメージを使用してホスト アプリケーションを実行

AFI が**使用可能**になったので、F1 インスタンスでアプリケーションを実行できるようになりました。

```bash
sudo sh
source /opt/xilinx/xrt/setup.sh   
./TEST_RTL_KERNEL.exe
```

次の内容が出力されるはずです。

```bash
Device/Slot[0] (/dev/xdma0, 0:0:1d.0)
xclProbe found 1 FPGA slots with XDMA driver running
platform Name: Xilinx
Vendor Name : Xilinx
Found Platform
XCLBIN File Name: vadd
INFO: Importing ./binary_container_1.awsxclbin
Loading: './binary_container_1.awsxclbin'
TEST PASSED
```

ログのメッセージは非常に簡潔ですが、多くの操作が実行されています。アプリケーションにより、次のことが実行されています。

- FPGA プラットフォームが検出されました。
- `binary_container_1.awsxclbin` コンテナーが読み込まれました。
- コンテナーから AFI ID が取り込まれ、この ID に対応する AFI が FPGA にダウンロードされるようにリクエストされました。
- FPGA にバッファーが作成され、2 つのベクター (A および B) が転送されました。
- この 2 つのベクター (A および B) を足すため、FPGA カーネルがトリガーされました。
- 結果値がリードバックされ、その値が正しいことが確認されました。

これでこのチュートリアルは終了で、RTL カーネルを使用した AWS F1 での最初の SDAccel プログラムの実行方法を学びました。

ここで停止するか、インスタンスを終了させてください。

<hr/>
<p align="center"><b><a href="STEP3.md">次へ: SDAccel RTL フローに関する知識を深める</a></b></p><br><hr/>
<p align="center"><sup>Copyright&copy; 2019 Xilinx</sup></p>

この資料は表記のバージョンの英語版を翻訳したもので、内容に相違が生じる場合には原文を優先します。資料によっては英語版の更新に対応していないものがあります。日本語版は参考用としてご使用の上、最新情報につきましては、必ず最新英語版をご参照ください。
