=========================
構築するアプリケーション(Spring Boot)
=========================
アプリケーションのベースとなるFW
====
Spring Boot + Spring Batchで構成する

アプリケーションの仕様概要
====
* 処理内容
    * S3上に保存されたファイルの内容を解析し、DB（RDS）に保存する【TODO】
* 細かな仕様
    * DBアクセス
        * MyBatisを用いてRDS（PosgtreSQL）にアクセス【TODO】
    * ファイルアクセス
        * S3
            * AWS SDKを利用【TODO】
        * その他
            * Java File API【TODO】
    * パラメータ管理
        * applivcation.ymlとAWS System Manager（パラメータストアの併用）【TODO】
    * マルチスレッド
        * taskletモデルとchunkモデルAP作成【TODO】
* APのリポジトリ
    * https://github.com/naopolo/batchdemo

Spring Batchのアーキテクチャ
====

