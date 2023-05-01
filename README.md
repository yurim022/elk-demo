# elk-demo

elk 환경에서 batch 로 실행한 데이터가 logstash로 전송되고 이를 elastic 에 저장 - kibana로 확인하는 기본적인 구성.   
[docker-elk](https://github.com/yurim022/docker-elk-custom) 로 구성한 elk 클러스터 (단일모드) 를 활용한다.


<img width="863" alt="image" src="https://user-images.githubusercontent.com/45115557/235416632-4006146b-2afc-4327-901d-8985e8e12ea0.png">

본 프로젝트에서는 spring batch로 로그를 발생시킨 뒤 이를 추가한 플러그인을 통해 logstash로 보내도록 한다. 

### gradle에 추가

```
    implementation 'net.logstash.logback:logstash-logback-encoder:6.3'
```

### logback.xml 일부

logstash에 tcp 방식으로 전송한다.

```java

    <!-- 추가한 'net.logstash.logback:logstash-logback-encoder:6.3' 를 통해 logstash로 전송 -->
    <appender name="stash" class="net.logstash.logback.appender.LogstashTcpSocketAppender">
        <destination>127.0.0.1:5000</destination>

        <!-- encoder is required -->
        <encoder class="net.logstash.logback.encoder.LogstashEncoder" />
    </appender>

```


### application.yml 

```
logging.config: classpath:logback.xml
```

<br><br>

[docker-elk](https://github.com/yurim022/docker-elk-custom) logstash의 config 파일을 다음과 같이 수정해준다. 

```
input {

        tcp {
                port => 5000
                codec => json_lines
        }
}

output {
        elasticsearch {
            hosts => "elasticsearch:9200"
            index => "logstash-20230430"
            user => "elastic"
            password => "changeme"
         }
        } 
```
