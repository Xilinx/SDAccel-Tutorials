<p align="right">
<a href="../../../docs/controlling-vivado-impl/README.md">English</a> | <a>日本語</a>
</p>
<table>
 <tr>
   <td align="center"><img src="https://japan.xilinx.com/content/dam/xilinx/imgs/press/media-kits/corporate/xilinx-logo.png" width="30%"/><h1>2019.1 SDAccel™ 開発環境チュートリアル</h1>
   <a href="https://github.com/Xilinx/SDAccel-Tutorials/branches/all">その他のバージョンを表示</a>
   </td>
 </tr>
 <tr>
 <td align="center"><h1>Vivado インプリメンテーションの制御</h1>
 </td>
 </tr>
</table>

# 概要

SDAccel™ 環境の XOCC コンパイラは、ソース コードからカーネル オブジェクトを作成し、カーネルをターゲット シェルとリンクし、Vivado® ツールのインプリメンテーション フローを使用してアセンブルしたデザインを実行します。また、FPGA ベースのアクセラレーション カードをプログラムするのに必要なプラットフォーム ファイル (xclbin) を生成します。望む結果を達成するためには、タイミング クロージャなど、Vivado 合成およびインプリメンテーションのアドバンス オプションを使用する必要があることもあります。

# チュートリアルの概要

このチュートリアルでは、プロジェクトをインプリメントする際に Vivado ツール フローを制御する方法をお見せし、コマンド ライン フローの手順を示します。

SDAccel™ 環境には、Vivado ツール フローを制御する 2 つの方法があります。

1. システム ビルドのコンパイルまたはリンクする際は、XOOC `--xp` オプションを使用すると、Vivado の合成およびインプリメンテーション オプションを渡すことができます。

   > **注記:** システム実行を終了するには数時間かかることがあります。

2. アプリケーションが既にコンパイル済みの場合は、次を実行できます。

   * Vivado® Design Suite を使用してデザインを最適化します。
   * 新しい配線済みチェックポイントを生成します。
   * このチェックポイントを再利用してプラットフォーム ファイル (xclbin) を生成します。

# 開始前の注意点

このチュートリアルでは、次を使用します。

* BASH Linux シェル コマンド
* 2019.1 SDx™ リリースおよび xilinx\_u200\_xdma\_201830\_1 プラットフォーム。必要であれば、その他のバージョンおよびプラットフォームも使用できます。

> **重要:**
>
> * サンプル ファイルを実行する前に、『SDAccel 開発環境リリース ノート、インストール、およびライセンス ガイド』 ([UG1238](https://japan.xilinx.com/html_docs/xilinx2019_1/sdaccel_doc/yrc1534452173645.html)) で説明されるように、ザイリンクス ランタイム (XRT) と SDAccel 開発環境ツールをインストールしてください。
> * Alveo™ カードでアプリケーションを実行する場合は、『Alveo データセンター アクセラレータ カード入門ガイド ([UG1301](https://japan.xilinx.com/support/documentation/boards_and_kits/accelerator-cards/j_ug1301-getting-started-guide-alveo-accelerator-cards.pdf)) で説明されるように、カードとソフトウェア ドライバーを正しくインストールしてください。
> * この演習を実行する前に、[アクセラレーションされたアプリケーションをビルドおよび実行の基本的な概念](../Pathway3/README.md)チュートリアルを理解しておく必要があります。

## チュートリアル リファレンス ファイルの入手

1. リファレンス ファイルを入手するには、ターミナルに `git clone https://github.com/Xilinx/SDAccel-Tutorials` と入力します。
2. `SDAccel-Tutorials-master/docs/controlling-vivado-impl/reference-files/run` に移動します。

# --xp XOOC プションの使用

> **注記:** このチュートリアルでは、すべての手順を `reference-files/run` ディレクトリから実行します。

`–-xp` オプションは Vivado ツールを設定するパラメーターに対応しています。たとえば、`--xp` オプションで最適化、配置、およびタイミングを設定したり、エミュレーションおよびコンパイル オプションを設定したりできます。コマンド ライン フローでは、パラメーターが `param:<param_name>=<value>` と指定されます。

* `param`: 必須のキーワード。
* `param_name`: 適用するパラメーターの名前。
* `value`: パラメーターに適した値。

このセクションでは、次のコンフィギュレーションを例として使用して、Vivado 合成およびインプリメンテーションを制御するフローをお見せします。

* RTL 合成で階層を完全にフラット化 (FLATTEN\_HIERARCHY=full を指定)

  * `--xp vivado_prop:run.my_rm_synth_1.{STEPS.SYNTH_DESIGN.ARGS.FLATTEN_HIERARCHY}={full}`

* 配線で NoTimingRelaxation 指示子を使用 (DIRECTIVE=NoTimingRelaxation を指定)

  * `--xp vivado_prop:run.impl_1.{STEPS.ROUTE_DESIGN.ARGS.DIRECTIVE}={NoTimingRelaxation}`

> **注記:** `-–xp` コマンド オプションは 1 回の xocc の起動で何度も指定できます。または、`--xp` オプションを使用せずに、`xocc.ini` ファイルで各行にオプションを 1 つずつ指定することもできます。

すべてのパラメーターおよび有効な値のリストは、『SDx コマンドおよびユーティリティ リファレンス ガイド ([UG1279](https://japan.xilinx.com/html_docs/xilinx2019_1/sdaccel_doc/ckx1534452174973.html)) を参照してください。

1. サンプルを実行する前に、次のコマンドを実行して SDx ツール、プラットフォーム、ランタイムがを設定されるようにしてください。

   ```bash
   #setup Xilinx SDx tools, XILINX_SDX and XILINX_VIVADO will be set in this step. source <SDX install path>/settings64.sh. for example:
   source /opt/Xilinx/SDx/2019.1/settings64.sh
   #Setup runtime. XILINX_XRT will be set in this step
   source /opt/xilinx/xrt/setup.sh
   ```

2. XOCC を使用してカーネルをコンパイルし、それを `--xp` コマンド オプションでプラットフォーム ファイル (xclbin) にリンクします。

   ```bash
   xocc -t hw --platform xilinx_u200_xdma_201830_1 -g --max_memory_ports apply_watermark -c -k apply_watermark -o'apply_watermark.hw.xilinx_u200_xdma_201830_1.xo' '../src/krnl_watermarking.cl'
   xocc -t hw --platform xilinx_u200_xdma_201830_1 -g --sp apply_watermark_1.m_axi_gmem0:DDR[0] --sp apply_watermark_1.m_axi_gmem1:DDR[1] -s --xp vivado_prop:run.my_rm_synth_1.{STEPS.SYNTH_DESIGN.ARGS.FLATTEN_HIERARCHY}={full} --xp vivado_prop:run.impl_1.{STEPS.ROUTE_DESIGN.ARGS.DIRECTIVE}={NoTimingRelaxation} -R2 -l --nk apply_watermark:1:apply_watermark_1 -o'apply_watermark.hw.xilinx_u200_xdma_201830_1.xclbin' apply_watermark.hw.xilinx_u200_xdma_201830_1.xo
   ```

   前述のコマンド オプションについて説明します。

   > **コマンド オプションの説明**
   >
   > * `-t hw`: システム実行をターゲットに指定
   > * `--platform xilinx_u200_xdma_201830_1`: xilinx\_u200 プラットフォームをターゲットに指定
   > * `-g`: デバッグ情報を生成
   > * `--max_memory_ports apply_watermark`: 1 つのポートに対して 1 つの AXI4 インターフェイスを生成
   > * `-c`: カーネルをコンパイル
   > * `-k apply_watermark`: カーネル名を指定
   > * `../src/krnl_watermarking.cl`: ソース ファイルを指定
   > * `--sp apply_watermark_1.m_axi_gmem0:DDR[0] --sp apply_watermark_1.m_axi_gmem1:DDR[1]`: DDR バンク接続を指定
   > * `--xp vivado_prop:run.my_rm_synth_1.{STEPS.SYNTH_DESIGN.ARGS.FLATTEN_HIERARCHY}={full} --xp vivado_prop:run.impl_1.{STEPS.ROUTE_DESIGN.ARGS.DIRECTIVE}={NoTimingRelaxation}`: Vivado 合成およびインプリメンテーション オプションを設定
   > * `-R2`: レポート レベルを 2 に設定して、各インプリメンテーション段階ごとに DCP を生成
   > * `-l`: カーネルをリンク
   > * `--nk apply_watermark:1:apply_watermark_1`: カーネル インスタンスの数と名前を指定

3. Vivado 合成およびインプリメンテーション オプションが適用されたことを確認するには、`_x/logs/link/vivado.log` ファイルで「flatten\_hierarchy」および「NoTimingRelaxation」を検索します。オプションが実行されたかどうかは、次の行からわかります。

   * **コマンド**: `synth_design -top pfm_dynamic -part xcu200-fsgd2104-2-e -flatten_hierarchy full -mode out_of_context`
   * **コマンド**: `route_design -directive NoTimingRelaxation`

# Vivado ツールを使用した最適化

アプリケーションが既にシステム実行用にコンパイルされている場合は、さまざまな Vivado ツールの手法を使用してインプリメンテーション結果を最適化し、タイミング クロージャなどの目標を達成します。最適化が終了したら、配線済みのデザインをチェックポイント ファイル (DCP) に書き戻して、このチェックポイントを使用して新しいインプリメンテーションを終了します。

> **注記:** SDx 環境と Vivado 環境のバージョンは異なるイテレーション間で同じにする必要があります。

最適化段階では、Vivado ツールの手法を使用します。Vivado ツールの使用方法および最適化については、『Vivado Design Suite ユーザー ガイド: インプリメンテーション』 ([UG904](https://japan.xilinx.com/cgi-bin/docs/rdoc?v=latest;d=ug904-vivado-implementation.pdf)) および『UltraFast 設計手法ガイド (Vivado Design Suite 用)』 ([UG949](https://japan.xilinx.com/cgi-bin/docs/rdoc?v=latest;d=ug949-vivado-design-methodology.pdf)) を参照してください。

たとえば、Tcl スクリプトを使用してバッチ モードで Vivado ツールを実行する場合、その Tcl スクリプトにより DCP が読み込まれて最適化が実行され、配線された DCP が書き出されます。

`/reference-file/run` フォルダーから次のコマンドを実行します。

```bash
#make sure you have successfully setup SDx/Vivado environments as previous part shows.
vivado -mode batch -source opt.tcl
```

この場合、`opt.tcl` ファイルに次のように表示されます。

```bash
#DCP files in different stages of Vivado have been written by xocc linker automatically with option "-R2"
open_checkpoint ./_x/link/vivado/vpl/prj/prj.runs/impl_1/pfm_top_wrapper_routed.dcp
#Run post-route physical optimization
phys_opt_design -directive AggressiveExplore
write_checkpoint -force ./_x/link/vivado/routed.dcp
```

# チェックポイントを再利用したプラットフォーム ファイル (xclbin) の生成

次は、`opt.tcl` スクリプトで生成された `routed.dcp` チェックポイント ファイルを再利用して新しいプラットフォーム ファイル (xclbin) を生成します。これには、XOCC コマンドに `--reuse_impl` オプションを追加します。

```bash
xocc -t hw --platform xilinx_u200_xdma_201830_1 -l -o'apply_watermark.hw.xilinx_u200_xdma_201830_1.xclbin' apply_watermark.hw.xilinx_u200_xdma_201830_1.xo --reuse_impl ./_x/link/vivado/routed.dcp
```

コンソールのメッセージに、インプリメントしたデザインの生成を飛ばして、ビットストリームの生成が開始されたことが表示されます。

```bash
INFO: [VPL 60-251]   Hardware accelerator integration...
Starting FPGA bitstream generation.
```

# まとめ

次のセクションには、XOCC コマンドを使用して Vivado インプリメンテーションを制御し、Vivado ツールを使用してデザインを最適化し、インプリメントした DCP を再利用してプラットフォーム ファイルを生成する方法をまとめていますので、コマンドのクリック リファレンスとしてご利用ください。

1. 環境および作業ディレクトリを設定します。

   ```bash
   #setup Xilinx SDx tools, XILINX_SDX and XILINX_VIVADO will be set in this step. source <SDX install path>/settings64.sh. for example:
   source /opt/Xilinx/SDx/2019.1/settings64.sh
   #Setup runtime. XILINX_XRT will be set in this step
   source /opt/xilinx/xrt/setup.sh
   ```

2. XOCC コマンドを使用してプラットフォーム ファイル (xclbin) を生成します。

   ```bash
   xocc -t hw --platform xilinx_u200_xdma_201830_1 -g --max_memory_ports apply_watermark -c -k apply_watermark -o'apply_watermark.hw.xilinx_u200_xdma_201830_1.xo' '../src/krnl_watermarking.cl'
   xocc -t hw --platform xilinx_u200_xdma_201830_1 -g --sp apply_watermark_1.m_axi_gmem0:DDR[0] --sp apply_watermark_1.m_axi_gmem1:DDR[1] -s --xp vivado_prop:run.my_rm_synth_1.{STEPS.SYNTH_DESIGN.ARGS.FLATTEN_HIERARCHY}={full} --xp vivado_prop:run.impl_1.{STEPS.ROUTE_DESIGN.ARGS.DIRECTIVE}={NoTimingRelaxation} -R2 -l --nk apply_watermark:1:apply_watermark_1 -o'apply_watermark.hw.xilinx_u200_xdma_201830_1.xclbin' apply_watermark.hw.xilinx_u200_xdma_201830_1.xo
   ```

3. Vivado ツールを使用してデザインを最適化し、配線後の DCP を記述し直します。

   ```bash
   vivado -mode batch -source opt.tcl
   ```

4. 配線後の DCP を再利用して、プラットフォーム ファイル (xclbin) を生成します。

   ```bash
   xocc -t hw --platform xilinx_u200_xdma_201830_1 -l -o'apply_watermark.hw.xilinx_u200_xdma_201830_1.xclbin' apply_watermark.hw.xilinx_u200_xdma_201830_1.xo --reuse_impl ./_x/link/vivado/routed.dcp
   ```

5. このチュートリアルには、さまざまなフローを実行するためのホスト コードが含まれています。次のコマンドは、ホストをビルドしてからアプリケーションを実行します。

   ```bash
   #Generate the host executable.
   xcpp -I$XILINX_XRT/include/ -I$XILINX_VIVADO/include/ -Wall -O0 -g -std=c++14 -L$XILINX_XRT/lib/ -lOpenCL -lpthread -lrt -lstdc++ -o 'host' '../src/host.cpp'
   #Please set correct XCL_EMULATION_MODE manually if running sw_emu and hw_emu modes
   ./host apply_watermark.hw.xilinx_u200_xdma_201830_1.xclbin ../src/inputImage.bmp ../src/golden.bmp
   ```

# まとめ

このチュートリアルでは、Vivado ツールでデザインを最適化する単純な例を使用して、XOCC コマンドの `--xp` オプションで Vivado 合成およびインプリメンテーションを制御する方法について説明しました。その後、インプリメント済みデザインを生成する中間の手順を省いて、インプリメント後の DCP を再利用してプラットフォーム ファイル (xclbin) を生成しました。

すべてのフローを実行するためのホスト コードとそのビルド コマンドも参照用に含まれています。

</br>
<hr/>
<p align= center><b><a href="../../README.md">メイン ページに戻る</a></b></p>
<p align="center"><sup>Copyright&copy; 2019 Xilinx</sup></p>
