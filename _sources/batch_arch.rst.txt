=========================
AWSにおけるBatch関連のサービス
=========================


AWS Batch
====
AWS Batchの利用が適しているパタ－ン
----
AWS Batchは以下のうち、 **「大規模計算におけるバッチ処理」** に適している。

* 定型業務におけるバッチ処理
    夜間バッチのような、あらかじめジョブの起動や順序を定義しておいて、ジョブを実行・管理する。
* 大規模計算におけるバッチ処理
    大規模科学計算、機械学習、ゲノム分析等のアドホックな計算。

AWS Batchを利用できる条件
----
* Dockerコンテナイメージを用意する必要がある。

AWS Lambda
====
Lambdaの利用が適しているパターン
----
* 1つのジョブが数秒など短時間で終わる場合
    * 実行時間は15分が上限

Lambdaで動作するAP
----
* SpringBootの導入
    * https://qiita.com/_kensh/items/603b16e31e787c856395

ECS Task
====


AWS Step Functions
====


参考情報
====
* AWS Batch Black Belt
    * https://www.slideshare.net/AmazonWebServicesJapan/20190911-aws-black-belt-online-seminar-aws-batch

* AWS Lambda Black Belt
    * https://www.slideshare.net/AmazonWebServicesJapan/20190402-aws-black-belt-online-seminar-lets-dive-deep-into-aws-lambda-part1-part2/

* AWS Step Functions Black Belt
    * https://www.slideshare.net/AmazonWebServicesJapan/20190522-aws-black-belt-online-seminar-aws-step-functions

* 各サービスの比較
    * https://techblog.timers-inc.com/entry/2019/08/06/aws-batch-lambda-ecs-comparison
