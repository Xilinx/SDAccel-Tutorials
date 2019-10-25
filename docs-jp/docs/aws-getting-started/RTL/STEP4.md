# RTL カーネルを使用した演習

ここでは、このガイドのこれまでのステップで学んだことを、さらに実践的な例を実行して練習します。まずは SDAccel™ 環境に関連したオンライン リソースにどんなものが用意されているかを知り、[AWS フォーラム](https://forums.aws.amazon.com/forum.jspa?forumID=243)を利用してさらに詳しい情報を検索します。

## その他の例を使用した演習

次の 3 つの RTL カーネル例を実行しながら、さらに RTL カーネル フローについて学びます。

#### 例 1: クロックを 2 つ使用したベクターの加算

この例では、クロックを 2 つと、`--kernel_frequency` XOCC オプションを使用して、RTL カーネルでベクターを加算します。

SDAccel GitHub リポジトリから[この例](https://github.com/Xilinx/SDAccel_Examples/tree/2018.2/getting_started/rtl_kernel/rtl_vadd_2clks) をダウンロードして実行します。



#### 例 2: カーネルを 2 つ使用したベクターの加算

この例では、2 つ以上の RTL カーネルを使用したアクセラレートされたデザインを作成します。2 つのカーネル (Kernel_0 および Kernel_1) を使用して、ベクターを加算します。Kernel_1 には Kernel_0 からの出力が読み込まれます (2 入力が 1 入力として読み込まれる)。

SDAccel GitHub リポジトリから[この例](https://github.com/Xilinx/SDAccel_Examples/tree/2018.2/getting_started/rtl_kernel/rtl_vadd_2kernels)をダウンロードして実行します。

#### 例 3: あらかじめコンパイルされた XO ファイルおよびアドバンスド Vivado™ インプリメンテーション オプションを使用したハイ パフォーマンス行列乗算

この例では、2 入力行列のハイ パフォーマンス行列乗算 (A*B=C) をインプリメントします。行列乗算のカーネルは、int16 型の行列で演算を実行し、int16 型の結果値を出力します。内部的には、カーネルに 2048 個の DSP ユニットがあるシストリック配列があり、2 つの DDR バンクが接続されています。DSP 配列は 400 MHz で実行されますが、この配列周辺のロジックは 300 MHz で実行されます。

SDAccel GitHub リポジトリから[この例](https://github.com/Xilinx/SDAccel_Examples/tree/2018.2/acceleration/high_perf_mat_mult)をダウンロードして実行します。


## サポートを利用して問題をトラブルシュート

[AWS F1 SDAccel 開発フォーラム](https://forums.aws.amazon.com/forum.jspa?forumID=243)は、質問をし合ったり、知識を共有したり、サポートを得るための場です。[Available Actions] セクションにある **[Watch Forum]** リンクをクリックして、このフォーラムを購読するようにしてください。



## SDAccel についてさらに学ぶ

#### SDAccel QuickTake ビデオ チュートリアル

* [アプリケーション ホスト コードの基本概念](https://japan.xilinx.com/video/hardware/concepts-of-application-host-code.html)

* [SDAccel RTL カーネル ウィザードの概要](https://www.youtube.com/watch?v=IZQ1A2lPXZk)

#### SDAccel v2018.3 の資料

* 『SDx 環境リリース ノート、インストール、およびライセンス ガイド』 ([UG1238](https://japan.xilinx.com/cgi-bin/docs/rdoc?v=replace;d=ug1238-sdx-rnil.pdf))
* 『SDAccel 環境ユーザー ガイド』 ([UG1023](https://japan.xilinx.com/cgi-bin/docs/rdoc?v=replace;d=ug1023-sdaccel-user-guide.pdf))
* 『SDAccel 環境プロファイリングおよび最適化ガイド』 ([UG1207](https://japan.xilinx.com/cgi-bin/docs/rdoc?v=replace;d=ug1207-sdaccel-optimization-guide.pdf))
* 『SDAccel 設計手法ガイド』 ([UG1021](https://japan.xilinx.com/support/documentation/sw_manuals/xilinx2019_1/ug1346-sdaccel-methodology-guide.pdf))

<hr/>
<p align="center"><b>
<a href="STEP5.md">次へ: コンピューターに SDAccel をインストールして実行</a>
</b></p>
<br>
<hr/>
<p align="center"><sup>Copyright&copy; 2019-2019 Xilinx</sup></p>
