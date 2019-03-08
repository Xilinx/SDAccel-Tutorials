
<table style="width:100%">
  <tr>
    <th width="100%" colspan="6"><img src="https://www.xilinx.com/content/dam/xilinx/imgs/press/media-kits/corporate/xilinx-logo.png" width="30%"/><h1>2018.2 SDAccel 環境チュートリアル</h2>
</th>
  </tr>
  <tr>
    <td><a href="../../README.md">:house: HOME </a></td>
    <td colspan="2" align="center"><b>C/C++ カーネル入門</b></td>
  </tr>  <tr>
  <td align="center"><a href="getting-started.md">はじめに</a></td>
    <td align="center">演習 1: SDAccel 開発環境の概要</td>
    <td align="center"><a href="lab-2-introduction-to-the-sdaccel-makefile.md">演習 2: SDAccel makefile の概要</a></td>
  </tr>
</table>

## 演習 1: SDAccel 開発環境の概要  

この演習では、ザイリンクス GitHub リポジトリからの SDAccel™ サンプル デザインを使用します。2 種類のフローを示します。手順 1 ～ 3 では GUI フロー、手順 4 では makefile フローを説明します。

<details>
<summary><strong>手順 1: GitHub サンプルからの SDAccel プロジェクトの作成</strong></summary>

  1. Linux のターミナル ウィンドウで `sdx` コマンドを使用して SDx&trade; を起動します。
     [Workspace Launcher] ダイアログ ボックスが表示されます。  

     ![](./images/dew1517374817420.png)  

  2. ワークスペース ディレクトリを選択します。ここで指定したディレクトリにプロジェクトが保存されます。  

  3. **[OK]** をクリックします。   
     [Welcome] ウィンドウが表示されます。[Welcome] ウィンドウは、ツールを初めて起動した場合に表示されます。**[Help] → [Welcome]** をクリックして開くこともできます。

     ![](./images/welcome_window.png)  

  4. [Welcome] ウィンドウで **[Create SDx Project]** をクリックします。  
     New SDx Project ウィザードが開きます。  

     ![](./images/application_project.png)

  5. **[Application]** をオンにし、**[Next]** をクリックします。
     [Create a New SDx Project] ページが開きます。

     ![](./images/project_name.PNG)  

  6. プロジェクトの名前とディレクトリを指定します。このプロジェクトの場合は、[Project name] に「`helloworld`」と入力し、**[Use default location]** をオンにして、[Next] をクリックします。
     [Platform] ページが開きます。  

     ![](./images/hardware_platform_dialog_new.PNG)

  7. `xilinx_u200_xdma_201820_2` プラットフォームを選択して **[Next]** をクリックします。  
     選択したプラットフォームによって、プロジェクトが SDAccel プロジェクトになるか SDSoC™ プロジェクトになるかが決まります。ここでは SDAccel アクセラレーション プラットフォームを選択したので、プロジェクトは SDAccel プロジェクトになります。

     [System Configuration] ページが開きます。このページでは、使用するシステムのタイプとランタイムを定義します。  

     ![](./images/gba1517357172448.png)  

  8. この演習では、デフォルト設定 ([System configuration] は [Linux]、[Runtime] は [OpenCL]) を使用します。   

  9. **[Next]** をクリックします。  
     [Templates] ページが開き、SDAccel プロジェクトの作成に使用可能なテンプレートがリストされます。SDx のサンプルをダウンロードしていない場合は、[Empty Application] と [Vector Addition] のみが表示されます。この演習では、GitHub リポジトリの Hello World サンプルを使用します。これには、まずサンプルをダウンロードする必要があります。  

     ![](./images/faq1517357172427.png)  

  10. **[SDx Examples]** ボタンをクリックします。  
      表示される [SDx Examples] ダイアログ ボックスから、SDAccel サンプルと SDSoC サンプルの両方をダウンロードできます。  

      ![](./images/20182_examples1.png)  

  11. [SDAccel Examples] の **[Download]** ボタンをクリックします。GitHub リポジトリが [Details] に示されているディレクトリにクローンされます。  
      >**:pushpin: 注記:** ダウンロードには、接続速度によって時間がかかることがあります。[Progress Information] ダイアログ ボックスがリポジトリのクローンが完了するまで表示されます。  

      ダウンロードが完了すると、[SDAccel Examples] の下にサンプルがツリー形式でリストされます。  

      ![](./images/20182_examples2.png)  

  12. **[OK]** をクリックしてダイアログ ボックスを閉じ、[Templates] ページに戻ります。  
      [Templates] ページに SDAccel GitHub サンプルが表示されるようになります。  

      ![](./images/github_example_new.PNG)  

  13. [Find] フィールドに「hello」と入力し、[Host Examples] の下の [Hello World (HLS C/C++ Kernel)] を選択します。   

  14. **[Finish]** をクリックします。  
      Hello World プロジェクトが指定した名前で作成され、SDAccel 環境に開きます。次の図ような環境が表示されるはずです。

      ![](./images/helloworld_project.PNG)

      SDAccel 環境には、既にここまでの作業で使用していた Eclipse ベースの SDx 統合環境 (IDE) が含まれます。次の図に示すように、デフォルト パースペクティブには [Project Explorer] ビュー、[Project Editor] ウィンドウ、[Outline] ビューが上部に、[Assistant] ビュー、[Console] ビュー、[Target Connections] ビューが下部に表示されます。SDx IDE の機能の詳細は、『SDAccel 環境ユーザー ガイド』 ([UG1023](https://japan.xilinx.com/cgi-bin/docs/rdoc?v=2018.2;d=ug1023-sdaccel-user-guide.pdf)) を参照してください。

  </details>

<details>
<summary><strong>手順 2: ソフトウェア エミュレーションの実行</strong></summary>

この手順では、ソフトウェア エミュレーションを実行する方法を説明します。[Run Configuration] ダイアログ ボックスの設定を指定し、レポートを開いて、デバッグを開始します。レポートおよびデバッグの詳細は、『SDAccel 環境ユーザー ガイド』 ([UG1023](https://japan.xilinx.com/cgi-bin/docs/rdoc?v=2018.2;d=ug1023-sdaccel-user-guide.pdf)) を参照してください。  

  1. CPU エミュレーションを実行するため、[Application Project Settings] で [Active build configuration] を [Emulation-SW] に設定します。  

     ![](./images/project_settings_hw.png)  

  2. GitHub サンプルには、アクセラレータが既にデザインに含まれています。デザインにアクセラレータが含まれない場合は、[Add Hardware Function] ボタン (![](./images/qpg1517374817485.png)) をクリックしてハードウェア関数を追加します。これにより C/C++ コードが解析され、アクセラレーションに使用可能な関数を判断できます。  

  3. [Run] ボタン ![](./images/lvl1517357172451.png) をクリックしてソフトウェア エミュレーションを実行します。エミュレーション実行前にプロジェクトがビルドされます。  

     >**:pushpin: 注記:** ビルドおよびエミュレーション プロセスには数分以上かかります。この間に、[Run Configurations] ダイアログ ボックスを開き、特定のコマンド ライン オプションを追加してビルドをカスタマイズする方法を説明します。  

  4. [Run] → **[Run Configurations]** をクリックします。  

  5. [Arguments] タブの [Program arguments] フィールドには、XOCC コマンド ライン フラグおよびオプションを追加できます。コマンド オプションの詳細は、『SDx コマンドおよびユーティリティ リファレンス ガイド』 ([UG1279](https://japan.xilinx.com/cgi-bin/docs/rdoc?v=2018.2;d=ug1279-sdx-command-utility-reference-guide.pdf)) を参照してください。このチュートリアルでは、デザインを機能させるのにコマンド ライン引数は必要ありません。  

  6. [Profile] タブには、[Generate timeline trace report] ドロップダウン リストがあります。オプションをクリックすると、生成されるレポートのタイプを確認できます。このタブには、[Enable Profiling] チェック ボックスもあります。何も変更せずウィンドウを閉じます。  
     >**:pushpin: 注記:** [Run Configurations] ダイアログ ボックスの設定を変更をした場合は、**[Run]** ボタンをクリックして現在のエミュレーション段階を再実行し、変更内容を反映させます。  

  7. [Console] ビューに「TEST PASSED」と表示されるはずです。   

  8. エミュレーションの実行が終了したら、[Profile Summary] および [Application Timeline] レポートで最適化の詳細を確認できます。次の図に示すように、[Assistant] ビューで [Profile Summary] をダブルクリックします。

     ![](./images/assistant_reports_hw.PNG)

     デザインを最適化するのに使用可能な動作、実行時間、帯域幅などの有益なデータが表示されます。サマリの数値は、次の図とは異なる場合があります。  

     ![](./images/profile_summary_hw.png)  

  9. [Assistant] ビューに [Application Timeline] レポートを表示するには、[Application Timeline] をダブルクリックします。ホスト コードとカーネル コードの内訳とそれぞれの実行時間が表示されます。特定の部分を拡大するには、マウスを右へドラッグします。

     ![](./images/application_timeline.png)  

  10. [Profile Summary] および [Application Timeline] には、ホスト コードとカーネル間の通信に関するデータと、カーネルの処理情報が含まれます。[Debug] 機能を使用すると、ホスト カーネルの処理をステップ実行して問題を特定できます。[Project Explorer] ビューで `src` ディレクトリにある **host.cpp** をダブルクリックしてエディターで開きます。  

  11. デバッグを実行するには、ブレークポイントを設定する必要があります。重要ポイントにブレークポイントを設定しておくと、問題を見つけやすくなります。カーネル デバッグの前にホスト コードを一時停止するには、89 行目 (次の図の青い選択部分) の (`OCL_CHECK(err, err = q.enqueueMigrateMemObjects({buffer_in1, buffer_in2},0/* 0 means from host*/));`) を右クリックして [Toggle Breakpoint] をクリックします。  

      ![](./images/debug_breakpoint_hw.PNG)  

  12. [Debug] ![](./images/cwo1517357172495.png) をクリックしてデバッグを実行します。パースペクティブを変更するかどうかを尋ねるダイアログ ボックスが表示されます。[Yes] をクリックします。  

  13. Eclipse デバッグを使用すると、ホストおよびカーネル コードを詳細に検証できます。デバッグをステップ実行するための制御コマンドは、[Run] メニューおよびメイン ツールバーにあります。

      ![](./images/debug_hw.png)  

  14. デフォルトでは、`main` の最初の行に自動ブレークポイントが挿入されます。次の図に示すように、[Run Configuration] ダイアログ ボックスの [Debugger] タブに `main` 関数で停止するオプションがあります。これは、問題のある関数をさらに詳細にデバッグする場合に便利な機能です。**F8** キーを押すか、[Run] → [Resume] をクリックして、次のブレークポイントまでデバッグを実行します。  

      ![](./images/debug_configuration_hw.PNG)  

  15. デバッグを再開すると、SDx でカーネル コード用に別の gdb インスタンスが起動します。これにも関数の始めにブレークポイントが設定されています。これでカーネルが詳細に解析され、データがどのように関数に読み込まれてメモリに書き込まれるのかがわかります。カーネル実行が gdb で終了すると、そのインスタンスが終了し、main デバッグ スレッドに戻ります。**F8** キーを押して続行します。  
      >**:pushpin: 注記:** [Console] ビューにはまだカーネル デバッグ出力が表示されています。![](./images/gqm1517357172417.png) をクリックして vadd.exe コンソールに戻り、ホスト コードからの出力を確認します。  

  16. メイン ウィンドウの右上にある [Debug] ボタン ![](./images/cwo1517357172495.png) を右クリックして **[Close]** をクリックして [Debug] パースペクティブを閉じるか、SDx ボタン ![](./images/sdx_perspective_icon.PNG) をクリックして標準の [SDx] パースペクティブに切り替えます。

  17. 標準の [SDx] パースペクティブに戻ったら、中央にあるプロジェクト エディター ウィンドウの [Application Project Settings] 以外のすべてのタブを閉じます。

</details>

<details>
<summary><strong>手順 3: ハードウェア エミュレーションの実行</strong></summary>

この手順では、ハードウェア エミュレーション機能を実行する方法と、基本的なプロファイリングとレポートについて説明します。  

  1. ハードウェア エミュレーションを実行するため、[SDx Application Settings] で [Active build configuration] を [Emulation-HW] に設定し、[Run] をクリックします。これには、少し時間がかかります。<!--how long?-->  
     >**:pushpin: 注記:** [Emulation-SW] と [Emulation-HW] の主な違いは、ハードウェア エミュレーションではカーネル コードの RTL が合成され、プラットフォームのものにより近いデザインをビルドできる点です。より正確な帯域幅、スループット、実行時間などに関するデータが使用されます。このため、デザインのコンパイルに時間がかかります。  

  2. [Assistant] ビューの [Emulation-HW] の下の [System Estimate] をダブルクリックして開きます。
     このテキスト レポートには、カーネル情報、デザインに関するタイミング、クロック サイクル、デバイスで使用されるエリアなどの情報が示されます。

     ![](./images/system_estimate_hw.png)  

  3. [Assistant] ビューで [Profile Summary] ダブルクリックして開きます。このサマリ レポートには、カーネル動作、データ転送、OpenCL API 呼び出しに関する詳細情報のほか、リソース使用量に関するプロファイル情報、カーネル/ホスト間のデータ転送などに関する詳細な情報が示されます。
     >**:pushpin: 注記:** ハードウェア エミュレーションで使用されるシミュレーション モデルは近似です。表示されるプロファイルの数値はあくまで見積もりであり、実際のハードウェアの結果とは異なる可能性があります。  

     ![](./images/profile_summary_report_hw.png)

  4. [Console] ビューの横に [Guidance] というビューがあります。このビューには、満たされなかったチェックに対してカーネルの最適化方法に関する情報が含まれます。

     ![](./images/guidance_view_hw.png)  

     >**:pushpin: 注記:** その他のパフォーマンス最適化手法および設計手法は、『SDAccel 環境プロファイリングおよび最適化ガイド』 ([UG1207](https://japan.xilinx.com/cgi-bin/docs/rdoc?v=2018.2;d=ug1207-sdaccel-optimization-guide.pdf)) を参照してください。  

  5. [Application Timeline] レポートを開きます。  
     このレポートは、ホストおよびカーネルがタスクを終了するのにかかる見積もり時間と、どこがボトルネックなのかを詳細に示します。マーカーを追加、拡大/縮小、信号を展開すると、ボトルネックを見つけるのに役立ちます。  

     ![](./images/timeline_hw.png)  

  6. [Emulation-HW] タブを展開して関連のカーネルのタブを展開し、HLS レポートを開きます。
     このレポートには、Vivado® HLS からのカーネル変換および合成に関する詳細な情報が表示されます。下のタブには、カーネルで最も時間がかかった場所とその他のパフォーマンスに関するデータが表示されます。パフォーマンス データには、レイテンシおよびクロック周期が含まれる場合もあります。  

     ![](./images/hls_hw.png)  
</details>

<details>
<summary><strong>手順 4: makefile フロー</strong></summary>

この手順では、基本的な makefile フローと SDx での使用方法について説明します。このフローを使用する利点は、次のとおりです。  


  * システムに簡単にオートメーションを導入  
  * デザインを少し変更した場合の処理時間の短縮  

mkefile フローを実行するには、次の手順に従います。  

  1. [Project Explorer] ビューの [Emulation-SW] ディレクトリで makefile ファイルを見つけます。ファイルをダブルクリックして、エディターに表示します。  
     この makefile は SDx IDE で作成され、エミュレーションをビルドして実行するのに使用されます。または、[Emulation-HW] ディレクトリで makefile ファイルを見つけます。  

  2. ビルドごとに makefile があります。エディター ウィンドウで開いている makefile の 21 行目で、`hw_emu` または `sw_emu` がターゲットとして指定されています。   

     >**:information_source: ヒント:** SDx IDE で生成された makefile を使用して、GUI の外でプロジェクトをビルドすることもできます。   

  3. 新しいターミナル セッションを開いて、ワークスペースに移動します。

  4. [Emulation-SW] ディレクトリに移動して、「`make incremental`」と入力します。これにより、典型的な SDx のログ出力が生成されます。  

     >**:pushpin: 注記:** ホストまたはカーネル コードが変更されていない場合は、コンパイルは既に完了しているので何も実行されません。「`make: Nothing to be done for ’incremental’`」というメッセージが表示されます。  

[演習 2: SDAccel makefile の概要](./lab-2-introduction-to-the-sdaccel-makefile.md)で、makefile の使用方法とコマンド ライン フローをさらに詳細に説明します。  
</details>

### まとめ


このチュートリアルを終了すると、次ができるようになります。  

* GitHub サンプル デザインから SDAccel 環境プロジェクトを作成。
* デザインのバイナリ コンテナーとアクセラレータを作成。  
* ソフトウェア エミュレーションを実行し、ホストおよびカーネル コードでデバッグ環境を使用。  
* ハードウェア エミュレーションを実行し、レポートを使用して可能な最適化を判別。  
* ソフトウェア エミュレーションとハードウェア エミュレーション レポートの違いを理解。  
* プロジェクトの makefile を読み込んで、makefile コマンド ラインを実行。  


<hr/>

:arrow_forward: **次のトピック:** <a href="lab-2-introduction-to-the-sdaccel-makefile.md">演習 2: SDAccel makefile の概要</a>

:arrow_backward:**前のトピック:** <a href="getting-started.md">はじめに</a>

  <hr/>
  <p align="center"><sup>Copyright&copy; 2018 Xilinx</sup></p>
