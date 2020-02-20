<p align="right">
<a href=https://github.com/Xilinx/SDAccel-Tutorials/blob/master/docs/aws-getting-started/PREREQUISITES/README.md">English</a> | <a>日本語</a>
</p>

# AWS F1 で SDAccel を使用するための必須条件

最初に AWS F1 インスタンスに接続する前に、このガイドに示す手順をすべて実行する必要があります。

## 1\. AWS アカウントの作成

アマゾン ウェブ サービス (AWS) のアカウントを既に持っていない場合は、[https://aws.amazon.com/](https://aws.amazon.com) で作成してください。

このプロセスでは、特殊なアクセス キー (AWS アクセス キー ID および AWS 秘密アクセス キー) を生成します。これらのキーは後で AWS コマンド ライン インターフェイス (CLI) を設定するために必要となりますので、記録しておいてください。

## 2\. リージョンの選択

AWS F1 インスタンスは現時点で特定のリージョン (US East (N.Virginia)、US West (Oregon)、または EU (Ireland)) でしか使用できません。AWS Management Console でサポートされるリージョンを選択する必要があります。

* AWS Management Console にログインします ([https://console.aws.amazon.com](https://console.aws.amazon.com))。
* コンソールの右上にあるドロップダウンからリージョンを選択します。

## 3\. S3 バケットを作成

Amazon FPGA Image (AFI) 作成サービスを実行するには、Amazon S3 バケットを作成する必要があります。バケットには、Amazon FPGA Image (AFI) 作成サービスから生成される TAR ファイルとログ ファイルが含まれるようになります。

この手順では、AWS Management Console を使用して Amazon S3 バケットを作成します。バケット名はグローバルに重複しない名前にする必要があります。バケット名が既に存在している場合はエラーになるので、別の数字や文字を使用して、まだ使われていない名前を見つけてください。

* AWS Management Console に移動します ([https://console.aws.amazon.com](https://console.aws.amazon.com))。
* AWS Management Console で \[Services] を選択し、\[Storage] で \[S3] を選択します。
* \[+ Create Bucket] を選択します。
* 「<yourname>afibucket」など、ほかと重複しないバケット名を付けます。バケット名が既に存在している場合はエラーになるので、まだ使われていない名前が見つかるまで試してください。
* F1 を使用するのに選択したリージョンを選択します (US East (N.Virginia)、US West (Oregon)、または EU (Ireland))。
* 設定をコピーするバケットを選択せず、ダイアログ ボックスの左下にある \[Create] を選択します。

バケットを作成したら、SDAccel で生成されたデザイン チェックポイント (DCP) とログ ファイルを保存する 2 つのフォルダーを作成します。

* S3 コンソールで新しく作成したバケットの名前をクリックして開きます。
* \[+ Create folder] を選択します。
* デザイン チェックポイント (DCP) を保存するために使用するフォルダーの名前を指定します。
* \[Save] をクリックします。
* \[+ Create folder] を選択します。
* SDAccel ログ ファイルを保存するために使用するフォルダーの名前を指定します。
* \[Save] をクリックします。

## 4\.秘密接続キーを準備

秘密キーは、インスタンスへの接続に必要です。秘密キーは、最初の手順で生成したアクセス キーとは異なるものです。前もって作成しておくことをお勧めします。

* EC2 Management Console を開きます ([console.aws.amazon.com/ec2](console.aws.amazon.com/ec2))。
* 左側のペインで、\[NETWORK \& SECURITY] メニューから \[Key Pairs] を選択します。
* \[Create Key Pair] ボタンをクリックします。
* キー ペアを指定して、\[Create] をクリックします。PEM ファイルが自動的にダウンロードされます。
* ターミナルを開き、先ほどダウンロードしたファイルのあるディレクトリに移動します。
* **chmod** コマンドを使用して、キー ファイルが一般に表示されないようにします。

```
chmod 400 <my-key-pair.pem>
```

プライベート キーが正しいフォーマットになり、SSH で使用して AWS EC2 インスタンスに接続できるようになりました。

### Windows の場合

* PuTTY がコンピューターに既にインストールされていない場合は、ダウンロードしてインストールします ([http://www.putty.org/](http://www.putty.org/))。
* EC2 Management Console を開きます ([console.aws.amazon.com/ec2](console.aws.amazon.com/ec2))。
* 左側のペインで、\[NETWORK \& SECURITY] メニューから \[Key Pairs] を選択します。
* \[Create Key Pair] ボタンをクリックします。
* キー ペアを指定して、\[Create] をクリックします。PEM ファイルが自動的にダウンロードされます。
* PuTTYgen を起動します (\[スタート] → \[すべてのプログラム] → \[PuTTY] → \[PuTTYgen] をクリックします)。
* \[Type of key to generate] の下で \[RSA] を選択します。
* \[Load] を選択します。デフォルトでは、PuTTYgen には拡張子 PPK のファイルのみが表示されます。自分の PEM ファイルを表示するには、すべてのファイル形式を表示するオプションを選択してください。
* インスタンスを起動したときに指定したキー ペアの PEM ファイルを選択し、\[Open] をクリックします。\[OK] をクリックしてダイアログ ボックスを閉じます。
* PuTTY が使用できるフォーマットでキーを保存するため、\[Save private key] をクリックします。PuTTYgen に、パスフレーズなしでキーを保存することに対する警告メッセージが表示されます。\[Yes] をクリックします。

プライベート キーが正しいフォーマットになり、PuTTY で使用して AWS EC2 インスタンスに接続できるようになりました。

## 5\.AWS F1 インスタンスへのアクセス リクエスト

デフォルトでは、AWS ユーザーは F1 インスタンスへアクセスできません。F1 インスタンスは、使用する前にアクセスを請求して承認される必要があります。

* \[Service Limit Increase] フォームを開きます ([http://aws.amazon.com/contact-us/ec2-request](http://aws.amazon.com/contact-us/ec2-request))。
* アカウント名が正しいことを確認します。
* 「EC2 インスタンス」用に \[Service Limit Increase] を提出します。
* F1 インスタンスにアクセスする必要のあるリージョンを選択します (US East (N.Virginia)、US West (Oregon)、または EU (Ireland))。
* プライマリ インスタンス タイプに \[f1.2xlarge] または \[f1.16xlarge] を選択します。
* \[New limit value] を 1 以上に設定します。
* フォームの残りの箇所を記入して、\[Submit] をクリックします。

AWS でのリクエストの処理には 24 時間から 48 時間かかります。<br>

<hr/>
<p align="center"><sup>Copyright&copy; 2019 Xilinx</sup></p>


この資料は表記のバージョンの英語版を翻訳したもので、内容に相違が生じる場合には原文を優先します。資料によっては英語版の更新に対応していないものがあります。日本語版は参考用としてご使用の上、最新情報につきましては、必ず最新英語版をご参照ください。
