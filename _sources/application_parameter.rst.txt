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

                <dependency>
                    <groupId>org.springframework.cloud</groupId>
                    <artifactId>spring-cloud-starter-aws-parameter-store-config</artifactId>
                </dependency>
        
        * bootstrap.ymlをapplication.ymlと同じディレクトリに作成
            
            .. sourcecode:: yaml
                :linenos:
                
                aws:
                    paramstore:
                        default-context: application
                        enabled: true
                        prefix: /config
                        name: batchdemo
                        profile-separator: '-'
                        #parameter storeに接続できなくてもAPをエラーにしない
                        fail-fast: false
                

    * 参考サイト
        * https://cloud.spring.io/spring-cloud-static/spring-cloud-aws/2.0.0.RELEASE/multi/multi__cloud_environment.html#_integrating_your_spring_cloud_application_with_the_aws_parameter_store
        * https://wisdom-dev.jp/?p=29