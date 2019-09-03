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

# 4\. ハードウェアでの実行

> **重要:** この演習では、コンパイルしたカーネル ライブラリの xclbin を実行するために Alveo アクセラレータ カードを使用する必要があります。Alveo カードをインストールしたマシンにアクセスがない場合、この演習を終了できません。

## 概要

ハードウェア実行では、ホスト プログラムがホスト マシンで実行され、カーネル コードがザイリンクス デバイス上の Alveo アクセラレータ カードで実行されます。ハードウェア実行で取得されるパフォーマンス データが実際のパフォーマンスになります。前の演習で説明したプロファイル サマリおよびタイムライン トレース レポートも使用できます。

> **ヒント:** システム ビルドのデバイス プロファイリングオンにすると (`-t hw`)、全体的なアプリケーションのパフォーマンスに悪影響を与える可能性があります。この機能は、パフォーマンス デバッグにのみ使用し、プロダクション ビルドではオフにするようにしてください。

## 開始前の注意点

この演習を実行する前に、[アプリケーションのビルド](./BuildingAnApplication.md)、[エミュレーションの実行](./Emulation.md)、および[プロファイルおよびタイムライン トレース レポートの生成](./ProfileAndTraceReports.md)演習を確認して実行しておきます。この演習では、これらの演習と同じソース ファイルおよびオプションを使用します。ただし、ホストおよびハードウェア プラットフォームをビルドするのに必要なコマンドすべてを表示はしません。

## デザインのビルド

ザイリンクス ボードまたはデバイスでアプリケーションを実行する前に、必要なシステム モードでデザインをビルドする必要があります。これは、ハードウェアをビルドする際に `-t hw` xocc オプションを使用して指定します。

ホスト ソフトウェアおよびハードウェアのビルド方法および \<*build\_target*> の指定方法については、[アプリケーションのビルド](./BuildingAnApplication.md)演習を参照してください。

> **重要:** サンプルを実行する前に、ランタイム スクリプトを読み込むようにしてください。
>
> ```bash
> #Setup runtime. XILINX_XRT will be set in this step
> #Note that it's only necessary to setup runtime before deployment on hardware.
> source /opt/xilinx/xrt/setup.sh
> ```
>
> **ヒント:** また、XCL\_EMULATION\_MODE 環境は設定しないようにします。前の演習では、これを `sw_emu` または `hw_emu` に設定しました。

次のコマンドを実行します。

```bash
unset XCL_EMULATION_MODE
```

## アプリケーションの実行

ホスト プログラムおよびハードウェア プラットフォーム (xclbin) を使用すると、アプリケーションをザイリンクス ボードまたはデバイスで実行できます。ホスト プログラムおよびハードウェア プラットフォームをまだビルドしていない場合は、次のコマンドを使用します。

> **重要:** FPGA バイナリ ファイルの合成およびプリメンテーションのため、ハードウェア ターゲットのコンパイルいおよびリンクにはかなり時間がかかることがあります。

```bash
source /opt/Xilinx/SDx/2019.1/settings64.sh
source /opt/xilinx/xrt/setup.sh
#make sure working path is in reference-files/run
xcpp -I$XILINX_XRT/include/ -I$XILINX_VIVADO/include/ -Wall -O0 -g -std=c++14 ../src/host.cpp  -o 'host'  -L$XILINX_XRT/lib/ -lOpenCL -lpthread -lrt -lstdc++
xocc -t hw --platform xilinx_u200_xdma_201830_1 -c -k mmult -I'../src' -o'mmult.hw.xilinx_u200_xdma_201830_1.xo' '../src/mmult.cpp'
xocc -t hw --platform xilinx_u200_xdma_201830_1 -l --nk mmult:1:mmult_1 -o'mmult.hw.xilinx_u200_xdma_201830_1.xclbin' mmult.hw.xilinx_u200_xdma_201830_1.xo
```

***アプリケーションの実行***

```bash
./host mmult.hw.xilinx_u200_xdma_201830_1.xclbin
```

アプリケーションが問題なく実行されると、コンソールに次のように表示されます。

```
Loading: 'mmult.hw.xilinx_u200_xdma_201830_1.xclbin'
TEST PASSED
```

## xbutil を使用したボード ステータスおよびデバッグの確認

`xbutil` の使用はオプションですが、ザイリンクス ボードに関する解析およびデバッグに便利なツールです。たとえば、次のコマンドを実行してボード ステータスを確認できます。

```bash
xbutil query
```

ボード ステータス、計算ユニット ステータスなどが表示されます。`xbutil` の詳細は、『SDx コマンドおよびユーティリティ リファレンス ガイド ([UG1279](https://japan.xilinx.com/html_docs/xilinx2019_1/sdaccel_doc/ckx1534452174973.html)) および『SDAccel デバッグ ガイド』 ([UG1281](https://japan.xilinx.com/html_docs/xilinx2019_1/sdaccel_doc/caz1534452170629.html)) を参照してください。

## 次のステップ

ここまでで、SDAccel 開発環境でアプリケーションをビルドして実行するために必要な手順を理解できたはずです。これには、ソフトウェア プログラムおよびカーネルのコード記述、アプリケーションのビルド、ソフトウェアおよびハードウェア エミュレーションの実行、レポートの確認、アクセラレータ カードでのアプリケーションの実行が含まれます。次のステップでは、これを踏まえた上で[最初のプログラムをビルド](../my-first-sdaccel-application/README.md)します。

</br>
<hr/>
<p align= center><b><a href="../../README.md">メイン ページに戻る</a> — <a href="../sdaccel-getting-started/README.md">入門コースの初めに戻る</a></b></p>
<p align="center"><sup>Copyright&copy; 2019 Xilinx</sup></p>
