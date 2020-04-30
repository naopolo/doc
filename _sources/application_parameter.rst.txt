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

        * AWS Systems Manager パラメータストアの設定方法

            .. image:: ../img/parameterstore.svg
               :scale: 50%
               :align: center


        * application.ymlで同様の項目を管理する方法

            .. sourcecode:: yaml
                :linenos:

                naopolo:
                    batch:
                        param01: local execute

    * 参考サイト
        * https://cloud.spring.io/spring-cloud-static/spring-cloud-aws/2.0.0.RELEASE/multi/multi__cloud_environment.html#_integrating_your_spring_cloud_application_with_the_aws_parameter_store
        * https://wisdom-dev.jp/?p=29