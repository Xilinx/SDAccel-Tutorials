<table>
 <tr>
   <td align="center"><img src="https://japan.xilinx.com/content/dam/xilinx/imgs/press/media-kits/corporate/xilinx-logo.png" width="30%"/><h1>2019.1 SDAccel™ 開発環境チュートリアル</h1その他のバージョンを表示</a>
   </td>
 </tr>
 <tr>
 <td align="center"><h1>アクセラレーションされたアプリケーションのビルドおよび実行の基本的な概念</h1>
 </td>
 </tr>
</table>

# 3\. プロファイルおよびトレース レポートの生成

## 概要

プロファイル サマリおよびアプリケーション タイムライン レポートを生成すると、デザインをより理解できます。

* プロファイル サマリには、全体的なアプリケーション パフォーマンスに関する注釈付きの詳細が表示されます。
* アプリケーション タイムラインは、ホストとデバイスのイベント情報を収集し、共通のタイムラインに表示します。これは、システムの全体的な状態とパフォーマンスを視覚的に表示して理解するのに役立ちます。

プロファイル サマリおよびタイムライン トレース レポートは、すべてのビルド ターゲット (`sw_emu`、`hw_emu`、および `hw`) で生成されます。ただし、レポートの精度はビルド ターゲットによって異なります。たとえば、`sw_emu` ビルドの場合、プロファイル サマリレポートにカーネル実行効率およびデータ転送効率の下にデータ転送の詳細は含まれません。

この演習では、ハードウェア エミュレーションを使用してプロファイリングの手順を説明します。ここでの手順は、`sw_emu` または `hw` をターゲット (`-t sw_emu` または `-t hw`) にすると、ほかのフローで簡単に使用できます。

> **重要:** システム ビルドのデバイス プロファイリングオンにすると (`-t hw`)、全体的なアプリケーションのパフォーマンスに悪影響を与える可能性があります。この機能は、パフォーマンス デバッグにのみ使用し、プロダクション ビルドではオフにするようにしてください。

## 開始前の注意点

この演習を実行する前に、[アプリケーションのビルド](./BuildingAnApplication.md)演習および[ソフトウェアおよびハードウェア エミュレーションの実行](./Emulation.md)演習を実行しておく必要があります。

## エミュレーション データの生成

プロファイル サマリおよびアプリケーション タイムライン レポートはデフォルトでは生成されません。これは、エミュレーション データを生成するのに、より多くの時間がかかり、ディスク スペースが必要となるからです。このため、エミュレーションを実行する前にプロファイリング データの収集を有効にしておく必要があります。これには、ホスト プログラムと同じディレクトリ、この場合は `./reference-files/run` ディレクトリにある `sdaccel.ini` テキスト ファイルでオプションを設定します。`sdaccel.ini` ファイルの詳細は、『SDAccel 環境ユーザー ガイド』 ([UG1023](https://japan.xilinx.com/html_docs/xilinx2019_1/sdaccel_doc/itd1534452174535.html)) を参照してください。

### sdaccel.ini ファイルの作成

まず、`sdaccel.ini` ファイルを作成して必要なオプションを追加します。

1. 実行ディレクトリに、`sdaccel.ini` というファイルを作成します。

2. このファイルに次の行を入力します。

   ```
   [Debug]
   profile=true
   timeline_trace=true
   data_transfer = <coarse|fine|off>
   ```

   > **コマンド オプションおよび説明**
   >
   > * `[Debug]`: デバッグ特有のコマンド
   > * `profile=true`: プロファイル モニタリングをイネーブルにします。
   > * `timeline_trace=true`: タイムライン トレース情報の収集をイネーブルにします。
   > * `data_transfer_trace=fine`: デバイス レベルの AXI データ転送情報をイネーブルにします。
   >   * `fine`: すべての AXI レベルのバースト データ転送を表示します。
   >   * `coarse`: 計算ユニット (CU) の転送アクティビティを最初の転送の初めから最後の転送の終わり (計算ユニットの転送終了前) まで表示します。
   >   * `off`: 読み出しをオフにして、ランタイム中にデバイス レベルのトレースがレポートされないようにします。

3. ファイルを保存して閉じます。

### ハードウェア エミュレーションのビルドおよび実行

`sdaccel.ini` ファイルでプロファイリングとタイムライン トレースをイネーブルにすると、エミュレーション中にパフォーマンス データが収集され、CSV ファイルに保存されます。ただし、パフォーマンス情報が取り込まれるようにするには、ハードウェア リンク段階で `--profile_kernel` オプションを使用してカーネルをビルドする必要があります。`--profile_kernel` の詳細は、『SDAccel 環境プロファイリングおよび最適化ガイド』 ([UG1207](https://japan.xilinx.com/html_docs/xilinx2019_1/sdaccel_doc/zgr1534452172723.html)) を参照してください。

1. 次のコマンドを使用してカーネルをビルドし直します。

   ```bash
   xocc -t hw_emu --platform  xilinx_u200_xdma_201830_1 -g -c -k mmult -I'../src' -o'mmult.hw_emu.xilinx_u200_xdma_201830_1.xo' '../src/mmult.cpp'
   xocc -t hw_emu --platform xilinx_u200_xdma_201830_1 -g -l --nk mmult:1:mmult_1 --profile_kernel data:all:all:all -o'mmult.hw_emu.xilinx_u200_xdma_201830_1.xclbin' mmult.hw_emu.xilinx_u200_xdma_201830_1.xo
   ```

   これで、プロファイリングをサポートする新しい xclbin ファイルが生成されます。デザインのビルドに関する詳細は、[アプリケーションのビルド](./BuildingAnApplication.md)演習を参照してください。

2. ビルドが終了したら、次のコマンドを使用しエミュレーションを実行します。

   ```bash
   emconfigutil --platform xilinx_u200_xdma_201830_1
   export XCL_EMULATION_MODE=hw_emu
   ./host mmult.hw_emu.xilinx_u200_xdma_201830_1.xclbin
   ```

> **ヒント:** エミュレーションの実行の詳細は、[ソフトウェアおよびハードウェア エミュレーションの実行](./Emulation.md)演習を参照してください。

### デバッグ情報の表示

エミュレーションを実行したら、実行ディレクトリに次の 2 つの CSV ファイルが生成されます。

* `profile_summary.csv` (プロファイル レポート)
* `timeline_trace.csv` (タイムライン トレース)

### プロファイル サマリの表示

プロファイル サマリを表示するには、CSV ファイルをスプレッドシート ツールで開くか、`sdx_analyze` ユーティリティを使用してデータを HTML レポート形式に変換します。`sdx_analyze` ユーティリティの詳細は、『SDAccel 環境プロファイルおよび最適化 ガイド』 ([UG1207](https://japan.xilinx.com/html_docs/xilinx2019_1/sdaccel_doc/zgr1534452172723.html)) を参照してください。

次のコマンドを実行します。

```bash
sdx_analyze profile -f html -i ./profile_summary.csv
```

HTML ファイルはブラウザーで開くことができます。`firefox profile_summary.html`

次の図に示すように、プロファイル サマリ レポートには、カーネル実行時間、ホストからグローバル メモリへのデータ転送、カーネルからグローバル メモリへのデータ転送に関連する情報が表示されます。

![プロファイル レポート](./images/profile_report.png)

### タイムライン トレースの表示

タイムライン トレースを表示するには、CSV ファイルから波形ファイルを作成します。

1. 次のコマンドを実行します。

   ```bash
   sdx_analyze trace -f wdb -i ./timeline_trace.csv
   ```

2. SDx 環境を使用すると、次のコマンドで生成したタイムライン トレース波形データベースを開くことができます。

   ```bash
   sdx -workspace workspace -report timeline_trace.wdb
   ```

   次はタイムライン トレース レポートの例です。![タイムライン](./images/timeline_trace.png)

   \[Timeline Trace] ビューには、タイムライン ベースで、カーネル実行、ホストからのデータ読み出しおよび書き込み、OpenCL API 呼び出しなどを含むイベントに関する情報が表示されます。

   プロファイル サマリとタイムライン トレースの詳細は、『SDAccel 環境プロファイルおよび最適化 ガイド』 ([UG1207](https://japan.xilinx.com/html_docs/xilinx2019_1/sdaccel_doc/zgr1534452172723.html)) を参照してください。

## まとめ

プロファイル サマリとタイムライン トレース レポートを生成して表示するのに必要な手順をまとめると、次のようになります。

1. `sdaccel.ini` ファイルを作成します。

   ```bash
   [Debug]
   profile=true
   timeline_trace=true
   data_transfer = fine
   ```

2. `--profile_kernel` オプションでプラットフォームをビルドします。

   ```bash
   xocc -t hw_emu --platform xilinx_u200_xdma_201830_1 -g -l --nk mmult:1:mmult_1 --profile_kernel data:all:all:all -o'mmult.hw_emu.xilinx_u200_xdma_201830_1.xclbin' mmult.hw_emu.xilinx_u200_xdma_201830_1.xo
   ```

3. アプリケーションを実行します。

   ```bash
   emconfigutil --platform xilinx_u200_xdma_201830_1
   export XCL_EMULATION_MODE=hw_emu
   ./host mmult.hw_emu.xilinx_u200_xdma_201830_1.xclbin
   ```

4. プロファイル サマリとタイムライン トレースを表示可能な形式に変換します。

   ```bash
   sdx_analyze profile -f html -i ./profile_summary.csv
   sdx_analyze trace -f wdb -i ./timeline_trace.csv
   ```

5. レポートを表示します。

   ```bash
   firefox profile_summary.html
   sdx -workspace workspace -report timeline_trace.wdb
   ```

# 次のステップ

エミュレーションを実行して正確かどうかとパフォーマンスを確認したら、実際のデバイスでアプリケーションを実行します。ハードウェアでアプリケーションを実行する方法は、[ハードウェアでの実行](./HardwareExec.md)演習を参照してください。

</br>
<hr/>
<p align= center><b><a href="../../README.md">メイン ページに戻る</a> — <a href="../sdaccel-getting-started/README.md">入門コースの初めに戻る</a></b></p>
<p align="center"><sup>Copyright&copy; 2019 Xilinx</sup></p>
