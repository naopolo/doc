AWS Batch
====
AWS Batchの環境構成図
----
.. image:: ../img/AWS_Batch.png
   :scale: 50%
   :align: center

AWS Batchの利用が適しているパタ－ン
----
* 複数ジョブのシーケンシャル実行
    * ジョブの実行順序が固定されており、順番通りに実行していくジョブ。
* 特定の前処理ジョブ完了後に一括して複数のジョブを実行する
    * 
* 複数のジョブがすべて完了後に後処理ジョブを実行する
    * 
* 大規模計算におけるバッチ処理
    * 大規模科学計算、機械学習、ゲノム分析等のアドホックな計算。
    * 機械学習用途でモデル設計から推論エンドポイントまで一気通貫で行いたい場合はAmazon SageMakerの利用を検討

AWS Batchより他のサービス利用が適しているパターン
----
* 現行のバッチAPが存在し、そのジョブ管理機能（スケジューラ）の移行が難しい場合
   * AWS ParallelCluster(AWS がサポートするオープンソースのクラスター管理ツール)の利用を検討
   * https://docs.aws.amazon.com/ja_jp/parallelcluster/latest/ug/aws-parallelcluster-ug.pdf
* 1つのジョブが短時間で完了する軽い処理の場合
   * LambdaやStepFunctionの利用を検討する
* コンテナイメージが用意できないアプリケーション
   * EC2上でのAP起動を検討する。

ジョブの起動方法
----
* Management Console
   * AWS BatchのGUIからの起動
* AWS CLI
   * https://docs.aws.amazon.com/cli/latest/reference/batch/index.html
* Cloud Watch Event
   * スケジュールされた通りに定期実行
* Lambda関数
   * S3のファイル行進などをトリガーにLambda関数を起動し、関数内でJobキューに登録する

AWS Batchを利用できる条件
----
* Dockerコンテナイメージを用意する必要がある。