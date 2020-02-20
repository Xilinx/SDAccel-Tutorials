# コンピューターに SDAccel をインストールして実行

AWS F1 の SDAccel™ フローでは、次の開発モデルがサポートされています。

- AWS EC2 クラウド インスタンスでのクラウドベース開発
- ローカル コンピューターでのオンプレミス開発

どちらの場合も、AWS F1 インスタンスで最終的なバイナリが運用されます。

このガイドでは、オンプレミス フローを始める詳細な手順、特に次の点について説明します。

1. ユーザー環境で SDAccel をインストールしてライセンスを使用
2. SDAccel を使用したオンプレミスでのアプリケーションの構築
3. アプリケーションをアップロードし、F1 で実行

## 使用要件

この資料で説明されている手順を実行する前に、「[AWS F1 インスタンスの作成、設定、テスト](STEP1.md)」チュートリアルを完了させてください。ユーザー環境で SDAccel を設定する前に、クラウドベースの開発環境に慣れておくことが重要です。

## 要件

SDAccel のオンプレミス開発用にサポートされている OS は次のとおりです。

- Ubuntu 16.04.5 LTS、18.04.1 LTS
- CentOS 7.4、7.5、7.6
- RHEL 7.4、7.5、7.6

ほかのシステム要件の詳細は[このページ](https://japan.xilinx.com/html_docs/xilinx2019_1/sdaccel_doc/igz1531201833632.html#olw1504034315783)を参照してください。

# 1\. ユーザー環境で SDAccel をインストールしてライセンスを使用

## SDAccel 開発環境のダウンロード

オンプレミスで SDAccel アプリケーションを開発するには、AWS F1 で運用したのと同じバージョンの SDAccel をインストールしておく必要があります。ツール バージョンおよびダウンロード手順を確認するには、[「オンプレミスでの開発を可能にする」](https://github.com/aws/aws-fpga/edit/master/hdk/docs/on_premise_licensing_help.md)をページを参照してください。

## ライセンスのリクエスト

新規ユーザーの場合は、オンプレミス用の Vivado® ライセンスも取得する必要があります。[こちらから](https://japan.xilinx.com/products/design-tools/acceleration-zone/ef-vivado-sdx-vu9p-op-fl-nl.html)ノード ロックおよびフローティング ライセンスの両方をリクエストできます (ページの右側にあるリンク)。

## SDAccel のインストール

* ツールをインストールするには、『*SDAccel 環境リリース ノート、インストール、およびライセンス ガイド*』 [(UG1238)](https://japan.xilinx.com/html_docs/xilinx2019_1/sdaccel_doc/yrc1534452173645.html) にある手順を参照してください。

## AWS-FPGA Git リポジトリのクローン

AWS GitHub には、F1 インスタンス用に SDAccel を実行しデザインを構築するために必要なプラットフォーム定義ファイルおよび設定スクリプトがすべて含まれています。また、SDAccel を学ぶためサンプルも多数含まれています。

GitHub リポジトリをクローンし、SDAccel 環境を設定するには、使用コンピューターで次のコマンドを実行します。

```bash
cd $HOME
git clone https://github.com/aws/aws-fpga.git
cd aws-fpga                                      
source sdaccel_setup.sh
```

**重要**: sdaccel\_setup.sh を実行すると、sudo へのアクセスを必要とするランタイム ドライバーをインストールしようとするので、エラーがいくつか表示される場合があります。これらのエラーは重要ではないので、無視しても問題はありません。

# 2\. SDAccel を使用したオンプレミスでのデザインの構築

このモジュールでは、次の方法について説明します。

- ローカル コンピューターで SDAccel を実行できる状態であることを確認します。
- バイナリを生成してから F1 インスタンスで運用します。

GitHub のサンプルを使用して、AWS EC2 インスタンスで使用したのと同じコマンド セットを実行できます。

## GUI を開始

SDAccel の GUI を起動するには、次のコマンドを入力します。

```bash
sdx
```

GUI が問題なく開いたことを確認したら、GUI を閉じます。

## ソフトウェア エミュレーションの実行

次のコマンドを実行して、SDAccel の `helloworld` 例に対しソフトウェア エミュレーションを実行します。

```bash
cd $HOME/aws-fpga/SDAccel/examples/xilinx_2019.1/getting_started/host/helloworld_c/
make clean
make check TARGETS=sw_emu DEVICES=$AWS_PLATFORM all
```

## ハードウェア エミュレーションの実行

次のコマンドを実行して、SDAccel の `helloworld` 例に対しハードウェア エミュレーションを実行します。

```bash
cd $HOME/aws-fpga/SDAccel/examples/xilinx_2019.1/getting_started/host/helloworld_c/
make clean
make check TARGETS=hw_emu DEVICES=$AWS_PLATFORM all
```

## F1 運用のためのビルド

* 次のコマンドを実行して、SDAccel の `helloworld` 例に対し FPGA バイナリをビルドします。

```bash
cd $HOME/aws-fpga/SDAccel/examples/xilinx_2019.1/getting_started/host/helloworld_c/
make clean
make TARGETS=hw DEVICES=$AWS_PLATFORM all
```

このプロセスで、ホストおよび FPGA のバイナリが生成されます。

1. ホスト バイナリ: `./helloworld`
2. FPGA バイナリ: `./xclbin/vector_addition.hw.xilinx_aws-vu9p-f1_4ddr-xpr-2pr_4_0.xclbin`

次の方法のいずれかで、`create_sdaccel_afi.sh` スクリプトを使用し `.xclbin` ファイルから Amazon FPGA イメージ (AFI) を作成します。

* コンピューターに AWS CLI をインストールした後、イメージをローカルに作成します。
* AWS インスタンスに `.xclbin` をアップロードし、そこで `create_sdaccel_afi.sh script` を実行します。

このチュートリアルでは、AWS EC2 インスタンスに何もかもアップロードします。

# 3\. アップロードして F1 で実行

このセクションでは、次のステップについて説明します。

- AWS クラウドに FPGA バイナリ (オンプレミスでビルド) をアップロードします。
- `.xclbin` ファイルから AFI を作成します。
- F1 インスタンスでホスト プログラムをコンパイルします。
- F1 インスタンスでアクセラレートされたアプリケーションを実行します。

## AWS クラウドに FPGA バイナリおよびホスト プロフラムをアップロード

1. 必要なファイルが含まれた TAR ファイルを作成します。

```bash
tar cvfz helloworld.tgz Makefile src xclbin/vector_addition.hw.xilinx_aws-vu9p-f1_4ddr-xpr-2pr_4_0.xclbin
```

2. AWS F1 インスタンスを起動します。
3. AWS F1 インスタンスに先ほど作成した TAR ファイルをアップロードします。

```bash
scp -i ~/<AWS key pairs>.pem <xclbin file> centos@<public IP address of EC2 instance>:/home/centos/.
```

> **注記**: または、AWS S3 バケットを使用し、ファイルを転送できます。

## Amazon FPGA イメージを作成

1. AWS F1 インスタンスで ssh を実行します。

```bash
ssh -i <AWS key pairs.pem> -ssh centos@<public IP address of EC2 instance> 22
```

2. TAR ファイルを抽出します。

```bash
mkdir helloworld
cd helloworld
tar xvfz ../helloworld.tgz
```

3. SDAccel 環境を設定します。

```bash
cd $AWS_FPGA_REPO_DIR                                         
source sdaccel_setup.sh
```

4. AWS FPGA バイナリと AFI を `.xclbin` ファイルから作成します。

```bash
cd $HOME/helloworld/xclbin
$SDACCEL_DIR/tools/create_sdaccel_afi.sh \
    -xclbin=<xclbin file name>.xclbin \
    -s3_bucket=<bucket-name> \
    -s3_dcp_key=<dcp-folder-name> \
    -s3_logs_key=<logs-folder-name>
```

## ホスト アプリケーションをコンパイル

makefile を使用してホスト アプリケーションをコンパイルします。

```bash
cd $HOME/helloworld
make exe
```

これで `helloworld` プログラムが作成されます。

## F1 インスタンスでアクセラレートされたアプリケーションを実行

* AFI ステータスが **available** に変更になったら、F1 で次のことが実行できます。

```bash
sudo sh
source /opt/xilinx/xrt/setup.sh   
./helloworld
```

このモジュールでは、オンプレミスで FPGA バイナリを開発し、AFI を作成し、AWS F1 インスタンスでアクセラレートされたアプリケーションを実行する方法を説明しました。

<hr/>
<p align="center"><b><a href="README.md">入門ガイドに戻る</a></b></p><br><hr/>
<p align="center"><sup>Copyright&copy; 2019 Xilinx</sup></p>

この資料は表記のバージョンの英語版を翻訳したもので、内容に相違が生じる場合には原文を優先します。資料によっては英語版の更新に対応していないものがあります。日本語版は参考用としてご使用の上、最新情報につきましては、必ず最新英語版をご参照ください。
