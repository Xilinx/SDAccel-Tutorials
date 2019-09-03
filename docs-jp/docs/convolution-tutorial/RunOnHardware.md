<table>
 <tr>
   <td align="center"><img src="https://japan.xilinx.com/content/dam/xilinx/imgs/press/media-kits/corporate/xilinx-logo.png" width="30%"/><h1>2019.1 SDAccel™ 開発環境チュートリアル</h1>
   <a href="https://github.com/Xilinx/SDAccel-Tutorials/branches/all">その他のバージョンを表示</a>
   </td>
 </tr>
 <tr>
 <td align="center"><h1>アクセラレーションされた FPGA アプリケーションの最適化手法
 </td>
 </tr>
</table>

# 7. ハードウェアでのアクセラレータの実行

ここまでは、前の演習すべての結果をハードウェア エミュレーション モードで実行して、システムをビルドするためのコンパイル時間を減らしつつ、最適化によってどれくらいパフォーマンスが改善できるかを確認してきました。このセクションでは、前の最適化それぞれをビルドして、Alveo アクセラレータ カードのハードウェアで実行します。

それぞれの実行の終了後とにタイムライン トレース レポートからのパフォーマンス データを記録し、セクションの終わりの表に数値を埋めていきます。数値は、場合によって異なることもあります。  
次のデータを記録してください。

* **データ合計**: (フレーム数 x フレーム サイズ) で計算。
* **合計時間**: ハードウェアのタイムライン トレース レポートで測定。公正に比較するため、これにはデータ転送とカーネル実行時間も含まれます。
* **スループット**: 処理されたデータ合計 (MB)/合計時間 (s) で計算。

> **重要:**: この演習の手順では、それぞれハードウェア カーネルをコンパイルするので、終了するのにかなり時間がかかる可能性があります。

## ベースライン アプリケーションのハードウェアでの実行

次のコマンドを使用して、ハードウェアで実行し、タイムライン トレース レポートを生成して表示します。

```
make run TARGET=hw STEP=baseline SOLUTION=1
make gen_report TARGET=hw STEP=baseline
make view_timeline_trace TARGET=hw STEP=baseline
```

次の図のようなタイムライン トレース レポートが表示されるはずです。

![][baseline_hw_timeline]

2 つのマーカーは実行の開始点と終了点を記録するので、実行時間は大体 761.52-1.86 = 759.66s と計算されます。

処理された合計は 1920 x 1080 x 4(Bytes) x 132(frames) = 1095 MB です。このため、スループットは 1095(MB)/759(s)= 1.44 MB/s と計算されます。この数値をベンチマーク基準として使用して、今後の最適化を測定していきます。

## メモリ転送演習のハードウェアでの実行

次のコマンドを使用して、ハードウェアで実行し、タイムライン トレース レポートを生成して表示します。

```
make run TARGET=hw STEP=localbuf SOLUTION=1
make gen_report TARGET=hw STEP=localbuf
make view_timeline_trace TARGET=hw STEP=localbuf
```

ハードウェア実行のタイムライン トレース レポートが次の図のように表示されるはずです。

![][localbuf_hw_timeline]

2 つのマーカーは実行の開始点と終了点を記録するので、実行時間は大体 86.56-1.57 = 84.99s と計算されます。

## 固定小数点演習のハードウェアでの実行

次のコマンドを使用して、ハードウェアで実行し、タイムライン トレースを生成して表示します。

```
make run TARGET=hw STEP=fixedpoint SOLUTION=1
make gen_report TARGET=hw STEP=fixedpoint
make view_timeline_trace TARGET=hw STEP=fixedpoint
```

ハードウェア実行のタイムライン トレース レポートが次の図のように表示されるはずです。

![][fixedtype_hw_timeline]

2 つのマーカーは実行の開始点と終了点を記録するので、実行時間は大体 27.12-1.6 = 25.52s と計算されます。

## データフロー演習のハードウェアでの実行

次のコマンドを使用して、ハードウェアで実行し、タイムライン トレースを生成して表示します。

```
make run TARGET=hw STEP=dataflow SOLUTION=1
make gen_report TARGET=hw STEP=dataflow
make view_timeline_trace TARGET=hw STEP=dataflow
```

ハードウェア実行のタイムライン トレース レポートが次の図のように表示されるはずです。

![][dataflow_hw_timeline]

2 つのマーカーは実行の開始点と終了点を記録するので、実行時間は大体 4.66-0.26 = 4.4s と計算されます。

### 複数の計算ユニット演習のハードウェアでの実行

次のコマンドを使用して、ハードウェアで実行し、タイムライン トレースを生成して表示します。

```
make run TARGET=hw STEP=multicu SOLUTION=1
make gen_report TARGET=hw STEP=multicu
make view_timeline_trace TARGET=hw STEP=multicu
```

ハードウェア実行のタイムライン トレース レポートが次の図のように表示されるはずです。

![][hostopt_hw_timeline]

2 つのマーカーは実行の開始点と終了点を記録するので、実行時間は大体 4.077-1.682 = 2.395s と計算されます。

### パフォーマンス表

最終的なパフォーマンスのベンチマーク表は、次のようになります。

| 演習名| Image Size| Number of Frames| Time (Hardware) (s)| Throughput (MBps)
|:----------|:----------|----------:|----------:|----------:
| baseline| 1920x1080| 132| 759.66| 1.44
| localbuf| 1920x1080| 132| 84.99| 12.9 (8.96x)
| fixed-point data| 1920x1080| 132| 25.52| 43 (3.33x)
| dataflow| 1920x1080| 132| 4.4| 249 (5.8x)
| multi-CU| 1920x1080| 132| 2.395| 457 (1.83x)

---------------------------------------


[baseline_hw_timeline]: ./images/191_baseline_hw_timeline_new.JPG "ベースライン バージョンのハードウェアのタイムライン トレース レポート"
[localbuf_hw_timeline]: ./images/191_localbuf_hw_timeline_new.JPG "ローカル バッファー バージョンのハードウェアのタイムライン トレース レポート"
[fixedtype_hw_timeline]: ./images/191_fixedtype_hw_timeline_new.JPG "固定小数点型バージョンのハードウェアのタイムライン トレース レポート"
[dataflow_hw_timeline]: ./images/191_dataflow_hw_timeline_new.JPG "データフロー バージョンのハードウェアのタイムライン トレース レポート"
[hostopt_hw_timeline]: ./images/191_hostopt_hw_timeline_new.JPG "ホスト コード最適化バージョンのハードウェアのタイムライン トレース レポート"
## まとめ

お疲れ様でした！これで、この演習のすべてのモジュールを終了し、標準的な CPU ベースのアプリケーションを FPGA アクセラレーションされたアプリケーションに変換し、Alveo U200 アクセラレータ カードで実行すると、ほぼ 300 倍のスループットで実行できるようになりました。パフォーマンス目標を設定してから、一連の最適化を使用してその目標を達成しました。

1. 基本的な C アプリケーションから SDAccel アプリケーション作成しました。
2. ソフトウェアおよびハードウェア エミュレーション中に生成されるレポートを理解できるようになりました。
3. HLS カーネルのさまざまな最適化方法を確認しました。
4. OpenCL API コマンド キューを順不同で実行するように設定して、パフォーマンスを改善する方法を学びました。
5. カーネルをイネーブルにして、複数 CU で実行するようにしました。
6. HLS データフロー指示子を使用し、それがアプリケーションにどのように影響するかを確認しました。
7. Alveo アクセラレータ カードで最適化したアプリケーションを実行し、実際にパフォーマンスがどれくらい改善したかを確認しました。

</br>
<hr/>
<p align= center><b><a href="../../README.md">メイン ページに戻る</a> — <a href="../sdaccel-getting-started/README.md">入門コースの初めに戻る</a></b></p>
<p align="center"><sup>Copyright&copy; 2019 Xilinx</sup></p>
