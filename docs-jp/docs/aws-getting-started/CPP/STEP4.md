# C/C++ および OpenCL カーネルを使用した演習

ここでは、このガイドのこれまでのステップで学んだことを、さらに実践的な例を実行して練習します。まずは SDAccel™ 環境に関連したオンライン リソースにどんなものが用意されているかを知り、[AWS フォーラム](https://forums.aws.amazon.com/forum.jspa?forumID=243)を利用してさらに詳しい情報を検索します。

## オンデマンド SDAccel AWS F1 開発者向け演習

ザイリンクス SDAccel 開発環境を使用して、アクセラレートされたアプリケーションを開発する方法を学びます。

[**演習を始める**](https://github.com/Xilinx/SDAccel-AWS-F1-Developer-Labs)


## その他の例を使用した演習

C/OpenCL™ カーネルの 3 つの例を使用した演習では、適切なコード記述方法やカーネル最適化のテクニックをさらに詳しく学ぶことができます。

### ループのパイプライン処理

この例では、カーネルのパフォーマンスを改善する目的で、ループのパイプライン処理を使用する方法を学びます。

SDAccel GitHub リポジトリから[この例](https://github.com/Xilinx/SDAccel_Examples/tree/master/getting_started/kernel_opt/loop_pipeline_c)をダウンロードして実行します。

### ループの再順序付け

この例では、カーネルのパフォーマンスを改善する目的で、ループの再順序付けを使用する方法を学びます。

SDAccel GitHub リポジトリから[この例](https://github.com/Xilinx/SDAccel_Examples/tree/master/getting_started/kernel_opt/loop_reorder_c)をダウンロードして実行します。

### ループの融合 (ループ フュージョン)

この例では、OpenCL カーネルのパフォーマンスを改善する目的で、2 つのループを 1 つに置き換える方法を学びます。

SDAccel GitHub リポジトリから[この例](https://github.com/Xilinx/SDAccel_Examples/tree/master/getting_started/kernel_opt/loop_fusion_c)をダウンロードして実行します。

### その他の例

ザイリンクスの Github リポジトリには、学習しやすくするための例が 80 以上含まれています。関心のある例を検索するには、リポジトリを参照し、コード記述例や最適化例が多く含まれたものから始めます。

ほかの [C/C++ および OpenCL カーネルのコード記述例および最適化例](https://github.com/Xilinx/SDAccel_Examples/tree/master/getting_started/kernel_opt)を参照してください。


## サポートを利用して問題をトラブルシュート

[AWS F1 SDAccel 開発フォーラム](https://forums.aws.amazon.com/forum.jspa?forumID=243)は、質問し合ったり、知識を共有したり、サポートを得るための場です。\[Available Actions] セクションにある **\[**Watch Forum**]** リンクをクリックして、このフォーラムを購読するようにしてください。

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
