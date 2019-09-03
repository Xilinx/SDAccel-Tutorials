<p align="right">
<a href="../../../docs/convolution-tutorial/README.md">English</a> | <a>日本語</a>
</p>
<table>
 <tr>
   <td align="center"><img src="https://japan.xilinx.com/content/dam/xilinx/imgs/press/media-kits/corporate/xilinx-logo.png" width="30%"/><h1>2019.1 SDAccel™ 開発環境チュートリアル</h1>
   <a href="https://github.com/Xilinx/SDAccel-Tutorials/branches/all">その他のバージョンを表示</a>
   </td>
 </tr>
 <tr>
 <td align="center"><h1>アクセラレーションされた FPGA アプリケーションの最適化手法</td>
 </tr>
</table>

# 概要

最適化したアクセラレーション アプリケーションを開発する手法には、アプリケーションの構築と必要なパフォーマンス目標を達成するアクセラレータの開発の 2 つの主な段階があります。

* 最初の段階では、どのソフトウェア関数を FPGA カーネルでアクセラレーションするか、どれくらい並列処理が達成可能か、どのようにコードで記述するかなど、アプリケーション アーキテクチャに関する重要事項を決定します。
* 次の段階では、ソース コードを構築し、必要なコンパイラ オプションとプラグマを適用して、パフォーマンス ターゲットを達成するのに必要なカーネル アーキテクチャを作成して、カーネルをインプリメントします。

基準アプリケーションを使用してこのチュートリアルを開始し、プロファイルしてハードウェア アクセラレーションの可能性を検証します。チュートリアルのアプリケーションは、多くの音声/画像形式を再生し、コードを変換し、多重化および分離し、フィルターできるマルチメディア フレームワークの ffmpeg を使用して、RGBA ビデオの 2D たたみ込みとフィルター係数をのセットを実行します。この後、ホスト プログラムおよびカーネル側の両方でさまざまな最適化を実行します。このチュートリアルでは、次の最適化手法を使用します。

* メモリ転送の最適化
* 固定小数点データ型の適用
* データフローおよびストリーム
* ループの最適化

CPU ベースのアプリケーションを最適化した FPGA アクセラレーションされたデザインに移行する方法については、『SDAccel 設計手法ガイド』 ([UG1346](https://japan.xilinx.com/support/documentation/sw_manuals/xilinx2019_1/ug1346-sdaccel-methodology-guide.pdf)) を参照してください。このチュートリアルを実行する際は、この資料も確認することでより理解が深まります。

# 開始前の注意点

このチュートリアルでは、手順を実行するマシンに ffmpeg フレームワークがインストールされている必要があります。これらの依存ファイルは、次のコマンドを実行するとダウンロードできます。

* CentOS の場合:

  ```
  sudo yum localinstall --nogpgcheck https://download1.rpmfusion.org/free/el/rpmfusion-free-release-7.noarch.rpm
  sudo yum install ffmpeg
  ```

* Ubuntu の場合:

  ```
  sudo apt update
  sudo apt install ffmpeg
  ffmpeg -version
  ```

このチュートリアルでは、次を使用します。

* BASH Linux シェル コマンド
* 2019.1 SDx リリースおよび xilinx\_u200\_xdma\_201830\_1 プラットフォーム。必要であれば、その他のバージョンおよびプラットフォームも使用できます。
* 多くの手順および変数を含む `Makefile`。`Makefile` の構造および内容については、[makefile の理解](./HowToRunTutorial.md)を参照してください。

> **重要:**
>
> * サンプル ファイルを実行する前に、『SDAccel 開発環境リリース ノート、インストール、およびライセンス ガイド』 ([UG1238](https://japan.xilinx.com/html_docs/xilinx2019_1/sdaccel_doc/yrc1534452173645.html)) で説明されるように、ザイリンクス ランタイム (XRT) と SDAccel 開発環境ツールをインストールしてください。
> * Alveo™ カードでアプリケーションを実行する場合は、『Alveo データセンター アクセラレータ カード入門ガイド ([UG1301](https://japan.xilinx.com/support/documentation/boards_and_kits/accelerator-cards/ug1301-getting-started-guide-alveo-accelerator-cards.pdf)) で説明されるように、カードとソフトウェア ドライバーを正しくインストールしてください。

## チュートリアル リファレンス ファイルの入手

1. リファレンス ファイルを入手するには、ターミナルに `git clone http://github.com/Xilinx/SDAccel-Tutorials` と入力します。
2. `SDAccel-Tutorials-master/docs/convolution-tutorial` に移動します。

# チュートリアルの概要

次の演習では、既存のアプリケーションを FPGA アクセラレーションされたアプリケーションとして最適化するためのベスト プラクティスを説明します。このチュートリアルでは、複数の演習に分けて手法をお見せします。演習は、次の順序で実行することをお勧めします。

1. [元のアプリケーションの評価](RunOriginalCode.md): この演習では、元の C ベースのアプリケーションが入力ビデオを処理してたたみ込み出力ビデオを生成するところをお見せします。この演習では、アクセラレーションされたアプリケーションの現実的なパフォーマンス目標設定についても説明します。
2. [C アプリケーションからの SDAccel アプリケーションの作成](baseline.md): 元の C コードをホスト プログラムおよびハードウェア カーネルに変換し、OpenCL™ API を使用してホストから呼び出されるようにします。
3. [メモリ転送の最適化](localbuf.md): メモリ アクセスを改善するためのハードウェア カーネルの最適化方法を学びます。ローカル キャッシュを使用して効率的に FPGA 帯域幅を使用できるようにする方法を学びます。
4. [固定小数点データ型を使用した最適化](fixedtype.md): データ型のデザイン パフォーマンスに与える影響について説明します。
5. [データフローを使用した最適化](dataflow.md): データフローおよびストリーミングを適用してデータパスを改善することで、カーネルの計算効率を改善します。
6. [順不同キューと複数の計算ユニットの使用](multi-CU.md): ホスト プログラムの OpenCL API 呼び出しを順不同のタスク実行ができるように変更し、作業を実行する複数カーネルを合成してアクセラレータの並列処理を増加します。
7. [ハードウェアでのアクセラレータの実行](RunOnHardware.md): 前の手順はすべてハードウェア エミュレーション モードで実行されました。ここでは、アクセラレーション ハードウェアでアプリケーションを実行します。

</br>
<hr/>
<p align= center><b><a href="../../README.md">メイン ページに戻る</a> — <a href="../sdaccel-getting-started/README.md">入門コースの初めに戻る</a></b></p>
</br>
<p align="center"><sup>Copyright&copy; 2019 Xilinx</sup></p>
