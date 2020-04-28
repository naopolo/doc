=========================
環境構築
=========================

Docker Image作成
====
* mavenビルド成功後にtargetフォルダ以下で実行
    docker build ./ -t batchdemo
* Docker Imageの実行確認
    docker run batchdemo
* Dockerプロセスの全表示
    docker ps -a
* Docker Imageの全表示
    docker images


ECR構築
====
事前にECRへのアクセス権限を持ったIAMロールのクレデンシャルを取得しておくこと

* ECR接続用のprofile作成
    aws configure --profile ecr
* ECRにログイン
    * aws ecr get-login --region [リージョン] --profile ecr --no-include-email
    * 返却されたコマンド文字列を実行するとログインできる
* ECR push様のtag名を作成
    * docker tag batchdemo [ログインID].dkr.ecr.[リージョン].amazonaws.com/batchdemo:latest
* ECRへpush
    * docker push [ログインID].dkr.ecr.[リージョン].amazonaws.com/batchdemo:latest

AWS Batch構築
====
一通りデフォルトで構築して、以下のみ変更

* ジョブ定義－リソース要件－コンテナ定義
    * リリースしたいDocker ImageのECR上のパス
        * [ログインID].dkr.ecr.[リージョン].amazonaws.com/batchdemo:latest
    * ジョブロールの設定
        * 以下のサイトを参考にAWS Systems Manager パラメータストアへのアクセス権限をもつアクセスポリシーが付与されたロールを設定すること
            * https://docs.aws.amazon.com/ja_jp/systems-manager/latest/userguide/sysman-paramstore-access.html