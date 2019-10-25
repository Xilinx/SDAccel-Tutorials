# AWS F1 で最初の SDAccel プログラムを実行

このチュートリアルでは、SDAccel™ カーネルとして RTL デザインをパッケージする方法と、ホスト アプリケーションをアクセラレートする目的でこのカーネルを使用する方法を説明します。SDAccel GitHub サンプル用リポジトリにある [**vadd_kernel**](https://github.com/Xilinx/SDAccel_Examples/tree/master/getting_started/rtl_kernel/rtl_vadd) を使用し、次の点を学びます。

1. [SDAccel カーネル インターフェイス要件に準拠した RTL デザインの作成](Run-your-first-SDAccel-program-on-AWS-F1.md#1-writing-an-rtl-design-adhering-to-the-sdaccel-kernel-interface-requirements)
2. [SDAccel カーネルとして RTL デザインをパッケージ (XO ファイル)](Run-your-first-SDAccel-program-on-AWS-F1.md#2-packaging-the-rtl-design-as-an-sdaccel-kernel-xo-file)  
3. [ホスト アプリケーションおよび RTL カーネルを含んだ FPGA バイナリのコンパイル](Run-your-first-SDAccel-program-on-AWS-F1.md#3-compiling-the-host-application-and-the-fpga-binary-containing-the-rtl-kernel)
4. [Amazon FPGA イメージの作成](Run-your-first-SDAccel-program-on-AWS-F1.md#4-creating-the-amazon-fpga-image)
5. [Amazon FPGA イメージを使用したホスト アプリケーションの実行](Run-your-first-SDAccel-program-on-AWS-F1.md#5-executing-the-host-application-with-the-amazon-fpga-image)

>**注記**: このチュートリアルでは SDAccel **RTL カーネル ウィザード**は使用しません。SDAccel RTL カーネル ウィザードは、RTL デザインを SDAccel カーネルとしてパッケージしやすくするための機能です。このウィザードでは、必須 XML ファイル、サンプル プロジェクト デザイン、サンプル デザインを XO ファイルにビルドするスクリプト セットが生成されます。RTL カーネル ウィザードの使用方法については、この[オンライン ビデオ](https://www.youtube.com/watch?v=IZQ1A2lPXZk)をご視聴ください。



## サンプルの概要
このサンプルは単純な vector-add (ベクター加算) デザインです。ホスト アプリケーションにより、任意の長さの 2 つのベクター (A および B) が FPGA カーネルに書き込まれます。そうすると、この 2 つのベクターの和が計算され、出力ベクター (C) が出力されます。その後、ホスト アプリケーションにより結果がリードバックされます。

#### ハードウェア カーネルの概要
ハードウェア カーネルには AXI メモリ マップド マスター インターフェイスおよび AXI4-Lite スレーブ インターフェイスがあります。
- AXI マスター インターフェイスは、グローバル メモリからの値 A および B を読み出し、値 C をライトバックするのに使用されます。
- AXI4-Lite スレーブ インターフェイスは、次のように、パラメーターを渡し、カーネルを制御するために使用されます。
   - オフセット 0x00: 制御およびステータス レジスタ
   - オフセット 0x10: グローバル メモリのベクター A のベース アドレス
   - オフセット 0x1C: グローバル メモリのベクター B のベース アドレス
   - オフセット 0x28: グローバル メモリのベクター C のベース アドレス
   - オフセット 0x34: ベクターの長さ

制御レジスタのビット 0 が 1 にセットされていると、カーネルが実行を開始します。AXI マスターにより、グローバル メモリから値 A および B を読み出すバースト リクエストが出力され、2 つの FIFO (1 つは値 A 用、もう 1 つは値 B 用) にそのリクエストがストリームされます。加算器モジュールにより、両方の FIFO から読み出しが実行され、`C[i] = A[i] + B[i]` の計算をするため両方の値の和が計算され、その結果値が出力 FIFO に書き込まれます。この FIFO は、vector-add の結果をグローバル メモリにバースト ライトバックするため、AXI マスターによって読み出されます。ベクターが処理されると、カーネルにより、処理の完了を知らせるため制御レジスタのビット 1 がアサートされます。


#### ホスト アプリケーションの概要
host.cpp ファイルには、vector-add カーネルを実行するための非常に単純なアプリケーションが含まれています。すべての FPGA 側の操作は、次の標準 OpenCL™ API 呼び出しを使用してトリガーされます。
- バッファーは `cl::Buffer` を使用して FPGA に作成されます。
- データのコピーは `<command_queue>.enqueueMigrateMemObjects` を使用して FPGA に対して実行されます。
- カーネルの引数 (ベクターの長さ、A、B、C のベース アドレス) は `<kernel>.setArg` を使用して渡されます。
- カーネルは `<command_queue>.enqueueTask` を使用して実行されます。

また、FPGA デバイスは、`xcl::find_binary_file` および `xcl::import_binary_file` のユーティリティ関数を使用して初期化されます。`xcl::find_binary_file` 関数は、目的の FPGA バイナリ ファイルを非常に検索しやすくします。この関数により、次のいずれかの名前に一致するバイナリ ファイルの 4 つのあらかじめ定義されたディレクトリが検索されます。
* `\<name>.\<target>.\<device>.(aws)xclbin`
* `\<name>.\<target>.\<device_versionless>.(aws)xclbin`
* `binary_container_1.(aws)xclbin`
* `\<name>.(aws)xclbin`

## チュートリアルを実行するための準備

- RDP クライアントを使用して、FPGA Developer AMI と一緒に読み込まれた AWS EC2 インスタンスに接続します。詳細な方法は、[AWS F1 インスタンスの作成、設定、テスト](STEP1.md)を参照してください。
- AWS インスタンスのターミナルで次のコマンドを実行して、SDAccel 環境を設定します。   
    ```bash
    cd $AWS_FPGA_REPO_DIR
    source sdaccel_setup.sh
    ```

- サンプルが含まれているディレクトリに移動します。  
    ```bash
    cd $AWS_FPGA_REPO_DIR/SDAccel/examples/xilinx/getting_started/rtl_kernel/rtl_vadd
    ```

- SDAccel GitHub サンプルでは、使いやすくするため、ローカル プロジェクトのソース フォルダーにコピーする必要のある一般的なヘッダー ファイルが使用されます。**make local-files** コマンドを実行して、ローカル ディレクトリにある必要なファイルをすべてコピーします。  
   ```
   make local-files
   ```

## 1. SDAccel カーネル インターフェイス要件に準拠した RTL デザインの作成
SDAccel カーネルとして使用するには、RTL デザインは次の信号およびインターフェイス要件に準拠している必要があります。
 * クロック。
 * アクティブ Low リセット。
 * グローバル メモリ用の 1 つ以上の AXI4-Lite メモリ マップド (MM) マスター インターフェイス。AXI4-Lite MM マスター インターフェイスにはすべて 64 ビット アドレスが必要です。
   - グローバル メモリ空間の分割はユーザーが実行します。グローバル メモリの各パーティションがカーネル引数になります。各パーティションのメモリ オフセットは、AXI4-Lite MM スレーブ インターフェイスを介してプログラム可能な制御レジスタにより設定される必要があります。
 * 1 つの AXI4-Lite MM スレーブ制御インターフェイス。AXI4-Lite インターフェイス名は **S_AXI_CONTROL** にする必要があります。
    - AXI4-Lite MM スレーブのオフセット 0 には次の信号が必要です。
      - `Bit 0`: 開始信号 - このビットがセットされていると、カーネルがデータ処理を開始します。
      - `Bit 1`: 完了信号 - 処理が完了すると、この信号がアサートされます。
      - `Bit 2`: アイドル信号 - どのデータも処理されていない場合、この信号がアサートされます。
 * カーネル間でデータをストリーミングするための 1 つ以上の AXI4-Stream インターフェイス。

インターフェイス要件の詳細は、[SDAccel 環境ユーザー ガイド](https://japan.xilinx.com/html_docs/xilinx2018_3/sdaccel_doc/creating-rtl-kernels-qnk1504034323350.html#qnk1504034323350)を参照してください。

この例では、RTL は既にインターフェイス要件に準拠しているので、変更の必要はありません。

この例の RTL コードは `./src/hdl` ディレクトリにあります。

## 2. RTL デザインを SDAccel カーネルとしてパッケージ (XO ファイル)  
完全にパッケージされた RTL カーネルは、XO ファイルとして配布されます (拡張子は `.xo`)。このファイルは、Vivado® IP オブジェクト (RTL ソース ファイルを含む) およびカーネルを記述した XML ファイルを含むコンテナーです。XO ファイルはプラットフォームにコンパイルでき、SDAccel ハードウェアまたはハードウェア エミュレーション フローで実行できます。

カーネルをパッケージし、XO ファイルを作成するには、次の作業を実行する必要があります。
- カーネル記述 XML ファイルを作成します。
- IP インテグレーターで使用できるよう、RTL を IP としてパッケージします。
- `package_xo` コマンドを実行して XO ファイルを生成します。

#### カーネル記述 XML ファイルの作成    
RTL カーネルのインターフェイス プロパティを記述するには、特別な XML ファイルが必要です。カーネル XML ファイルのフォーマットは、[カーネル記述 XML ファイルの作成](https://japan.xilinx.com/html_docs/xilinx2018_3/sdaccel_doc/creating-rtl-kernels-qnk1504034323350.html#rzv1504034325561)で説明されています。

この XML ファイルは、手動で、または RTL カーネル ウィザードを使用して作成できます。この例では、XML ファイルは既に提供されています (`./src/kernel.xml`)。

- このファイルを開き、XML で記述されている情報を確認します。

#### IP インテグレーターで使用できるよう、RTL を IP としてパッケージ
この例では、既存の RTL デザインを Vivado IP としてパッケージする `./scripts/package_kernel.tcl` スクリプトを使用します。このスクリプトは、`./packaged_kernel_${suffix}` と呼ばれる IP ディレクトリにあり、`suffix` にはユーザー引数を指定します。    

#### package_xo コマンドを実行して XO ファイルを生成
- `SDAccel/examples/xilinx/getting_started/rtl_kernel/rtl_vadd` ディレクトリで、RTL をパッケージし、XO ファイルを生成するため、次のコマンドを実行します。   

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

## 3. ホスト アプリケーションおよび RTL カーネルを含んだ FPGA バイナリのコンパイル
このセクションでは、次のステップについて説明します。
   * SDAccel GUI での新規プロジェクトの作成
   * あらかじめ生成されている XO ファイルを含むデザイン ファイルのインポート
   * ハードウェア エミュレーション フローを使用したアプリケーションの検証
   * ハードウェア実行用のホスト アプリケーションおよび FPGA バイナリのコンパイル   

この例のホスト アプリケーション コードは `./src/host.cpp` ディレクトリにあります。

SDAccel フローでは、FPGA との通信にホスト コードで OpenCL API が使用されます。

### SDAccel GUI での新規プロジェクトの作成
- 次のコマンドを実行し、SDx の GUI を起動します。
   ```bash
  sdx -worskpage Test_dir
  ```
- **[Welcome]** ウィンドウで **[Create SDx Project]** をクリックします。
- **[Project Type]** ページで **[Application]** をオンにして **[Next]** をクリックします。
- プロジェクト名を **TEST_RTL_KERNEL** にして **[Next]** をクリックします。
- **[Platform]** ページで **[Add Custom Platform]** をクリックし、```/home/centos/src/project_data/aws-fpga/SDAccel/aws_platform``` ディレクトリを指定して **[OK]** をクリックします。
- 新しく追加した AWS VU9P F1 カスタム プラットフォームを選択して、**[Next]** をクリックします。
- **[System Configuration]** のデフォルト設定をそのままにし、**[Next]** をクリックします。
- **[Templates]** ページで **[Empty Application]** を選択し、**[Finish]** をクリックします。

### アプリケーションのホスト コードおよびカーネル XO ファイルのインポート
- GUI の左側にある **[Project Explorer]** で **[Import Sources...]** ボタン ![](./images/ImportSRC.png) をクリックします。
- **[Import Sources]** で **[Browse]** ボタンをクリックし、**[rtl_vadd/src]** ディレクトリを選択して、**[OK]** をクリックします。
- **[Import Sources]** の右側で、次のファイルを選択し、**[Finish]** をクリックします。
    * `host.cpp`
    * `xcl2.cpp`
    * `xcl2.h`
    * `rtl_vadd.xo`

![](./images/STEP2-ImportFiles.png)  

**[Project Explorer]** の **[TEST_RTL_KERNEL > src]** フォルダーを展開すると、プロジェクトにデザイン ソースが追加されているのが確認できます。

### カーネル実行ファイルのバイナリ コンテナーの指定
デザイン ファイルをインポートしたので、次は、バイナリ コンテナーおよび関連のハードウェア ファンクションをプロジェクトに追加する必要があります。バイナリ コンテナーは、FPGA コンパイル プロセスの出力を含む出力ファイル (`.xclbin`) です。ハードウェア ファンクションは、実質的にはアクセラレーション カーネルです。バイナリ コンテナーには、ハードウェア ファンクションを 1 つ以上含めることができます。このチュートリアルでは、1 つのみです。

- **[Add Hardware Function...]** ボタン ![](./images/AddHW.png) をクリックします。このボタンは **[Application Project Settings]** の **[Hardware Functions]** セクションにあります。

![](./images/STEP2-AddHW.png)

- SDAccel によりすべての使用可能なカーネルの入力ソースが解析され、XO ファイルの **krnl_vadd_rtl** カーネルが認識されます。**[OK]** をクリックします。

バイナリ コンテナーがプロジェクトに追加され、**krnl_vadd_rtl** カーネルがこのコンテナーに追加されています。バイナリ コンテナーのデフォルト名は `binary_container_1` です。ホスト アプリケーションで `xcl::find_binary_file` ユーティリティ関数が使用されるので、デフォルト名のファイルを検索して、このコンテナーが自動的に検出されます。

### ハードウェア エミュレーション フローを使用したアプリケーションの検証
SDAccel には次の 3 つのビルド コンフィギュレーションがあります。  
* ソフトウェア エミュレーション (`Emulation-SW`)
* ハードウェア エミュレーション (`Emulation-HW`)
* ハードウェア (`System`)

**Emulation-SW** モードでは、カーネルの C/C++ または OpenCL モデルを使用して、ホスト アプリケーションが実行されます。このモードは、主に、アプリケーションが機能的に正しいことを確認するために使用されます。
>**注記**: このモードは、RTL カーネル用には現在サポートされていません。

**Emulation-HW** モードでは、カーネルの RTL モデルを使用して、ホスト アプリケーションが実行されます。また、カスタム演算ユニット用に生成されたロジックが正しいかを確認し、パフォーマンスを見積もることができます。

**System** モードでは、実際の FPGA を使用してホスト アプリケーションが実行されます。

- ハードウェア エミュレーションを実行するには、**[Project Settings]** で **[Active build configuration]** を [`Emulation-HW`] に設定します。

![](./images/STEP2-BuildConfig.png)

- ビルド プロセスを開始するには、**[Build]** ボタン ![](./images/Build.png) をクリックします。このプロセスが完了するまで約 3 ～ 4 分かかります。
- エミュレーション ビルド プロセスが完了したら、**[Run]** ボタン ![](./images/Run.png) をクリックしてハードウェア エミュレーションを実行します。
- この例は実行に数秒しかかかりません。**[Console]** ウィンドウで、実行が完了したことを示す次の用なメッセージが表示されます。
    ```
    Found Platform
    Platform Name: Xilinx
    XCLBIN File Name: vadd
    INFO: Importing ../binary_container_1.xclbin
    Loading: '../binary_container_1.xclbin'
    INFO: [SDx-EM 01] Hardware emulation runs simulation underneath.Using a large data set will result in long simulation times.It is recommended that a small dataset is used for faster execution.This flow does not use cycle accurate models and hence the performance data generated is approximate.
    TEST PASSED
    INFO: [SDx-EM 22] [Wall clock time: 13:05, Emulation time: 0.00385346 ms] Data transfer between kernel(s) and global memory(s)
    BANK0          RD = 2.000 KB               WR = 1.000 KB        
    BANK1          RD = 0.000 KB               WR = 0.000 KB        
    BANK2          RD = 0.000 KB               WR = 0.000 KB        
    BANK3          RD = 0.000 KB               WR = 0.000 KB  
    ```

- エミュレーション実行中に生成された **[Profile Summary]** および **[Application Timeline]** レポートは、GUI の左下にある **[Assistant]** ウィンドウからアクセスできます。

![](./images/STEP2-Assistant.png)

### ハードウェア実行用のホスト アプリケーションおよび FPGA バイナリのコンパイル   
- ハードウェアを実行するには、**[Application Project Settings]** で **[Active build configuration]** を **[System]** に設定します。
- **[Build]** ボタンをクリックして、ハードウェア ビルド プロセスを開始します。
    - この例の場合、ハードウェア ビルドは完了までに約 1 時間かかります。
    - ホスト実行ファイル (`TEST_RTL_KERNEL.exe`) および FPGA バイナリ (`binary_container_1.xclbin`) は `Test_dir/TEST_RTL_KERNEL/System` ディレクトリで生成されます。  
- SDAccel GUI を終了します。

## 4. Amazon FPGA イメージの作成
F1 上でアプリケーションを実行するには、Amazon FPGA イメージ (AFI) をまず FPGA バイナリ (`.xclbin`) から作成する必要があります。
>**注記**: 現在このステップは、SDAccel の GUI からは実行できません。AFI は、AWS の ```create_sdaccel_afi.sh``` コマンド ライン スクリプトを使用して作成されます。

- S3 バケット、S3 dcp フォルダー、S3 log フォルダーの情報を使用し、次のコマンドを実行します。

```bash
cd ./Test_dir/TEST_RTL_KERNEL/System
SDACCEL_DIR/tools/create_sdaccel_afi.sh \
         -xclbin=binary_container_1.xclbin \
         -o=binary_container_1 \
         -s3_bucket=<bucket-name> \
         -s3_dcp_key=<dcp-folder-name> \
         -s3_logs_key=<logs-folder-name>
```

上記のステップで、AFI の ID を含んだ `.awsxclbin` ファイルおよび `_afi_id.txt` ファイルが生成されます。AFI 生成プロセスのステータスを確認するには、AFI ID を使用します。  

- AFI ID をメモします。
```bash
  cat <timestamp>_afi_id.txt
```  

- AFI 生成プロセスのステータスを確認します。
```bash
  aws ec2 describe-fpga-images --fpga-image-ids <AFI ID>
```
AFI が作成され、登録され、使用準備が整った場合は、**Available** と返されます。	そうでない場合は、**Pending** と返されます。   

```json
  State: {
      "Code" : Available"
  }
```

## 5. Amazon FPGA イメージを使用したホスト アプリケーションの実行

AFI のステータスが **Available** になると、F1 インスタンスでアプリケーションを実行できます。  
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

ログのメッセージは非常に簡潔ですが、次のことが実際に実行されています。アプリケーション:
- FPGA プラットフォームの検出。
- ```binary_container_1.awsxclbin``` コンテナーの読み込み。
- コンテナーからの SFI ID の読み出しと、対応する AFI を FPGA にダウンロードするリクエスト。
- FPGA でのバッファーの作成と、2 つのベクター (A および B) の転送。
- 2 つのベクター (A および B) の和を計算するための FPGA カーネルのトリガー。
- 結果値のリードバックと、その確認。

これでこのチュートリアルは終了で、RTL カーネルを使用した AWS F1 での最初の SDAccel プログラムの実行方法を学びました。

ここで停止するか、インスタンスを終了させてください。
<hr/>
<p align="center"><b>
<a href="STEP3.md">次へ: SDAccel RTL フローに関する知識を深める</a>
</b></p>
<br>
<hr/>
<p align="center"><sup>Copyright&copy; 2019-2019 Xilinx</sup></p>
