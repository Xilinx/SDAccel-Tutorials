<p align="right">
<a href="../../../docs/alveo-getting-started/README.md">English</a> | <a>日本語</a>
</p>
<table>
 <tr>
   <td align="center"><img src="https://japan.xilinx.com/content/dam/xilinx/imgs/press/media-kits/corporate/xilinx-logo.png" width="30%"/><h1>2019.1 SDAccel™ 開発環境チュートリアル</h1>
   <a href="https://github.com/Xilinx/SDAccel-Tutorials/branches/all">その他のバージョンを表示</a>
   </td>
 </tr>
 <tr>
 <td align="center"><h1>SDAccel 入門コース</h1>
 </td>
 </tr>
</table>

# Alveo 入門

## 概要

このチュートリアルは、Alveo™ データセンター アクセラレータ カードに精通するため、およびインストールを実行するために使用可能なさまざまなリソースを紹介します。

サーバーに Alveo カードをインストールする方法は、[Alveo ページ](http://japan.xilinx.com/alveo)を参照してください。このページには、Alveo カードについて精通するために必要なリソースすべて、および RedHat/CentOS と Ubuntu オペレーティング システムへのインストール方法のリンクが含まれます。

サーバーにインストールされた Alveo カードを使用すると、ホスト サーバーおよびアクセラレータ カードで実行されるようにアプリケーションを運用できますが、  アプリケーションを開発するには、SDAccel 開発ソフトウェアをインストールする必要があります。  SDAccel ツールには、ホスト アプリケーション用に最適化されたコンパイラ、ハードウェア カーネルのクロス コンパイラ、堅固なデバッグ環境、パフォーマンスのボトルネックを見つけてアプリケーションを最適化するプロファイラーが含まれます。SDAccel 開発ソフトウェアは、Alveo カードと同じサーバーにインストールする必要はなく、  カードがインストールされていないシステムにもインストールできます。

そのほか、[AWS](https://aws.amazon.com/ec2/instance-types/f1/) または [Nimbix](https://www.nimbix.net/alveo/) などのクラウド サーバーでアプリケーションを開発および運用することもできます。どちらにもハードウェア アクセラレーション コードを開発、シミュレーション、デバッグ、およびコンパイルするために必要なものがすべて含まれています。

## Alveo カードのインストール

Alveo カードをインストールする前に、ハードウェアおよびドライバー ソフトウェア インストールの概要と、Alveo ページを簡単に説明する [QuickTake ビデオ](https://japan.xilinx.com/video/fpga/getting-started-with-alveo-u200-u250.html)を参照してください。

Alveo カードのインストールは、『Alveo データセンター アクセラレータ カード入門』 ([UG1301](https://japan.xilinx.com/support/documentation/boards_and_kits/accelerator-cards/j_ug1301-getting-started-guide-alveo-accelerator-cards.pdf)) の手順に従ってください。これには、最新のハードウェアおよびドライバー ソフトウェアのインストール手順が含まれ、Alveo カードを使用してハードウェア アクセラレーションされたアプリケーションを運用できるようになります。また、次も含まれます。

* 最低限のシステム要件
* 検証されたサーバーのリスト
* Alveo カードの概要
* カードの立ち上げおよび検証ステップ

アプリケーションを社内開発する場合は、『SDAccel 開発環境リリース ノート、インストール、およびライセンス ガイド』 ([UG1238](https://japan.xilinx.com/html_docs/xilinx2019_1/sdaccel_doc/yrc1534452173645.html)) の詳細な手順に従って SDAccel 環境ソフトウェアをインストールしてください。

AWS 用のアプリケーションを開発するには、次のチュートリアルを参照してください。

* [SDAccel および C/C++ を使用した AWS F1 入門](../../../docs-jp/docs/aws-getting-started/CPP)
* [SDAccel および RTL カーネルを使用した AWS F1 入門](../../../docs-jp/docs/aws-getting-started/RTL)

## 次のステップ

SDAccel™ 開発環境の使用して、ソフトウェア アプリケーションのビルド、ハードウェア プラットフォームのビルド、エミュレーションの実行、レポートの表示、ハードウェアでの実行などの[アクセラレーションされたアプリケーションをビルドおよび実行の基本的な概念](../../../docs/Pathway3/README.md)を理解します。

</br>
<hr/>
<p align= center><b><a href="../../README.md">メイン ページに戻る</a> — <a href="../sdaccel-getting-started/README.md">入門コースの初めに戻る</a></b></p>
</br>
<p align="center"><sup>Copyright&copy; 2019 Xilinx</sup></p>

この資料は表記のバージョンの英語版を翻訳したもので、内容に相違が生じる場合には原文を優先します。資料によっては英語版の更新に対応していないものがあります。日本語版は参考用としてご使用の上、最新情報につきましては、必ず最新英語版をご参照ください。

