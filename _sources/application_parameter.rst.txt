パラメータ管理
====
* Springによるパラメータ管理
    * application.properties(yml)を用いて、パラメータを別ファイルに切り出し管理することが可能
    * プログラム中では@Valueを用いて参照可能
* AWS Systems Manager パラメータストアによる管理
    * AWSのマネージドサービスによりパラメータを管理し、アプリケーションから参照する
    * Spring Cloud(spring-cloud-starter-aws-parameter-store-config)を用いて参照する
        * pom.xmlに以下の依存関係を追加
            
            .. sourcecode:: xml
                :linenos:

                <!-- バージョンは必要に応じて変更する -->
                <dependencyManagement>
                    <dependencies>
                    <!-- https://mvnrepository.com/artifact/org.springframework.cloud/spring-cloud-dependencies -->
                        <dependency>
                            <groupId>org.springframework.cloud</groupId>
                            <artifactId>spring-cloud-dependencies</artifactId>
                            <version>Greenwich.SR2</version>
                            <type>pom</type>
                            <scope>runtime</scope>
                        </dependency>
                    </dependencies>
                </dependencyManagement>
                
                <dependencies>
                    <!-- omitted -->
                    <dependency>
                        <groupId>org.springframework.cloud</groupId>
                        <artifactId>spring-cloud-context</artifactId>
                        <version>2.1.2.RELEASE</version>
                    </dependency>
                    <dependency>
                        <groupId>org.springframework.cloud</groupId>
                        <artifactId>spring-cloud-starter-aws-parameter-store-config</artifactId>
                        <version>2.1.2.RELEASE</version>
                    </dependency>
                </dependencies>
        
        * bootstrap.ymlをapplication.ymlと同じディレクトリに作成
            
            .. sourcecode:: yaml
                :linenos:
                
                aws:
                  paramstore:
                    #プロパティを定義するコンテキストの名前,nameの定義に該当するものがない場合のデフォルト値
                    default-context: application
                    #パラメータストアからの情報取得有効
                    enabled: true
                    #パラメータストアから取得するパラメータ名のprefix
                    prefix: /config
                    #パラメータ名のprefixに続くAP名
                    name: batchdemo
                    #AP名の後ろにプロファイル名を設定でき、AP名とプロファイル名の間のせパレータ
                    profile-separator: '-'
                    #parameter storeに接続できなくてもAPをエラーにしない
                    fail-fast: false
                
        * APからの参照方法

            .. sourcecode:: java
                :linenos:

                import org.springframework.beans.factory.annotation.Value;

                //omitted

                @Component
                public class Tasklet01 implements Tasklet {
                    // naopolo.batch.param01という項目のパラメータを取得し、param01に設定する
                    // application.ymlで同一項目が規定されていた場合、パラメータストアで上書く
                    @Value("${naopolo.batch.param01:default}")
                    String param01;

                    @Override
                    public RepeatStatus execute(StepContribution stepContribution, ChunkContext chunkContext) throws Exception {
                        System.out.println("tasklet1!! : " + param01);
                        return RepeatStatus.FINISHED;
                    }
                }

        * application.ymlで同様の項目を管理する方法

            .. sourcecode:: yaml
                :linenos:

                naopolo:
                    batch:
                        param01: local execute
    
    * AWS Systems Manager パラメータストアの設定
        * 名称付与ルール
            * **[prefix]/[apname][separator][profile]/[key]**

        * 各項目説明   
            * prefix
                aws.paramstore.prefixに対応。
                本項目に設定した値がaws.paramstore.prefixと等しくなっている値をアプリケーションが読み込む。
            * apname
                aws.paramstore.nameに対応。
                本項目に設定した値がaws.paramstore.nameと等しくなっている値をアプリケーションが読み込む。
                もし、aws.paramstore.nameがアプリケーション側で設定されていなかった場合、
                aws.paramstore.default-contextと本項目の値が等しい場合にアプリケーションが読み込む。
            * separator
                aws.paramstore.separatorに対応。
                アプリケーション起動時に指定するプロファイルとアプリケーション名のセパレータを設定する。
                profileを指定しない場合、本項目は省略する。
            * profile
                aws.paramstore.profileに対応。
                アプリケーション起動時に指定するプロファイル名を設定する。
                profileを指定しない場合、本項目は省略する。
            * key
                アプリケーションで設定するパラメータの項目名に対応。 
        * 設定例
            上記のbootstrap.ymlやapplication.ymlのサンプルを設定した場合の、パラメータストアの設定例。
            profile未指定の場合と、prodを指定した場合で読みかえるようにしている。

            .. image:: ../img/parameterstore.png
               :scale: 80%
               :align: center

        * IAMポリシーの設定
            AWS Systems Manager パラメータストアにアクセスるためのIAMポリシーを作成し、
            そのポリシーを設定したIAMロールをアプリケーションがデプロイされるインスタンスに設定する。
            以下、設定例(/configで始まるパラメータを取得することができるポリシー)。

            .. sourcecode:: json
                :linenos:

                {
                    "Version": "2012-10-17",
                    "Statement": [
                        {
                            "Effect": "Allow",
                            "Action": [
                                "ssm:DescribeParameters"
                            ],
                            "Resource": "*"
                        },
                        {
                            "Effect": "Allow",
                            "Action": [
                                "ssm:GetParameters"
                            ],
                            "Resource": "arn:aws:ssm:[リージョン]:[ログインID]:parameter/config*"
                        }
                    ]
                }
        

    * 参考サイト
        * Spring Cloud
            https://cloud.spring.io/spring-cloud-static/spring-cloud-aws/2.0.0.RELEASE/multi/multi__cloud_environment.html#_integrating_your_spring_cloud_application_with_the_aws_parameter_store
        * System Manager Parameter Store から設定値を取得し application.properties を上書きする
            https://wisdom-dev.jp/?p=29
        * Systems Manager パラメータへのアクセス制御
            https://docs.aws.amazon.com/ja_jp/systems-manager/latest/userguide/sysman-paramstore-access.html