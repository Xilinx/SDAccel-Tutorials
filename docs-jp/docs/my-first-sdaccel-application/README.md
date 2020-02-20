<p align="right">
<a href="../../../docs/my-first-sdaccel-application/README.md">English</a> | <a>日本語</a>
</p>
<table>
 <tr>
   <td align="center"><img src="https://japan.xilinx.com/content/dam/xilinx/imgs/press/media-kits/corporate/xilinx-logo.png" width="30%"/><h1>2019.1 SDAccel™ 開発環境チュートリアル</h1>
   <a href="https://github.com/Xilinx/SDAccel-Tutorials/branches/all">その他のバージョンを表示</a>
   </td>
 </tr>
 <tr>
 <td align="center"><h1>はじめての SDAccel プログラムの作成</h1>
 </td>
 </tr>
</table>

# 概要

SDAccel には、ホスト CPU で実行され、ザイリンクス FPGA で実行される 1 つまたは複数のアクセラレータと交信するソフトウェア プログラムが含まれます。このチュートリアルでは、ホストおよびアクセラレータ用のコードを記述し、SDAccel コンパイラを使用してアプリケーションをビルドしてから、そのアプリケーションを実行してプロファイルする方法について説明します。このチュートリアルで使用されるアクセラレータは、ベクター加算関数です。

このチュートリアルで使用される makefile には、ホストおよびカーネルをコンパイル、リンク、実行するためのコマンド セットが含まれます。makefile からは、結果を表示するプロファイル機能を有効にすることもできます。

# 開始前の注意点

このチュートリアルでは、次を使用します。

* BASH Linux シェル コマンド
* 2019.1 SDx リリースおよび xilinx\_u200\_xdma\_201830\_1 プラットフォーム。必要であれば、その他のバージョンおよびプラットフォームも使用できます。

> **重要:**
>
> * サンプル ファイルを実行する前に、『SDAccel 開発環境リリース ノート、インストール、およびライセンス ガイド』 ([UG1238)](https://japan.xilinx.com/html_docs/xilinx2019_1/sdaccel_doc/yrc1534452173645.html)) で説明されるように、ザイリンクス ランタイム (XRT) と SDAccel 開発環境ツールをインストールしてください。
> * Alveo カードでアプリケーションを実行する場合は、『Alveo データセンター アクセラレータ カード入門ガイド ([UG1301](https://japan.xilinx.com/support/documentation/boards_and_kits/accelerator-cards/ug1301-getting-started-guide-alveo-accelerator-cards.pdf)) で説明されるように、カードとソフトウェア ドライバーを正しくインストールしてください。

## チュートリアル リファレンス ファイルの入手

1. リファレンス ファイルを入手するには、ターミナルに `git clone https://github.com/Xilinx/SDAccel-Tutorials` と入力します。
2. `SDAccel-Tutorials-master/docs/my-first-sdaccel-application/reference-files` に移動します。

# 次のステップ

演習は、次の順序で実行します。

1. [はじめての C++ カーネルの記述](./cpp_kernel.md): アクセラレータで実行するハードウェア カーネルを作成します。
2. [はじめてのホスト プログラムの記述](./host_program.md):カーネルを実行するのに必要な API 呼び出しの作成を含め、単純なホスト プログラム コードを作成します。
3. [アプリケーションのコンパイルおよびリンク](./building_application.md): ホスト プログラムおよびハードウェア カーネルをビルドします。
4. [アプリケーションのプロファイル](./profile_debug.md): アプリケーション デザインをプロファイルおよび最適化します。

</br>
<hr/>
<p align= center><b><a href="../../README.md">メイン ページに戻る</a> — <a href="../sdaccel-getting-started/README.md">入門コースの初めに戻る</a></b></p>
</br>
<p align="center"><sup>Copyright&copy; 2019 Xilinx</sup></p>

この資料は表記のバージョンの英語版を翻訳したもので、内容に相違が生じる場合には原文を優先します。資料によっては英語版の更新に対応していないものがあります。日本語版は参考用としてご使用の上、最新情報につきましては、必ず最新英語版をご参照ください。
