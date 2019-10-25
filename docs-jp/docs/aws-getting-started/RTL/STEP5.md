# コンピューターに SDAccel をインストールして実行

AWS F1 の SDAccel™ フローでは、次の開発モデルがサポートされています。
- AWS EC2 クラウド インスタンスでのクラウドベースの開発
- ローカル ワークステーションでのオンプレミス開発

どちらの場合も、AWS F1 インスタンスで最終的なバイナリが運用されます。

このガイドでは、オンプレミス フローを始める詳細な手順、特に次の点について説明します。
1. ユーザー環境で SDAccel をインストールしてライセンスを使用
1. SDAccel を使用したオンプレミスでのアプリケーションの構築
1. アプリケーションをアップロードし、F1 で実行

## 使用条件
このガイドで説明されている手順を進める前に、[AWS F1 インスタンスの作成、設定、テスト](STEP1.md) のチュートリアルを終わらせてください。ユーザー環境で SDAccel を設定する前に、クラウドベースの開発環境に慣れておくことが重要です。

## 要件
SDAccel のオンプレミス開発用にサポートされている OS は次のとおりです。
  - Red Hat Enterprise Workstation/Server 7.3-7.4 (64 ビット)
  - CentOS 7.2
  - CentOS 7.3-7.4 (64 ビット)
  - Ubuntu Linux 16.04.3 LTS (64 ビット)
    - Linux カーネル 4.4.0 がサポートされます。
    - Ubuntu LTS enablement (HWE または Hardware Enablement とも呼ばれる) はサポートされません。

# 1. ユーザー環境で SDAccel をインストールしてライセンスを使用

## SDAccel 開発環境のダウンロード
オンプレミスで SDAccel アプリケーションを開発するには、AWS F1 で運用したのと同じバージョンの SDAccel をインストールしておく必要があります。
SDAccel インストーラーはこちらにあります。

* ザイリンクス Vivado v2018.2 または v2018.2.op (64 ビット)
* ライセンス: EF-VIVADO-SDX-VU9P-OP
* ソフトウェア ビルド 2258646 (2018 年 6 月 14 日、米国山岳部標準時 20:02:38)
* IP ビルド 2256618 (2018 年 6 月 14 日、米国山岳部標準時 22:10:49)
* URL: [https://www.xilinx.com/member/forms/download/xef.html?filename=Xilinx_SDx_op_Lin_2018.2_0614_1954_Lin64.bin&akdm=0](https://www.xilinx.com/member/forms/download/xef.html?filename=Xilinx_SDx_op_Lin_2018.2_0614_1954_Lin64.bin&akdm=0)
* MD5 SUM 値: 6b6939e70d4fa90677d2c54a37ec25c7

## ライセンスのリクエスト

新規ユーザーの場合は、オンプレミス用の Vivado® ライセンスも取得する必要があります。ノードロックおよびフローティング ライセンスの両方を [こちらから] (https://japan.xilinx.com/products/design-tools/acceleration-zone/ef-vivado-sdx-vu9p-op-fl-nl.html) リクエストできます (リンクはページの右側にあります)。

## SDAccel のインストール

* ツールをインストールするには、『SDAccel 開発環境リリース ノート、インストール、ライセンス ガイド』 [(UG1238)](https://japan.xilinx.com/support/documentation/sw_manuals/xilinx2018_3/ug1238-sdx-rnil.pdf) を参照してください。

## AWS-FPGA Git リポジトリのクローン

AWS GitHub には、F1 インスタンス用に SDAccel を実行しデザインを構築するために必要なプラットフォーム定義ファイルおよび設定スクリプトがすべて含まれています。また、SDAccel を学ぶためサンプルも多数含まれています。  

GitHub リポジトリをクローンし、SDAccel 環境を設定するには、使用コンピューターで次のコマンドを実行します。
```bash
cd $HOME
git clone https://github.com/aws/aws-fpga.git
cd aws-fpga                                      
source sdaccel_setup.sh
```

**重要**: sdaccel_setup.sh を実行すると、sudo アクセスが必要なランタイム ドライバーをインストールしようとするので、エラーがいくつか表示される可能性があります。これらのエラーは重要ではないので、無視しても問題はありません。

# 2. SDAccel を使用したオンプレミスでのデザインの構築

このモジュールでは、次の方法について説明します。
- ローカル コンピューターで SDAccel を実行できることを確認。
- F1 インスタンで後で運用できるバイナリを生成。

GitHub のサンプルを使用して、AWS EC2 インスタンスで使用したのと同じコマンドセットを実行できます。

## GUI の起動
SDAccel の GUI を起動するには、次のコマンドを入力します。
```bash
sdx
```
GUI が問題なく開いたことを確認したら、GUI を閉じます。

## ソフトウェア エミュレーションの実行
次のコマンドを実行し、SDAccel の `helloworld` のソフトウェア エミュレーション ステップを実行します。

```bash
cd $HOME/aws-fpga/SDAccel/examples/xilinx_2018.2/getting_started/host/helloworld_c/
make clean
make check TARGETS=sw_emu DEVICES=$AWS_PLATFORM all
```

## ハードウェア エミュレーションの実行

次のコマンドを実行し、SDAccel の `helloworld` のハードウェア エミュレーション ステップを実行します。

```bash
cd $HOME/aws-fpga/SDAccel/examples/xilinx_2018.2/getting_started/host/helloworld_c/
make clean
make check TARGETS=hw_emu DEVICES=$AWS_PLATFORM all
```

## F1 運用目的で構築

* 次のコマンドを実行し、SDAccel の `helloworld` の FPGA バイナリを構築します。

```bash
cd $HOME/aws-fpga/SDAccel/examples/xilinx_2018.2/getting_started/host/helloworld_c/
make clean
make TARGETS=hw DEVICES=$AWS_PLATFORM all
```

このプロセスで、ホストおよび FPGA のバイナリが生成されます。  
1. ホスト バイナリ: `./helloworld`  
2. FPGA バイナリ: `./xclbin/vector_addition.hw.xilinx_aws-vu9p-f1_4ddr-xpr-2pr_4_0.xclbin`

次のいずれかの方法で、`create_sdaccel_afi.sh` スクリプトを使用して `.xclbin` ファイルから Amazon FPGA イメージ (AFI) を作成します。
* コンピューターに AWS CLI をインストールした後、イメージをローカルに作成します。
* AWS インスタンスに `.xclbin` をアップロードし、そこで `create_sdaccel_afi.sh script` を実行します。

このチュートリアルでは、AWS EC2 インスタンスに何もかもアップロードします。


# 3. アップロードして F1 で実行

このセクションでは、次のステップについて説明します。
 - FPGA バイナリ (オンプレミスで構築) を AWS クラウドにアップロード
 - `.xclbin` ファイルから AFI を作成
 - F1 インスタンスでホスト プログラムをコンパイル
 - F1 インスタンスでアクセラレートされたアプリケーションを実行

## FPGA バイナリおよびホスト プログラムを AWS クラウドにアップロード

1. 必要なファイルが含まれた TAR ファイルを作成します。
```bash
tar cvfz helloworld.tgz Makefile src xclbin/vector_addition.hw.xilinx_aws-vu9p-f1_4ddr-xpr-2pr_4_0.xclbin
```
2. AWS F1 インスタンスを起動します。
3. AWS F1 インスタンスに先ほど作成した TAR ファイルをアップロードします。
```bash
scp -i ~/<AWS key pairs>.pem <xclbin file> centos@<public IP address of EC2 instance>:/home/centos/.
```
>**注記**: または AWS S3 バケットを使用してファイルを転送することもできます。

## Amazon FPGA イメージの作成

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
4. AWS FPGA バイナリと AFI を `*.xclbin` から作成します。
```bash
cd $HOME/helloworld/xclbin
$SDACCEL_DIR/tools/create_sdaccel_afi.sh \
    -xclbin=<xclbin file name>.xclbin \
    -s3_bucket=<bucket-name> \
    -s3_dcp_key=<dcp-folder-name> \
    -s3_logs_key=<logs-folder-name>
```

## ホスト アプリケーションのコンパイル
makefile を使用してホスト アプリケーションをコンパイルします。
```bash
cd $HOME/helloworld
make exe
```
これで `helloworld` プログラムが作成されます。

## F1 インスタンスでアクセラレートされたアプリケーションを実行

* AFI のステータスを **available** に変更したら、F1 で実行する準備が整います。
```bash
sudo sh
source /opt/xilinx/xrt/setup.sh   
./helloworld
```

このモジュールでは、オンプレミスで FPGA バイナリを開発し、AFI を作成し、AWS F1 インスタンスでアクセラレートされたアプリケーションを実行する方法を説明しました。

<hr/>
<p align="center"><b>
<a href="README.md">入門ガイドに戻る</a>
</b></p>
<br>
<hr/>
<p align="center"><sup>Copyright&copy; 2019-2019 Xilinx</sup></p>
