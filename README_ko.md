# 제니퍼 서버 어댑터

제니퍼 서버 어댑터란 트랜잭션 데이터나 EVENT 알림을 실시간으로 처리할 수 있는 외부 모듈을 말한다.제니퍼 서버 어댑터를 이용해 공개 API와 마찬가지로 E-Mail이나 SMS 모듈 등과 연동하여 다양한 서비스를 만들 수도 있다. 또한 환경에 따라 데이터 양이 많을 수도 있는 실시간 트랜잭션 데이터를 제니퍼 서버 성능에 별다른 영향없이 백업을 하거나 커스터마이징 하여 사용할 수도 있다.

### 버전 요구사항

본 문서는 제니퍼 서버 버전 5.4.0을 기준으로 작성되었다.


## IntelliJ에서 어댑터 개발환경 구성하기

어댑터 모듈을 구현하기 위해서는 별도의 프로젝트를 생성하고, JENNIFER_VIEW_SERVER_PATH/lib/jennifer.server.view-5.0.x.x.jar의 경로를 Java Build Path에 추가한다.

1. File > New > Project... > Java를 선택하여, 새로운 프로젝트를 생성한다.
2. 컴파일 환경 구성을 위해 File > Project Structure... > Project Settings > Libraries에서 추가 버튼을 클릭하여 이미 설치된 제니퍼 뷰서버 jar 파일을 선택한다.
![이미지](https://raw.githubusercontent.com/jennifersoft/jennifer-extension-manuals/master/res/img/view_server_adapter/1.png)
3. 빌드 파일 생성을 위해 File > Project Structure... > Project Settings > Artifacts에서 추가 버튼을 클릭하여 Jar > From modules with dependencies...를 클릭한다.
![이미지](https://raw.githubusercontent.com/jennifersoft/jennifer-extension-manuals/master/res/img/view_server_adapter/2.png)
4. Output Layout 좌측에 Extracted 'jennifer.server.view-5.3.x.jar/ ...'를 선택하고, 삭제 버튼을 클릭한다. 어차피 어댑터 jar 파일은 제니퍼 뷰서버에서 로드되기 때문에 굳이 2번 컴파일 환경 구성을 위해 추가해둔 제니퍼 뷰서버 jar파일을 빌드 시점에 포함하지 않아도 된다. 
![이미지](https://raw.githubusercontent.com/jennifersoft/jennifer-extension-manuals/master/res/img/view_server_adapter/3.png)
5. 어댑터 클래스를 가이드대로 개발한 후, Build > Build Artifacts... > Action > Build를 통해 jar 파일로 빌드할 수 있다.


## 어댑터 핸들러 인터페이스 구현하기

어댑터 클래스를 구현하기 위해서는 com.aries.view.extension.handler.Adapter 인터페이스를 구현해야 하며, on 메소드의 com.aries.view.extension.data.Model 객체 배열로 데이터를 받을 수 있다.

### X-View 트랜잭션 어댑터

실시간 X-View 차트에 나오는 트랜잭션 데이터를 어댑터 핸들러를 통해 실시간으로 받을 수 있다. 예를 들어 트랜잭션 데이터를 제니퍼 DB만이 아닌 별도의 데이터베이스에 저장하고 싶을 때, 어댑터 핸들러에 관련된 코드를 추가하면 된다. X-View 트랜잭션 어댑터 클래스 코드는 다음과 같다.

    package adapter;

    import com.aries.view.extension.handler.Adapter;
    import com.aries.view.extension.data.Model;
    import com.aries.view.extension.data.Transaction;
    import com.aries.view.extension.util.LogUtil;

    public class XViewAdapter implements Adapter {
        public void on(Model[] messages) {
            for(int i = 0; i < messages.length; i++) {
                Transaction model = (Transaction) message[i];

                // 트랜잭션 모델을 참조하여 핸들러 구현하기
                LogUtil.info("도메인 아이디: " + model.getDomainId());
                LogUtil.info("인스턴스 이름: " + model.getInstanceName());
                LogUtil.info("트랜잭션 아이디: " + model.getTxid());
                LogUtil.info("응답시간: " + model.getResponseTime());
                LogUtil.info("애플리케이션: " + model.getApplicationName());
            }
        }
    }

아래는 Transaction 클래스의 프로퍼티 목록이다.

| 변수 타입 | 프로퍼티 이름 |
|:-------|-------:|
| short | domainId |
| String | domainName |
| int | instanceId |
| String | instanceName |
| String | guid |
| String | clientIp |
| long | clientId |
| String | userId |
| int | networkTime |
| int | frontendTime |
| long | startTime |
| long | endTime |
| int | responseTime |
| int | cpuTime |
| int | sqlTime |
| int | fetchTime |
| int | externalcallTime |
| String | errorType |
| String | applicationName |
| long | txid |

### Event 알림 어댑터

EVENT 발생 시점에 관련된 데이터를 어댑터 핸들러를 통해 받기 위해서는 [관리 > EVENT 룰] 메뉴에서 설정된 값의 외부연동이 활성화되어 있어야 한다. EVENT 알림 어댑터 클래스 코드는 다음과 같다.

    package adapter;
    
    import com.aries.view.extension.handler.Adapter;
    import com.aries.view.extension.data.Model;
    import com.aries.view.extension.data.Event;
    import com.aries.view.extension.util.LogUtil;
    
    public class EventAdapter implements Adapter {
        public void on(Model[] messages) {
            for(int i = 0; i < messages.length; i++) {
                Event model = (Event) message[i];

                // EVENT 모델을 참조하여 핸들러 구현하기
                LogUtil.info("도메인 아이디: " + model.getDomainId());
                LogUtil.info("인스턴스 이름: " + model.getInstanceName());
                LogUtil.info("트랜잭션 아이디: " + model.getTxid());
                LogUtil.info("서비스 이름: " + model.getServiceName());
                LogUtil.info("에러 유형: " + model.getErrorType());
                LogUtil.info("이벤트 심각도: " + model.getEventLevel());
            }
        }
    }

아래는 Event 클래스의 프로퍼티 목록이다.

| 변수 타입 | 프로퍼티 이름 |
|:-------|-------:|
| short | domainId |
| String | domainName |
| int | instanceId |
| String | instanceName |
| long | time |
| String | errorType |
| String | metricsName |
| String | eventLevel |
| String | message |
| double | value |
| String | otype |
| String | detailMessage |
| String | serviceName |
| long | txid |


### 어댑터 사용자정의 옵션 사용하기

제니퍼 뷰서버의 관리 > 어댑터 및 실험실에서 직접 구현한 어댑터를 추가할 수 있는데, 이때 ID를 필수적으로 입력해야한다. 이 값은 어댑터 핸들러를 구현할 때, 사용자정의 옵션을 가져오기 위한 ID이다.
![이미지](https://raw.githubusercontent.com/jennifersoft/jennifer-extension-manuals/master/res/img/view_server_adapter/4.png)

추가한 어댑터를 선택하면 사용자정의 옵션을 설정할 수 있는 버튼이 노출된다. 예전에는 별도로 properties 파일을 만들어서 옵션을 어댑터에서 직접 참조했었는데, 이제는 제니퍼 뷰서버 관리 화면을 통해 동적으로 추가/수정/삭제할 수 있다.
![이미지](https://raw.githubusercontent.com/jennifersoft/jennifer-extension-manuals/master/res/img/view_server_adapter/5.png)

설정된 사용자정의 옵션들은 아래와 같이 어댑터 핸들러 구현시 사용할 수 있다. 첫번째 변수는 앞에서 어댑터를 추가할 때, 입력한 ID이며, 두번째 변수는 사용자정의 옵션키이다. 마지막 세번째 변수는 해당 키의 값이 없을 경우에 대신 추가되는 기본값이다.

    package event;

    import com.aries.view.extension.data.Event;
    import com.aries.view.extension.data.Model;
    import com.aries.view.extension.handler.Adapter;
    import com.aries.view.extension.util.PropertyUtil;
    import com.aries.view.extension.util.LogUtil;

    public class LogAdapter implements Adapter {
        @Override
        public void on(Model[] messages) {
            String option = PropertyUtil.getValue("eventlog", "full_path", "default_value");

            for(int i = 0; i < messages.length; i++) {
                Event model = (Event) messages[i];
                LogUtil.info(model.getErrorType() + " : " + option);
            }
        }
    }