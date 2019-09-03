<p align="right">
<a href="../../../docs/Pathway3/README.md">English</a> | <a>日本語</a>
</p>
<table>
 <tr>
   <td align="center"><img src="https://japan.xilinx.com/content/dam/xilinx/imgs/press/media-kits/corporate/xilinx-logo.png" width="30%"/><h1>2019.1 SDAccel™ 開発環境チュートリアル</h1>
   <a href="https://github.com/Xilinx/SDAccel-Tutorials/branches/all">その他のバージョンを表示</a>
   </td>
 </tr>
 <tr>
 <td align="center"><h1>アクセラレーションされたアプリケーションのビルドおよび実行の基本的な概念</h1>
 </td>
 </tr>
</table>

## 概要

次の演習では、SDAccel™ 開発環境を使用してアクセラレーションされたアプリケーションをビルドして実行するための基本的な概念を説明します。

## 開始前の注意点

このチュートリアルでは、次を使用します。

* BASH Linux シェル コマンド
* 2019.1 SDx™ リリースおよび xilinx\_u200\_xdma\_201830\_1 プラットフォーム。必要であれば、その他のソフトウェア リリースおよびプラットフォームを使用するように変更できます。

> **重要:**
>
> * サンプル ファイルを実行する前に、『SDAccel 開発環境リリース ノート、インストール、およびライセンス ガイド』 ([UG1238](https://japan.xilinx.com/html_docs/xilinx2019_1/sdaccel_doc/yrc1534452173645.html)) で説明されるように、ザイリンクス ランタイム (XRT) と SDAccel 開発環境ツールをインストールしてください。
> * Alveo™ カードでアプリケーションを実行する場合は、『Alveo データセンター アクセラレータ カード入門ガイド ([UG1301](https://japan.xilinx.com/support/documentation/boards_and_kits/accelerator-cards/ug1301-getting-started-guide-alveo-accelerator-cards.pdf)) で説明されるように、カードとソフトウェア ドライバーを正しくインストールしてください。

## チュートリアル リファレンス ファイルの入手

1. リファレンス ファイルを入手するには、ターミナルに `git clone https://github.com/Xilinx/SDAccel-Tutorials` と入力します。
2. `SDAccel-Tutorials-master/docs/<tutorial name>/reference-files` に移動します。

## 次のステップ

演習は、次の順序で実行します。

1. [アプリケーションのビルド](./BuildingAnApplication.md): アプリケーションのホスト プログラムおよびハードウェア カーネルをビルドする方法を説明します。
2. [ソフトウェアおよびハードウェア エミュレーションの実行](./Emulation.md): アプリケーションでハードウェアおよびソフトウェア エミュレーションを実行します。
3. [プロファイルおよびトレース レポートの生成](./ProfileAndTraceReports.md): アプリケーションのパフォーマンスをより理解するためのプロファイリング レポートの生成方法について説明します。
4. [ハードウェアでの実行](./HardwareExec.md): 最後に、Alveo アクセラレータ カードでアプリケーションを実行します。

</br>
<hr/>
<p align= center><b><a href="../../README.md">メイン ページに戻る</a> — <a href="../sdaccel-getting-started/README.md">入門コースの初めに戻る</a></b></p>
</br>
<p align="center"><sup>Copyright&copy; 2019 Xilinx</sup></p>
