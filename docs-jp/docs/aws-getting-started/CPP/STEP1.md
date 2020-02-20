# AWS F1 インスタンスの作成、設定、テスト

このモジュールでは、次の手順を実行します。

1. [AWS コンソールから EC2 F1 インスタンスを作成](STEP1.md#1-aws-コンソールから-ec2-f1-インスタンスを作成)
2. [リモート デスクトップを使用してインスタンスへ接続](STEP1.md#2-リモート-デスクトップを使用してインスタンスへ接続)
3. [SDAccel で使用できるようにインスタンスを設定](STEP1.md#3-sdaccel-で使用できるようにインスタンスを設定)
4. [SDAccel の Hello World 例を AWS F1 で実行](STEP1.md#4-sdaccel-の-hello-world-例を-aws-f1-で実行)
5. [セッションを閉じる](STEP1.md#5-セッションを閉じる)

> **重要**: このモジュールは、必ず*入門ガイド*の[**必須条件**](../PREREQUISITES/README.md)を確認してから開始するようにしてください。

## 1\. AWS コンソールから EC2 F1 インスタンスを作成

#### 手順 1: インスタンスを起動

1. AWS EC2 ダッシュボード ([https://console.aws.amazon.com/ec2](https://console.aws.amazon.com/ec2)) に移動します。
2. 右上の F1 インスタンスのリージョン (**\[US West (Oregon)]** など) を選択します。
3. **\[Launch Instance]** をクリックします。

#### 手順 2: Amazon マシン イメージ (AMI) を選択

1. **\[AWS Marketplace]** (左側の \[Quick Start] の下の 2 つ目の項目) をクリックします。
2. **FPGA** を検索します。
3. **\[FPGA Developer AMI]** を選択します。
4. ダイアログ ボックスが開き、各インスタンス タイプのレートが表示されます。
5. **\[Continue]** をクリックします。

#### 手順 3: インスタンス タイプを選択

1. **f1.2xlarge** インスタンスを選択します。
2. **\[Next: Configure Instance Details]** をクリックします。

> **注記**: **\[Review and Launch]** はクリックしないでください。

#### 手順 4: インスタンスを設定

1. **\[Next: Add Storage]** をクリックします (デフォルト設定を変更する必要はありません)。
2. **\[Next: Add tags]** をクリックします (デフォルト設定を変更する必要はありません)。
3. **\[Next: Configure Security group]** をクリックします (デフォルト設定を変更する必要はありません)。

#### 手順 5: セキュリティ グループを設定

ここでは、TCP ポート # 3389 を使用してインバウンド RDP リクエストができるように、インスタンスに接続するセキュリティ グループを変更します。リモート デスクトップ GUI セッションを使用するには、これを必ず変更しておく必要があります。

1. **\[Add Rule]** をクリックして、次のプロパティで新しいセキュリティ ルールを作成します。
   - **\[Type]**: RDP
   - **\[Protocol]**: TCP (デフォルト値)
   - **\[Port Range]**: 3389 (デフォルト値)
   - **\[Source]**: Custom - 0.0.0.0/0
2. **\[Review and Launch]** をクリックします。

#### 手順 6: インスタンスを確認

1. **\[Launch]** をクリックします。
2. 既存キー ペアを選択するか、新しいキー ペアを作成します。
3. ダイアログ ボックス一番下のチェック ボックスをオンにします。
4. **\[Launch Instances]** をクリックします。

#### 手順 7: インスタンスのステータスをチェック

1. 準備ができたら、ページ一番下の **\[View Instances]** をクリックします。
2. 一番下のペインに表示されるインスタンスの \[IPv4 Public DNS] および \[Public IP] アドレスをメモしておいてください。

> **重要**: これらのアドレスは、インスタンスへの接続に必要です。

新しく起動したインスタンスのステータスが緑 (実行中) に変わったら、接続する準備ができています。<br>

## 2\. リモート デスクトップを使用してインスタンスへ接続

#### 手順 1: SSH (Linux) または PuTTY (Windows) を使用してインスタンスへ接続

次のコマンドのいずれかを使用してインスタンスへ接続します。

* Linux コンピューターから接続する場合は、SSH を使用して接続します。

```bash
ssh -i <AWS key pairs.pem> -ssh centos@<public IP address of EC2 instance> 22
```

* Windows コンピューターから接続する場合は、PuTTY を使用して接続します。

```bash
putty -i <AWS key pairs.ppk> -ssh centos@<public IP address of EC2 instance> 22
```

説明:

- `<AWS key pairs\>` は、キー ペアを含む `.pem` (SSH) または `.ppk` (PuTTY) ファイルへのフル パスです。
- `<public DNS of EC2 instance\>` はインスタンスのパブリック DNS です。
- ユーザー名は常に **centos** です。
- 接続タイプは常に **SSH** です。
- ポートは常に **22** です。

これで EC2 インスタンスに接続できるようになりました。

#### 手順 2: インスタンスの GUI サービスを設定

FPGA Developer AMI には GUI デスクトップ アプリケーションが含まれません。この手順では、Gnome ウィンドウ マネージャーをインストールしてリモート デスクトップ プロトコル (RDP) サービスを開始し、リモート デスクトップ接続ができるようにします。

1. インスタンス ターミナルのコマンド ラインから次のコマンドを実行します。

```bash
source <(curl -s https://s3.amazonaws.com/aws-fpga-developer-ami/1.5.0/Scripts/setup_gui.sh)
```

このスクリプトの実行には約 10 分かかります。最後に \`centos\` ユーザー用のパスワードを設定して終了します。

**重要**: スクリプトで生成されるパスワードはメモしておきます。RDP を使用して接続する際に必要となります。

2. ウェブ ブラウザーで **\[**AWS EC2 dashboard**]** に戻ります。

3. インスタンス名を選択したら、**\[Actions]** → **\[Instance State]** → **\[Reboot]** をクリックします。

4. インスタンスがリブート サイクルを終了するまで数分待ちます。

終了したら、リモート デスクトップ クライアントを使用してインスタンスを接続できるようになります。

#### 手順 3: リモート デスクトップ クライアントを使用してインスタンスへ接続

1. ローカル マシンからリモート デスクトップ プロトコル (RDP) クライアントを開始します。

- Windows の場合: **Windows** キーを押して、プロンプトに「**mstsc.exe**」と入力します。
- Linux の場合: Remmina や Vinagre などの RDP クライアントを使用します。
- macOS の場合: Mac アプリ ストアからの Microsoft Remote Desktop v8.0.43 を使用します。このバージョンでは色深度を設定できるようになっています。

> **重要**: RDP クライアントで**色深度 24 ビット**を使用するように設定します。

- Windows の場合: 接続プロンプトの左下の **\[Options]** をクリックし、**\[Display]** タブで **\[Colors]** を **\[True Colors (24 bit)]** に設定します。

* RDP クライアントでインスタンスの「**IPv4 Public IP**」を入力します。
* **\[Connect]** をクリックします。接続証明を知らせるダイアログ ボックスが開きます。
* **\[Yes]** をクリックしてメッセージを消します。**\[Remote Desktop Connection]** ウィンドウのログイン画面が表示されます。
* 次を使用してログインします。
  - ユーザー名: **centos**
  - パスワード: `setup_gui.sh` スクリプトで生成されたパスワード
* **\[OK]** をクリックします。

これで Centos 7 および FPGA Developer AMI を実行する F1 インスタンスに接続できました。

## 3\. SDAccel で使用できるようにインスタンスを設定

#### 手順 1: AWS コマンド ライン インターフェイス (CLI) を設定

Amazon FPGA イメージを作成するには、AWS CLI を正しく設定する必要があります。

1. デスクトップ エリアで右クリックして **\[Open Terminal]** をクリックし、新しいターミナルを開きます。
2. AWS CLI は次のように設定します。

```bash
$ aws configure
AWS Access Key ID [None]: <your access key>
AWS Secret Access Key [None]: <your secret key>
Default region name [None]: <your AWS region, for instance us-west-2 for Oregon>
Default output format [None]: json
```

#### 手順 2: SDAccel 環境を設定

ここでは、AWS F1 でアプリケーションをビルドして実行するのに必要なファイルをインストールします。必要なファイルは、SDAccel プラットフォーム、ランタイム、ドライバーなどです。

1. インスタンス ターミナルのコマンド ラインから次のコマンドを実行します。
   ```bash
   git clone https://github.com/aws/aws-fpga.git $AWS_FPGA_REPO_DIR  
   cd $AWS_FPGA_REPO_DIR                                         
   source sdaccel_setup.sh
   ```

> **注記**: sdaccel\_setup.sh は、最初に実行する際にプラットフォームをダウンロードして、ランタイムおよびドライバーをコンパイルするので、読み込むのに数分かかります。2 回目からはかなり速くなります。sdaccel\_setup.sh では SDAccel の実行に必要な環境変数が設定されるので、新しいターミナルを開くたびに、sdaccel\_setup.sh を読み込む必要があります。  
>
2\. ターミナルを閉じます。

## 4\. SDAccel の Hello World 例を AWS F1 で実行

この最後のセクションでは、AWS F1 で SDAccel の `helloworld_c` 例を実行し、環境が正しく設定されているかどうかを確認します。

#### 手順 1: SDAccel 環境を設定

1. 新しいターミナル ウィンドウを開きます。
2. 次のコマンドを実行して、SDAccel 環境を設定します。

```bash
cd $AWS_FPGA_REPO_DIR  
source sdaccel_setup.sh
```

#### 手順 2: エミュレーション フローをビルドして実行

SDAccel エミュレーション フローでは、F1 でアプリケーションを運用する前に、テスト、プロファイル、デバッグができます。

1. SDAccel の 'hello world' 例でソフトウェア エミュレーション フローを実行します。

```bash
cd $SDACCEL_DIR/examples/xilinx/getting_started/host/helloworld_c/
make clean
make check TARGETS=sw_emu DEVICES=$AWS_PLATFORM all
```

2. SDAccel の 'hello world' 例でハードウェア エミュレーション フローを実行します。

```bash
cd $SDACCEL_DIR/examples/xilinx/getting_started/host/helloworld_c/
make clean
make check TARGETS=hw_emu DEVICES=$AWS_PLATFORM all
```

#### 手順 3: ホスト アプリケーションと FPGA バイナリをビルドして F1 で実行

F1 で実行するには、次のファイルをビルドする必要があります。

- ホスト アプリケーション
- AWS FPGA バイナリ
- Amazon FPGA イメージ (AFI)

これらのファイルは、次の 2 つのプロセスでビルドできます。まず SDAccel を使用してホスト アプリケーションとザイリンクス FPGA バイナリを作成します。次に AWS の create\_sdaccel\_afi.sh スクリプトを使用して、ザイリンクス FPGA バイナリから AFI と AWS FPGA バイナリを作成します。

1. ホスト アプリケーションと \*.xclbin (ザイリンクス FPGA バイナリ ファイル) をビルドします。

```bash
cd $SDACCEL_DIR/examples/xilinx/getting_started/host/helloworld_c/
make clean
make TARGETS=hw DEVICES=$AWS_PLATFORM all
```

2. AWS FPGA バイナリと AFI を \*.xclbin (ザイリンクス FPGA バイナリ ファイル) から作成します。

```bash
cd xclbin
$SDACCEL_DIR/tools/create_sdaccel_afi.sh \
	  -xclbin=<xclbin file name>.xclbin \
	  -s3_bucket=<bucket-name> \
	  -s3_dcp_key=<dcp-folder-name> \
	  -s3_logs_key=<logs-folder-name>
```

`create_sdaccel_afi.sh` スクリプトは、次を実行します。

1. AFI を作成するバックグラウンド プロセスを開始します。
2. 生成した AFI の FPGA イメージ識別子 (AFI ID) およびグローバル FPGA イメージ識別子 (AGFI ID) を含む `<timestamp>_afi_id.txt` ファイルを生成します。
3. ホスト アプリケーションがどの AFI を FPGA に読み込むのかを指定する `*.awsxclbin` AWS FPGA バイナリ ファイルを作成します。

#### 手順 4: AFI 作成プロセスの終了を待機

バックグラウンドで開始された AFI の作成プロセスは、すぐには終了しません。プロセスが問題なく終了してからでないと、F1 インスタンスで実行できません。

1. `<timestamp>_afi_id.txt` ファイルを開いて、AFI ID の値をメモします。

```bash
cat *.afi_id.txt
```

2. describe-fpga-images API を使用して、AFI 生成プロセスのステータスを確認します。

```bash
aws ec2 describe-fpga-images --fpga-image-ids <AFI ID>
```

3. AFI が問題なく作成されたら、次が表示されます。

```json
...
"State": {
    "Code": "available"
},
...
```

4. F1 インスタンスでアプリケーションを実行する前に、AFI が使用可能になるのを待ちます。

#### 手順 5: ホスト アプリケーションを実行

:warning: **重要**: 前の手順には AWS EC2 F1 インスタンスは不要です。前の手順は、C4 や C5 など、ほかの AWS EC2 F1 インスタンスで実行できます。ただし、その後の手順は、AWS EC2 F1 インスタンスで実行する必要があります。F1 インスタンスで次の手順を実行しない場合はエラーメッセージが表示されます。

1. インスタンス ターミナルで次のコマンドを実行します。

```bash
cd $SDACCEL_DIR/examples/xilinx/getting_started/host/helloworld_c/
sudo sh
source /opt/xilinx/xrt/setup.sh   
./helloworld
```

2. このアプリケーション例の場合、次のメッセージが表示されます。

   ```bash
   Device/Slot[0] (/dev/xdma0, 0:0:1d.0)
   xclProbe found 1 FPGA slots with XDMA driver running
   platform Name: Xilinx
    Vendor Name : Xilinx
    Found Platform
    Found Device=<platform name>
    XCLBIN File Name: vector_addition
    INFO: Importing ./<awsxclbin file name>.awsxclbin
    Loading: './<awsxclbin file name>.awsxclbin'
    Result =
    42 42 42 42 42 42 42 42 42 42 42 42 42 42 42 42
    42 42 42 42 42 42 42 42 42 42 42 42 42 42 42 42
    42 42 42 42 42 42 42 42 42 42 42 42 42 42 42 42
    42 42 42 42 42 42 42 42 42 42 42 42 42 42 42 42
    42 42 42 42 42 42 42 42 42 42 42 42 42 42 42 42
    42 42 42 42 42 42 42 42 42 42 42 42 42 42 42 42
    42 42 42 42 42 42 42 42 42 42 42 42 42 42 42 42
    42 42 42 42 42 42 42 42 42 42 42 42 42 42 42 42
    42 42 42 42 42 42 42 42 42 42 42 42 42 42 42 42
    42 42 42 42 42 42 42 42 42 42 42 42 42 42 42 42
    42 42 42 42 42 42 42 42 42 42 42 42 42 42 42 42
    42 42 42 42 42 42 42 42 42 42 42 42 42 42 42 42
    42 42 42 42 42 42 42 42 42 42 42 42 42 42 42 42
    42 42 42 42 42 42 42 42 42 42 42 42 42 42 42 42
    42 42 42 42 42 42 42 42 42 42 42 42 42 42 42 42
    42 42 42 42 42 42 42 42 42 42 42 42 42 42 42 42
    TEST PASSED
    sh-4.2#
   ```

## 5\. セッションを閉じる

セッションを終了したら、インスタンスで **\[Stop]** または **\[Terminate]** をクリックします。

インスタンスを **\[Terminate]** すると、インスタンスのルート ボリュームが削除されます。次に F1 インスタンスを使用する必要がある場合は、新しいインスタンスを作成および設定する必要があります。

インスタンスを停止すると、ルート ボリュームを保持したまま、停止したインスタンスを後で再起動できます。これ以降の接続は、このガイドで簡単に説明したように、新しい RDP クライアント セッションを開始するのと同じくらい簡単になります。設定を最初からやり直す必要はありません。停止しているインスタンスには、AWS で課金されませんが、そのインスタンスにつながっている EBS ボリュームに対しては課金される可能性があります。

1. リモート セッションを閉じます。
2. EC2 ダッシュボード [https://console.aws.amazon.com/ec2](https://console.aws.amazon.com/ec2) に戻ります。
3. メイン ウィンドウで **\[Running Instances]** をクリックします。
4. インスタンスを選択します。
5. **\[Actions]** ドロップダウン リストをクリックし、**\[Instance State]** を選択して、**\[Stop]** または **\[Terminate]** をクリックします。

<hr/>
<p align="center"><b><a href="STEP2.md">次の演習: AWS F1 で最初の SDAccel プログラムを実行</a></b></p><br><hr/>
<p align="center"><sup>Copyright&copy; 2019 Xilinx</sup></p>

この資料は表記のバージョンの英語版を翻訳したもので、内容に相違が生じる場合には原文を優先します。資料によっては英語版の更新に対応していないものがあります。日本語版は参考用としてご使用の上、最新情報につきましては、必ず最新英語版をご参照ください。
