# SDAccel 開発環境の概要

この演習では、ザイリンクス GitHub リポジトリ ([ここ](https://github.com/Xilinx/SDAccel_Examples)) からの SDAccel™ サンプル デザインを使用します。

## チュートリアル実行の準備

* RDP クライアントを使用して、FPGA Developer AMI と一緒に読み込まれた AWS EC2 インスタンスに接続します。詳細な方法は、「[AWS F1 インスタンスの作成、設定、テスト](STEP1.md)」を参照してください。

* AWS インスタンスのターミナルで次のコマンドを実行して、SDAccel 環境を設定します。

  ```bash
  cd $AWS_FPGA_REPO_DIR  
  source sdaccel_setup.sh
  ```

* 新しいディレクトリを作成して、このチュートリアルに必要なソース ファイルをコピーします。

  ```bash
  cd /home/centos
  cp -rp $AWS_FPGA_REPO_DIR/SDAccel/examples/xilinx_2018.2/getting_started/host/helloworld_c .
  cp $AWS_FPGA_REPO_DIR/SDAccel/examples/xilinx_2018.2/libs/xcl2/xcl2.cpp ./helloworld_c/src
  cp $AWS_FPGA_REPO_DIR/SDAccel/examples/xilinx_2018.2/libs/xcl2/xcl2.hpp ./helloworld_c/src
  cd helloworld_c
  ```

## SDAccel プロジェクトの作成

ここでは、SDAccel を起動し、新しいワークスペースを作成し、カスタムの AWS F1 プラットフォーム ファイルを読み込んで、新しいプロジェクトを作成します。

* 次のコマンドで SDx GUI を起動します。
  ```bash
  sdx -workspace ./workspace
  ```

> **注記**: AWS インスタンスで SDx GUI を最初に起動するには少し時間がかかりますが、2 回目からはかなり速く起動します。

* **\[Project Type]** ビューで **\[Application]** をオンにして **\[Next]** をクリックします。

* **Create a New SDx Project** ウィザードで **\[Project name]** フィールドに「**Test**」と入力して **\[Next]** をクリックします。

* **\[Platform]** ビューで **\[Add Custom Platform]** をクリックし、`/home/centos/src/project_data/aws-fpga/SDAccel/aws_platform` ディレクトリを指定して **\[OK]** をクリックします。

* **\[Platform]** ビューに戻って、新しく追加した AWS VU9P F1 カスタム プラットフォームを選択して、**\[Next]** をクリックします。

* **\[System configuration]** ビューでは、**\[System configuration]** のオプションは **\[Linux on x86]** だけで、**\[Runtime]** のオプションは **\[OpenCL]** だけです。**\[Next]** をクリックします。

* **\[Templates]** ビューが開き、SDAccel プロジェクトの作成に使用可能なテンプレートがリストされます。このチュートリアルでは、**\[Empty Application]** を選択し、**\[Finish]** をクリックします。

これで「**Test**」という名前の AWS F1 プラットフォーム用 SDAccel プロジェクトが作成されました。この情報は GUI 中央の \[SDX Project Settings] ビューにはっきりと表示されます。

デフォルトの GUI パースペクティブには、次のセクションが含まれます。

* メイン メニュー バーは一番上に表示されます。このメニュー バーからは、一般的なプロジェクト設定および GUI ビューの管理タスクを直接実行できます。ほとんどのタスクは、ほかの設定ビューから実行されるので、主にメイン メニューは、間違って閉じてしまったビューを復元したり、ツール ヘルプにアクセスするために使用します。
* メイン メニュー バーのすぐ下には、**SDAccel ツールバー**が表示されます。このツールバーからは、プロジェクトで最もよく使用されるタスクにアクセスできます。
* **\[Project Explorer]** ビューは、GUI の左側にあります。このビューは、プロジェクト ファイルを管理およびナビゲートするために使用します。
* 中央に表示されるのは **\[SDx Project Settings]** ビューです。これは、プロジェクトを管理し、主なプロジェクト情報を表示するためのビューです。
* 右側の **\[Outline]** ビューはファイルのナビゲーションに使用します。表示される内容は、メイン ビューで現在選択されているファイルによって異なります。
* 左下には **\[Guidance]** ビューが表示されます。このビューからは、SDAccel で生成されたレポートすべてに簡単にアクセスできます。
* メイン ビューの下に表示される残りのビューには、さまざまなコンソールおよびターミナルが含まれ、個別の SDAccel 実行ファイルに関連する出力情報が表示されます。通常は、コンパイル エラーやツールから出力されるメッセージなどです。

SDx IDE の機能の詳細は、『SDAccel 環境ユーザー ガイド』 ([UG1023](https://japan.xilinx.com/cgi-bin/docs/rdoc?v=replace;d=ug1023-sdaccel-user-guide.pdf)) を参照してください。

## デザイン ファイルのインポート

* \[Project Explorer] ビューで [Import Sources] ボタン ![](images/ImportSRC.png) をクリックします。

* \[Import Sources] ダイアログ ボックスで **\[Browse]** をクリックし、`/home/centos/helloworld_c/src` ディレクトリを指定します。**\[OK]** をクリックします。

* **\[Select All]** をクリックし、**\[Finish]** をクリックします。

* **\[Project Explorer]** の **src** ディレクトリを展開して、すべてのファイルがプロジェクト内に含まれていることを確認します。

  * host.cpp - ホスト プログラムのソース コード
  * vadd.cpp - ベクター加算関数をインプリメントする C++ カーネルのソース コード
  * xcl2.cpp および xcl2.hpp - FPGA バイナリ ファイルを使用しやすくするためのヘルパー関数

* **host.cpp** をダブルクリックして開きます。この例は単純でコンパクトですが、コードがどのように動作するのか、ホスト アプリケーションが FPGA デバイスのカーネルとどう関わり合うのかを把握しやすいよう、コメントが多く含められています。

## ハードウェア アクセラレーション用の関数の選択

必要なソース ファイルをすべてインポートしたので、次は、FPGA アクセラレーション用に、どの関数をハードウェアにマップするのか指定する必要があります。

* **\[SDx Project Settings]** ビューの **\[Hardware Functions]** セクションで **\[Add Hardware]** ボタン ![](images/AddHW.png) をクリックします。

* SDAccel では、デザインのカーネルすべてが解析され、複数のカーネルがあれば、それをフィルターにかける機能があります。このデザインの場合、**vadd** 関数のみが含まれています。

* **vadd** 関数を選択して **\[OK]** をクリックします。

* バイナリ コンテナー (デフォルトでは **binary\_container\_1**) がプロジェクトに追加され、**vadd** カーネルがこのコンテナーに追加されている点に注目してください。

## 実行コンフィギュレーション オプションの定義

ここでは、SDAccel で実行するときに、アプリケーションに渡されるオプションのコマンド ライン引数をどこで指定するかを説明します。

* **\[Run]** → **\[Run Configurations]** をクリックします。
* **\[Arguments]** ビューをクリックします。
* **\[Program Arguments]** でコマンド ライン引数を指定できます。このサンプル アプリケーションでは引数は使用できないので、このセクションは空のままにしておきます。
* **\[Close]** をクリックします。

\[Profile] ビューには、**\[Generate timeline trace report]** ドロップダウン リストがあります。このドロップダウン リストのオプションをクリックすると、生成されるレポートのタイプを確認できます。このビューには、**\[Enable Profiling]** チェック ボックスもあります。何も変更せずビューを閉じます。

## ソフトウェア エミュレーションの実行

ソフトウェア エミュレーションは、ホスト コードとカーネル コードを一緒に実行したときに、正しく機能するかどうかを確認するために使用されます。カーネルはソフトウェア関数としてコンパイルされるので、コンパイルや反復ループの処理がかなり速くなります。ソフトウェア エミュレーションは、アルゴリズムを調整し、機能的な問題をデバッグし、コードを改善するのに適しています。

* **\[Application Project Settings]** で **\[Active build configuration]** が **\[Emulation-SW]** に設定されていることを確認します。

* **SDAccel** ツールバーで **\[Build]** ボタン ![](images/Build.png) をクリックします。ソフトウェア エミュレーション用のプロジェクトが作成されますが、その作成にはあまり時間はかかりません。

* デザインが作成されたら、**\[Run]** ボタン ![](images/Run.png) をクリックします。すると、ソフトウェア エミュレーションが実行されます。ホスト アプリケーションが実行し、C バージョンのカーネルと通信します。**\[Console]** ビューにメッセージが表示されます。テストにパスしたことを伝えるメッセージが表示されたら、実行は終了です。

  ```
  Found Platform
  Platform Name: Xilinx
  XCLBIN File Name: vadd
  INFO: Importing ../binary_container_1.xclbin
  Loading: '../binary_container_1.xclbin'
  TEST PASSED
  ```

## デバッガーの使用

SDAccel にはビルトインのデバッガーがあり、アプリケーションをステップ処理して、問題箇所を正確に突き止めることができます。ここでは、デバッガーの起動および使用方法を学び、ホスト アプリケーションが FPGA のカーネルとどう対話するのかを理解するため、コードを 1 行ずつ処理していきます。

* `host.cpp` ファイルを開きます。

* 73 行目の行番号を右クリックし、**\[Toggle Breakpoint]** を選択して、この行にブレークポイントを設定します。

* **\[Debug]** モードを実行するには、**\[Debug]** ボタン ![](images/Debug.png) をクリックします。パースペクティブを変更することを尋ねるダイアログ ボックスが表示されます。**\[Yes]** をクリックします。

* Eclipse のデバッグ パースペクティブを使用すると、ホストおよびカーネルのコードをさらに詳細に確認できます。デバッグをステップ実行するコマンドは、メインのメニュー バーまたは **\[Run]** メニューに含まれます。

* デバッグを開始すると、プログラムは **main** 関数の最初の行で停止します。

* **\[Resume]** ボタン (F8) ![](images/Resume.png) をクリックしてアプリケーションを実行します。

* デバッグは **host.cpp** ファイルの 73 行目のブレークポイントまで進みます。

* **\[Step Over]** ボタン (F6) ![](images/StepOver.png) をクリックして、コードを進め、ホストアプリケーションの API 呼び出しシーケンスを確認します。特に、次の点に注意してください。

  * 73 行目: FPGA バイナリ ファイルがアプリケーションによりデバイスに読み込まれます。
  * 81 から 83 行目: デバイスとデータを交信するためのバッファーがアプリケーションにより作成されます。**vadd** では、2 つの配列を入力すると、1 つの配列が出力されます。このため、各配列に 1 つ、合計 3 つのバッファーが作成されます。
  * 89 行目: 2 つの入力バッファーをデバイスに移行させるよう、アプリケーションによりスケジュールされます。
  * 92 から 95 行目: カーネルの入力引数がアプリケーションにより設定されます。
  * 100 行目: カーネルの実行がアプリケーションによりスケジュールされます。
  * 103 行目: 出力バッファーをホストに戻すよう、アプリケーションによりスケジュールされます。
  * 104 行目: スケジュールされた操作がすべて完了するまでアプリケーションは待機します。

> **注記**: ホストとデバイス間のやりとりがどのように SDAccel アプリケーションで管理されるのかが、この単純なシーケンスで示されています。

* ビューの右上に ![](images/Debug.png) ボタンが表示されているので、それを右クリックし、**\[Close]** をクリックして、**\[Debug Perspective]** を閉じます。

## アプリケーション タイムラインの使用

* ソフトウェア エミュレーションを実行し、**Application Timeline** レポートを含む関連レポートを生成するには、**[Run]** ボタン ![](images/Run.png) をクリックします。

* **\[Assistant]** ビューで、**\[Emulation-SW]** を展開し、**\[Test-Default]** をクリックします。**\[Application Timeline]** をダブルクリックします。OpenCL API 呼び出し、データ転送、カーネル実行のタイムラインが表示されます。

* このタイムラインの右端のセクションを拡大表示します。ホスト アプリケーションによりデバイスにデータが転送され、カーネルがプログラムおよび実行され、その結果が返されるシーケンスをここでは確認します。シーケンスは、次の図のようになっているはずです。

![](images/TimelineSW.png)

* このタイムラインでは、デバッグ ステップ中の API 呼び出しシーケンスが視覚的にわかりやすく表示されています。このシーケンスを確認するため、簡単な実験をしてみましょう。

* **\[Application Timeline]** ビューを閉じます。

* **host.cpp** ファイルに戻り、88 行目から 103 行目まで (88 行目と 103 行目を含む) の 4 回の反復実行を含む loop を追加します。変更後のコードは次のようになるはずです。

  ```c
  for (int i=0; i<4; i++) {
    // Copy input data to device global memory
    OCL_CHECK(err, err = q.enqueueMigrateMemObjects({buffer_in1, buffer_in2},0/* 0 means from host*/));

    int size = DATA_SIZE;
    OCL_CHECK(err, err = krnl_vector_add.setArg(0, buffer_in1));
    OCL_CHECK(err, err = krnl_vector_add.setArg(1, buffer_in2));
    OCL_CHECK(err, err = krnl_vector_add.setArg(2, buffer_output));
    OCL_CHECK(err, err = krnl_vector_add.setArg(3, size));

    // Launch the Kernel
    // For HLS kernels global and local size is always (1,1,1). So, it is recommended
    // to always use enqueueTask() for invoking HLS kernel
    OCL_CHECK(err, err = q.enqueueTask(krnl_vector_add));

    // Copy Result from Device Global Memory to Host Local Memory
    OCL_CHECK(err, err = q.enqueueMigrateMemObjects({buffer_output},CL_MIGRATE_MEM_OBJECT_HOST));
  }
  q.finish();
  ```

* 変更を保存し、**\[Run]** ボタン ![](images/Run.png) をクリックします。カーネル コードは変更されていないので、インクリメンタル ビルド フローでは、ソフトウェア エミュレーションの実行前に、ホスト プログラムのみが再コンパイルされます。

* **\[Console]** に「**TEST PASSED**」のメッセージが表示されるまで待ち、**\[Application Timeline]** をもう一度開きます。

* アプリケーションの実行シーケンスへのコード変更の成果を確認するため、右端を拡大表示します。

* タイムラインの **\[Kernel Enqueues]** セクションにある青色の **vadd** のボックスの 1 つをクリックします。リード/実行/ライトの依存関係がハイライトされていて、**vadd** カーネルが 4 回連続して実行されていることがわかります。

![](images/TimelineSW_4.png)

## ハードウェア エミュレーションの実行

ソフトウェア エミュレーションはカーネルの抽象モデルを使用して実行されます。すばやい反復実行を用意し、アプリケーションが正しく機能していることを検証するのは非常に便利ですが、詳細な情報は得られないので、パフォーマンスを有意義に見積もることはできません。このため、SDAccel にはハードウェア エミュレーション モードがあります。ハードウェア エミュレーションでは、FPGA デバイスでの動作に非常に近いハードウェア記述レベルにカーネルがコンパイルされます。つまり、帯域幅、スループット、実行時間に関連したデータがより正確になります。従って、デザインのコンパイルや実行にさらに時間がかかります。

ここでは、ハードウェア エミュレーション モードの実行方法、および生成されるレポートの一部の使用方法を学びます。

* **\[Application Project Settings]** ビューに移動し、**\[Active build configuration]** を **\[Emulation-HW]** に設定します。

* **SDAccel** ツールバーで **\[Run]** ボタン ![](images/Run.png) をクリックします。これで、ハードウェア エミュレーション モードでアプリケーションがビルドされ実行されます。

  > 注記: ビルド ステップ中、カーネルは詳細なハードウェア記述 (レジスタ トランスファー レベル) にコンパイルされ、選択したプラットフォームのモデルにリンクされます (AWS VU9P V1)。ハードウェア エミュレーション用にデザインをビルドするには、コンピューターの設定によりますが、5 分以上かかる場合があります。

* 実行が完了すると、**\[Console]** ビューにカーネルとグローバル メモリ間のデータ転送のサマリが表示されます。

  * このサマリには、各 DDR バンクに対し、読み出され、書き込まれたデータの量がキロバイトで表示され、また、各カーネル ポートによって読み出され、書き込まれたデータ量が表示されます。

* このデータ転送サマリだけでなく、ハードウェア エミュレーションを実行すると、さまざまなパフォーマンスおよびプロファイルのレポートが生成されます。

  > 注記: ハードウェア エミュレーションで使用されるシミュレーション モデルは概算です。プロファイルの数値は見積もり値であって、実際のハードウェアで得られる結果とは異なる可能性があります。

* **\[Assitant]** ビューで (GUI の左下) **\[Emulation-HW]** を展開し、その下にあるすべての項目を表示させます。

  * **\[HLS Report]** (\[Emulation-HW] → \[binary\_container\_1] → \[vadd]) では、SDAccel コンパイラによりハードウェアにインプリメントされたカーネルについて、タイミング、パフォーマンス、リソース使用率などの詳細情報を確認できます。

  * **\[Profile Summary]** (\[Emulation-HW] → \[Test-Default]) では、カーネル操作、データ転送、OpenCL API 呼び出し、およびリソース使用率に関連したプロファイル情報、カーネルとホスト間のデータ転送を確認できます。

  * **\[Application Timeline]** (\[Emulation-HW] → \[Test-Default]) では、OpenCL API 呼び出し、データ転送、カーネル実行のタイムラインを視覚的に確認できます。このハードウェア エミュレーションのタイムラインは、ソフトウェア エミュレーションのものとは表示が異なります。API 呼び出しおよびカーネル起動のシーケンスは実際には同じですが、ハードウェア エミュレーションはより正確なパフォーマンス見積もりで操作するため、カーネルがキューにある時間がより実際に近い見積もり値でタイムラインに表示されます。

* これらのレポートに加え、SDAccel では、**\[Console]** の横にある **\[Guidance]** ビューで解析レポートも表示できます。

* **\[Guidance]** ビューをクリックし、**\[Maximize]** アイコンをクリックして、フル レポートを表示させます。

  * SDAccel では、主なパフォーマンス条件が満たされたかどうかが記載されているコンパイルおよび実行ログが生成されます。これらのレポートには、条件が満たされたかどうかの結果が報告され、必要であればアプリケーションの改善方法が提案されます。
  * レポートの冒頭の **\[Host Data Transfer]** セクションでは、ホストとデバイス間のデータの読み出しおよび書き込みにアプリケーションが効果を発揮していることが確認できます。
  * ただし、**\[**Kernel Data Transfer**]** セクションでは、アプリケーションですべての DDR バンクが活用しきれていないこと (4 つのうち 1 つしか使用されていない)、カーネル ポートのデータ幅が最適化されていないこと (推奨されている 512 ビットではなく 32 ビットであること) が確認できます。
  * **\[KERNEL\_PORT\_DATA\_WIDTH #1]** 行を選択し、**\[Resolution]** 列の右側を確認し、ガイダンス メッセージをクリックして推奨事項を確認します。

* **\[**Guidance**]** ビューの右上のアイコンをクリックして、通常のサイズに**復元**します。

## FPGA バイナリおよび Amazon FPGA イメージ (AFI) のビルド

アプリケーション プロファイルに問題がないようなので、ハードウェア プラットフォームで実行するためにこれをコンパイルします。

* **\[SDx Project Settings]** に移動し、**\[Active build configuration]** を **\[System]** に設定します。

* **\[Build]** ボタンをクリックして、ハードウェア ビルド プロセスを開始します。

  > 重要: ハードウェア ビルドを完了させるには通常数時間かかります。

* ビルド プロセスが完了したら、`/home/centos/helloworld_c/workspace/Test/System` ディレクトリでホスト実行ファイル (`Test.exe`) および FPGA バイナリ (`binary_container_1.xclbin`) を検索します。

* SDAccel GUI を終了します。

* AWS の **create\_sdaccel\_afi.sh** スクリプトを使用して、FPGA バイナリから AFI を作成します。

  ```bash
  cd /home/centos/helloworld_c/workspace/Test/System
  $SDACCEL_DIR/tools/create_sdaccel_afi.sh
      -xclbin=binary_container_1.xclbin
      -s3_bucket=<bucket-name>
      -s3_dcp_key=<dcp-folder-name>
      -s3_logs_key=<logs-folder-name>
  ```

* **create\_sdaccel\_afi.sh** スクリプトは、次を実行します。

  * AFI を作成するバックグラウンド プロセスを開始します。
  * 生成した AFI の FPGA イメージ識別子 (AFI ID) およびグローバル FPGA イメージ識別子 (AGFI ID) を含む `\<timestamp\>_afi_id.txt` ファイルを生成します。
  * ホスト アプリケーションがどの AFI を FPGA に読み込むのかを指定する `\*.awsxclbin` AWS FPGA バイナリ ファイルを作成します。

* `\<timestamp\>_afi_id.txt` ファイルを開いて、AFI ID の値をメモします。

  ```bash
  cat *.afi_id.txt
  ```

* **describe-fpga-images** API を使用して、AFI 生成プロセスのステータスを確認します。

  ```bash
  aws ec2 describe-fpga-images --fpga-image-ids <AFI ID>
  ```

* バックグラウンドで開始された AFI 作成プロセスは、すぐには終了しません。プロセスが完了してからでないと、F1 インスタンスでは実行できません。AFI が問題なく作成されたら、次が表示されます。

  ```json
  ...
  "State": {
      "Code": "available"
  },
  ...
  ```

* F1 インスタンスでアプリケーションを実行する前に、AFI が使用可能になるのを待ちます。

## F1 でアプリケーションを実行

* インスタンス ターミナルで次のコマンドを実行します。

  ```bash
  cd /home/centos/helloworld_c/workspace/Test/System
  sudo sh
  source /opt/xilinx/xrt/setup.sh   
  ./Test.exe
  ```

## まとめ

このチュートリアルを終了すると、次ができるようになります。

* SDAccel プロジェクトを作成し、必要なデザイン ファイルをインポート。
* デザインのバイナリ コンテナーとアクセラレータを作成。
* ソフトウェアおよびハードウェアのエミュレーションを実行し、この 2 つのモードの違いを理解。
* デバッグ環境を使用。
* アプリケーションをプロファイルするため、生成されたレポートを参照。
* Amazon FPGA イメージを作成し、F1 上でそれを実行。

<hr/>
<p align="center"><b><a href="STEP3.md">次へ: AWS F1 および SDAccel C/OpenCL フローを詳細に学ぶ</a></b></p><br><hr/>
<p align="center"><sup>Copyright&copy; 2019 Xilinx</sup></p>
