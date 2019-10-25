# AWS F1 で SDAccel を使用するための条件

最初に AWS F1 インスタンスに接続する前に、このガイドに示す手順をすべて実行する必要があります。

## 1. AWS アカウントを作成
アマゾン ウェブ サービス (AWS) アカウントをお持ちでない場合は、[https://aws.amazon.com/](https://aws.amazon.com) から作成します。

このプロセスでは、特殊なアクセス キー (AWS アクセス キー ID および AWS 秘密アクセス キー) を生成します。これらのキーは後で AWS コマンド ライン インターフェイス (CLI) を設定するために必要となりますので、記録しておいてください。

## 2. リージョンを選択
AWS F1 インスタンスは現時点で特定のリージョン (US East (N.Virginia)、US West (Oregon)、または EU (Ireland)) でしか使用できません。AWS Management Console でサポートされるリージョンを選択する必要があります。

* AWS Management Console: [https://console.aws.amazon.com](https://console.aws.amazon.com) にログインします。
* コンソール右上のドロップダウンからリージョンを選択します。

## 3. S3 バケットを作成

Amazon FPGA Image (AFI) 作成サービスを実行するには、Amazon S3 バケットを作成する必要があります。バケットには、Amazon FPGA Image (AFI) 作成サービスから生成される TAR ファイルとログ ファイルが含まれるようになります。

この手順では、AWS Management Console を使用して Amazon S3 バケットを作成します。バケット名はグローバルに一意でる必要があることに注意してください。バケット名が既に存在している場合はエラーになるので、別の数字や文字を使用して、まだ使われていない名前を見つけてください。

* AWS Management Console [https://console.aws.amazon.com](https://console.aws.amazon.com) まで移動します。
* AWS Management Console で [Services] をクリックし、[Storage] の下から S3 を選択します。
* **[+ Create Bucket]** を選択します。
* **<yourname>afibucket** のように、グローバルに一意な名前をバケットに付けます。バケット名が既に存在している場合はエラーになるので、まだ使われていない名前が見つかるまで試してください
F1 使用で選択したリージョン (US East (N.Virginia)、US West (Oregon) または EU (Ireland)) を選択します。
* ダイアログ ボックスの左下の **[Create]** を選択します。設定をコピーするバケットは選択しません。

バケットを作成したら、SDAccel で生成されたデザイン チェックポイント (DCP) とログ ファイルを格納する 2 つのフォルダーを作成します。

* S3 コンソールで新しく作成したバケットの名前をクリックして開きます。
* **[+ Create folder]** を選択します。
デザイン チェックポイント (DCP) を格納するフォルダーの名前を指定します。
**[Save]** をクリックします。
* **[+ Create folder]** を選択します。
SDAccel のログ ファイルを格納するフォルダーの名前を指定します。
**[Save]** をクリックします。

## 4. 秘密接続キーを準備
秘密キーは、インスタンスへの接続に必要となります。秘密キーは、最初の手順で生成したアクセス キーとは異なるものです。前もって作成しておくことをお勧めします。

* EC2 Management Console: [console.aws.amazon.com/ec2](console.aws.amazon.com/ec2) を開きます。
* 左画面の **[NETWORK & SECURITY]** メニューから **[Key Pairs]** を選択します。
* **[Create Key Pair]** ボタンを選択します。
* キー ペアに名前を付けて **[Create]** をクリックします。.pem ファイルが自動的にダウンロードされます。
* ターミナルを開いて、ダウンロードした .pem のディレクトリに変更します。
* **chmod** コマンドで、キーファイルが公開表示されないようにします。
```
chmod 400 <my-key-pair.pem>
```

プライベート キーが正しいフォーマットになり、SSH で使用して AWS EC2 インスタンスに接続できるようになりました。

### Windows の場合

* まだマシンにインストールしていない場合は PuTTY: [http://www.putty.org/](http://www.putty.org/) をダウンロードしてインストールします。
* EC2 Management Console: [console.aws.amazon.com/ec2](console.aws.amazon.com/ec2) を開きます。
* 左画面の **[NETWORK & SECURITY]** メニューから **[Key Pairs]** を選択します。
* **[Create Key Pair]** ボタンを選択します。
* キー ペアに名前を付けて **[Create]** をクリックします。.pem ファイルが自動的にダウンロードされます。
* PuTTYgen を開始します (たとえば、**[スタート]** → **[すべてのプログラム]** → **[PuTTY]** → **[PuTTYgen]** をクリック)。
* **[Type of key to generate]** から **[RSA]** を選択します。
* **[Load]** をクリックします。デフォルトでは、PuTTYgen には拡張子 .ppk のファイルのみが表示されます。ご自分の .pem ファイルを表示するには、すべてのファイル形式を表示するオプションを選択してください。
* インスタンスを開始したときに指定したキー ペアを含む .pem ファイルを選択して、**[Open]** をクリックします。**[OK]** をクリックしてダイアログ ボックスを閉じます。
* **[Save private key]** を選択して PuTTY が使用できるフォーマットでキーを保存します。PuTTYgen に、パスフレーズなしでキーを保存することを示す警告メッセージが表示されます。**[Yes]** をクリックします。

プライベート キーが正しいフォーマットになり、PuTTY で使用して AWS EC2 インスタンスに接続できるようになりました。

## 5. AWS F1 インスタンスへのアクセスをリクエスト

デフォルトでは、AWS ユーザーは F1 インスタンスへアクセスできません。F1 インスタンスは、使用する前にアクセスを請求して承認される必要があります。

* Service Limit Increase フォーム: [http://aws.amazon.com/contact-us/ec2-request](http://aws.amazon.com/contact-us/ec2-request) を開きます。
* アカウント名が正しいかどうかを確認してください。
* **[EC2 Instances]** の **[Service Limit Increase]** を提出します。
F1 インスタンスにアクセスするリージョン (US East (N.Virginia)、US West (Oregon)、または EU (Ireland)) を選択します。
* プライマリ インスタンス タイプに **f1.2xlarge** または **f1.16xlarge** を選択します。
* **[New limit value]** を 1 またはそれ以上に設定します。
* フォームの残りを適宜記載して、**[Submit]** をクリックします。

AWS のプロセスには、通常 24 ～ 48 時間かかります。
<br>
<hr/>
<p align="center"><sup>Copyright&copy; 2019-2019 Xilinx</sup></p>
