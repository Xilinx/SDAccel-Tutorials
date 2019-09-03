<table>
 <tr>
   <td align="center"><img src="https://japan.xilinx.com/content/dam/xilinx/imgs/press/media-kits/corporate/xilinx-logo.png" width="30%"/><h1>2019.1 SDAccel™ 開発環境チュートリアル</h1>
   <a href="https://github.com/Xilinx/SDAccel-Tutorials/branches/all">その他のバージョンを表示</a>
   </td>
 </tr>
 <tr>
 <td align="center"><h1>アクセラレーションされた FPGA アプアプリケーションの最適化手法
 </td>
 </tr>
</table>

# makefile の理解

この演習に必要なソース ファイルはすべて `design` フォルダーに含まれます。`design` フォルダーには、元のアプリケーション コードを含む `cpu_src` と、アクセラレーションされたアプリケーション コードを含む `src` と、`makefile` の 3 つのフォルダーが含まれます。

`src` フォルダーには、さらに 6 つのフォルダーが含まれ、各手順のソース ファイルが含まれます。次のディレクトリは、チュートリアルの各手順に該当しています。

| チュートリアルの手順| 関連ディレクトリ
|----------|----------
| C アプリケーションから SDAccel アプリケーションを作成| [src/baseline](./design/src/baseline)
| メモリ転送を最適化| [src/localbuf](./design/src/localbuf)
| 固定小数点データ型を使用して最適化| [src/fixedpoint](./design/src/fixedpoint)
| データフローを使用して最適化| [src/dataflow](./design/src/dataflow)
| 順不同キューと複数のカーネルを使用| [src/multicu](./design/src/multicu)

makefile の設定を使用すると、ソフトウェア エミュレーション、ハードウェア エミュレーション、Alveo アクセラレータ カードのハードウェアを実行できます。

## デザインをビルドする makefile の設定

デザインをビルドするには、次のコマンドを使用します。

```
make build TARGET=<sw_emu/hw_emu/hw> STEP=<baseline/localbuf/fixedpoint/dataflow/multicu/> SOLUTION=1
```

チュートリアルに基づいてコードをアップデートするため、`src` ディレクトリだけでなく、`reference-files` ディレクトリも提供されています。makefile ターゲットに `SOLUTION=1` を設定すると、`reference-files` ディレクトリのファイルを直接使用できます。

たとえば、ベースライン演習用にハードウェア エミュレーションの xclbin をビルドするには、次のコマンドを使用します。

```
make build TARGET=hw_emu STEP=baseline SOLUTION=1
```

## デザインを実行する makefile の設定

デザインをエミュレーションまたはハードウェアで実行するには、次のコマンドを使用します。

```
make run TARGET=<sw_emu/hw_emu/hw> STEP=<baseline/localbuf/fixedpoint/dataflow/multicu> SOLUTION=1 [NUM_FRAMES=1]
```

NUM\_FRAMES オプションでは、ホスト アプリケーションの実行に使用される入力ビデオ フレーム数を定義します。ソフトウェアおよびハードウェア エミュレーションの場合は、NUM\_FRAMES を **1** に設定してエミュレーション時間を削減します。ハードウェア実行の場合は、ビデオ ファイル全体が使用されるので、このオプションは使用しないでください。

たとえば、ベースライン手順のためにハードウェア エミュレーションを実行するには、次のコマンドを使用します。

```
make run TARGET=hw_emu STEP=baseline SOLUTION=1 NUM_FRAMES=1
```

## プロファイルおよびタイムライン トレース レポートを生成する makefile の設定

デザインのパフォーマンスを解析するには、レポートを確認する必要があります。レポートの詳細は、「アクセラレーションされたアプリケーションをビルドして実行するための基本的な概念」チュートリアルの[プロファイルおよびトレース レポートの生成](../Pathway3/ProfileAndTraceReports.md)演習を参照してください。

次のコマンドを使用して、プロファイル サマリ レポートとタイムライン トレース レポートを生成します。

```
make gen_report TARGET=hw_emu STEP=multicu
```

このコマンドを実行すると、プロファイル サマリおよびタイムライン トレース レポートが生成されます。上記のコマンドでは、次のレポートが生成されます。

- `sdaccel_profile_summary_hw_emu.html`
- `sdaccel_timeline_trace_hw_emu.wdb`

## プロファイルおよびタイムライン トレースを表示する makefile の設定

1. 次のコマンドを使用して、プロファイル サマリ レポートを表示します。

   ```
   make view_prof_report TARGET=hw_emu STEP=multicu
   ```

   makefile は次のコマンドを使用して、プロファイル サマリ レポートを開きます。

   ```
   firefox sdaccel_profile_summary_hw_emu.html
   ```

2. 次のコマンドを使用して、タイムライン トレース レポートを表示します。

   ```
   make view_timeline_trace TARGET=hw STEP=multicu
   ```

   makefile は次のコマンドを使用して、SDx を起動してタイムライン トレース レポートを表示します。

   ```
   sdx -report sdaccel_timeline_trace_multicu.wdb
   ```

## クリーンアップの makefile の設定

前に生成したビルドをクリーンアップするには、次のコマンドを使用します。

```
make clean TARGET=hw_emu STEP=baseline
```

これにより、ベースライン バージョンのデザイン用にハードウェア エミュレーションで生成されたファイルが削除されます。

</br>
<hr/>
<p align= center><b><a href="../../README.md">メイン ページに戻る</a> — <a href="../sdaccel-getting-started/README.md">入門コースの初めに戻る</a></b></p>
<p align="center"><sup>Copyright&copy; 2019 Xilinx</sup></p>
