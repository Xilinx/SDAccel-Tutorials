# RTL カーネルを使用した演習

ここでは、このガイドのこれまでのステップで学んだことを、さらに実践的な例を実行して練習します。SDAccel™ 環境に関連したオンライン リソースを利用して学習し、[AWS フォーラム](https://forums.aws.amazon.com/forum.jspa?forumID=243)ではさまざまな疑問点について話し合い、回答を得ることができます。

## その他の例を使用した演習

次の 3 つの RTL カーネル例を実行しながら、さらに RTL カーネル フローについて学びます。

#### 例 1: クロックを 2 つ使用したベクターの加算

この例では、クロック 2 つと、`--kernel_frequency` XOCC オプションを使用した RTL カーネルでベクターを加算します。

SDAccel GitHub リポジトリから[この例](https://github.com/Xilinx/SDAccel_Examples/tree/master/getting_started/rtl_kernel/rtl_vadd_2clks)をダウンロードして実行します。

#### 例 2: カーネルを 2 つ使用したベクターの加算

この例では、2 つ以上の RTL カーネルを使用したアクセラレートされたデザインを作成します。2 つのカーネル (Kernel\_0 および Kernel\_1) を使用して、ベクターを加算します。Kernel\_1 には Kernel\_0 からの出力が読み込まれます (2 入力が 1 入力として読み込まれる)。

SDAccel GitHub リポジトリから[この例](https://github.com/Xilinx/SDAccel_Examples/tree/master/getting_started/rtl_kernel/rtl_vadd_2clks)をダウンロードして実行します。

#### 例 3: 複数の RTL カーネル間のストリーミング接続

この例では、3 つの RTL カーネル間にストリーミング接続を作成します。入力段のカーネルは、グローバル メモリからデータを読み出し、それをカーネル間の AXI-Stream 接続を使用して加算カーネルにストリーミングします。加算カーネルの出力は、出力段のカーネルにストリーミングされ、その結果がグローバル メモリに書き込まれます。

SDAccel GitHub リポジトリから[この例](https://github.com/Xilinx/SDAccel_Examples/tree/master/getting_started/rtl_kernel/rtl_adder_streams)をダウンロードして実行します。

## サポートを利用して問題をトラブルシュート

[AWS F1 SDAccel 開発フォーラム](https://forums.aws.amazon.com/forum.jspa?forumID=243)は質問をすれば回答を得られる場だけでなく、知識を共有し、サポートが得られる場でもあります。\[Available Actions] の \[**Watch Forum**] リンクをクリックして、このフォーラムに必ず参加してください。

## SDAccel 環境についてさらに学ぶ

#### SDAccel v2019.1 の資料

* 『SDx 環境リリース ノート、インストール、およびライセンス ガイド』 ([UG1238](https://japan.xilinx.com/html_docs/xilinx2019_1/sdaccel_doc/gsv1547661552998.html#gsv1547661552998))
* 『SDAccel 環境プログラマ ガイド』 ([UG127](https://japan.xilinx.com/html_docs/xilinx2019_1/sdaccel_doc/vno1533881025717.html))
* 『SDAccel 環境ユーザー ガイド』 ([UG1023](https://japan.xilinx.com/html_docs/xilinx2019_1/sdaccel_doc/itd1534452174535.html))
* 『SDAccel 環境最適化ガイド』 ([UG1207](https://japan.xilinx.com/html_docs/xilinx2019_1/sdaccel_doc/itd1534452174535.html)

<hr/>
<p align="center"><b><a href="STEP5.md">次へ: コンピューターに SDAccel をインストールして実行</a></b></p><br><hr/>
<p align="center"><sup>Copyright&copy; 2019 Xilinx</sup></p>

この資料は表記のバージョンの英語版を翻訳したもので、内容に相違が生じる場合には原文を優先します。資料によっては英語版の更新に対応していないものがあります。日本語版は参考用としてご使用の上、最新情報につきましては、必ず最新英語版をご参照ください。
