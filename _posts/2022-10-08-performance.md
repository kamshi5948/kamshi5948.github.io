##### 참고자료

## JVM
* Heap dump 분석 : IBM heapAnalyzer, MAT
* Thread dump 분석 : stack
* GC Log 분석 : GC Viewer
* 통합모니터링 : jconsole, jstat, jvmstat
* 성능/오류 로그 분석 : Xlog 
* 서버 - CPU : prof, perf
* 서버-메모리 : Memory leak 분석


## 장애원인
* Application
	* 대용량 데이터 조회 시 OOM 발생
	* HTTP/DB 커넥션풀 비정상 커넥션 체크 정리 옵션 누락
	* 코딩 실수
	* 프로그램 성능을 고려한 로직 구현 미숙(loop 내 건별 처리)
	* 채번에 의한 LOCK
	* 트랜잭션 보유한 채 외부 API 호출
 * DB
	 * 트랜잭션 처리방식에 대한 이해부족
	 * WAS 제공 datasource 대신 직접 DB 커넥션 연결 후 사용
	 * SQL bind 변수 미처리
* 서버 및 용량
	* 이미지 다운로드 용량에 의한 네트워크 대역폭 부족
	* PEAK 시점 서버 자원 부족
* DB
	* DB lock
	* DB 업그레이드 후 기존 프로시저 비정상 동작
	* Bad SQL에 의한 성능 저하
	* DB 구조 설계 미흡
	* 연계 DB - DB 어답터에 의한 과도한 커밋 -> 로그 스위칭 과다 발생
* 기타
	* 보안/관리 툴에 의한 병목
	* 솔루션 버그에 의한 오류, 자원 점유
	* 외부 인터페이스 성능 제약
 * 환경구성
	 * WEB,WAS,DB 이중화 구성/설정 미흡(Datasource VIP 대신 Real IP 사용)
	 * WEB_WAS 연동 설정 오류
	 * Cloud scale-out/fail-over 설정 오류
	 * WAS thread, heap memory, network 파라미터, db 파라미터
	 * LB 알고리즘 고려 부족으로 불균등 분산

## 장애사례
* 네트워크
	* A1 -> A2 파일전송(정상), A2 -> A1 파일전송(1/3)
	* tcpdump wireshark 분석(switch 장비 문제 -> 교체필요)

* sys cpu 비중 매우 높음 -> kernal에서 사용하는 함수/서비스의 비중 높다는 것
	* get file status -> class 파일 상태를 반복적으로 조회 -> WAS 설정 파일 검토
	* WAS 자동 reload 설정 true 
	* WAS 인스턴스에 포함된 솔루션 agent가 설정 이상으로 반복적으로 log파일에 기록하기 위해서 file 상태에 대한 조회 call 수행, 로그 레벨이 debug 모드 되어 있어 system call 대한 처리가 가중되어 sys cpu 사용율이 증가하고 응답시간이 저하됨
* 동시접속자 증가 시 web서버의 sys cpu 증가로 인한 장애 발생
	* https와 was 간 cluster 구성 정보 file에 대한 lock과 관련 해당 file에 접근하는 프로세스 수, thread 수와 밀접한 연관이 있음
	* file lock 경합을 줄이기 위해 apache 설정 중 프로세스 max 수치를 40개 미만으로 변경
	* 서버 1대당 처리량을 줄이기 위해 web서버를 수평확장, 사용자 추가 분산(리전 재분배)
* Public Cloud 자체 구성 요소의 병목에 의한 장애
	* DB접속/처리 구간 지연으로 장애 발생
	* WAS - SQL GATEWAY - DB(Managed service mysql)
	* DB접속 및 처리를 담당하는 gateway와 해당 network 구간에서 지연 발생
	* CSP SQL gateway 증설
* 배치기동 성능 개선
	* Multi Datasource에서 SQL XML 파일 중복 로딩에 따른 불필요 CPU 사용
	* 배치 기동 시간 분산
	*  SQL XML 중복 로딩 -> lazy loading 적용(배치는 기동 시점에 로딩할 필요성이 적음)
* 검색 인덱싱 부하로 인한 5XX 에러 이슈
* Thread Pool 사용 방식에 따른 성능 및 안정성 이슈
	* 외부 API 요청이 있을때마다 thread 내부에서 별도의 thread pool을 생성하여 사용 -> 요청이 늘어날 때마다 Executor Thread Pool이 생성되고 CPU와 메모리 사용이 가중되면서 OutOfMemoryError가 발생할 수 있음
	* 공용 Thread Pool을 사용하도록 변경
* 클라우드 이관 후 RTT증가로 인해 특정 업무 저하 현상
	* 서버 위치 별 화면 응답시간 비교(tcpdump) -> 패킷 분석 결과 현재시간정보를 조회하는 SQL 다량 수행
	* 선택 대상의 시간대 차이 등으로 인해 SQL 수행횟수가 다소 상이하고, SQL수행횟수 차이로 인해 SQL 수행 시간 Gap은 다소 증가된 상태
	* RTT 차이만큼 발생
	* 통상 시간정보 조회 SQL은 AP와 DB의 시간이 다른 경우를 대비하기 위함
	* AP 내부적으로 OS커맨드/API를 통해 얻는 방법이 바람직함
	* JDBC Fetch 사이즈 기본값(10)을 증가시켜 network로 주고 받을 때 한번에 많이 가져 올 수 있도록 개선하여 응답시간을 향상 시켜야함
	* 캐시 적용(실시간 정보가 필요하지 않은 데이터에 대해서는 응용 내부적으로 최대한 캐시처리하여 최대한 DB를 조회하지 않고 network로 데이터를 주고 받지 않게 하는 것이 성능에 도움이 됨
	* SQL 통합(응용 수정이 가능한 경우 최대한 SQL 한번에 처리할 수 있도록 개선)
	* 화면조건제한/페이징 처리( SQL 수행 횟수는 적더라도 가져오는 데이터 양이 많다면 성능 지연 가능성이 존재함, 응용 화면에서 조회 조건을 제한하여 가져오는 양을 줄이거나, 페이징 처리를 추가함)
* 네트워크 패킷 재전송으로 인한 파일 다운로드 속도 저하현상
	* 게시판 첨부파일 다운로드 속도에 비해, 메일 수신함의 첨부파일 다운로드 속도가 현저하게 느린 현상
	* 웹메일 다운로드 시 TCP retransmission으로 인한 지연 현상(OS or NW 구간 이슈 판단)
	* 서버 네트워크 파라미터 수정
* 물리자원을 공유하는 타 업무 VM의 부하에 의한 성능 지연
	* 특정시간대 주기적으로 5초 이상의 지연 발생
	* 각 구간 별 자원 사용률 및 수행시간 점검(서버, 스토리지, DB, AP)
	* AP <-> DB간 해당 시간대의 일부 서버에서 DB데이터 전송 및 Ping 응답시간 증가 확인
	* 동일한 물리자원을 사용하는 타 업무의 DB에서 해당 시간대에 백업이 수행 되면서 네트워크 자원을 점유하여 병목 발생(네트워크 대역폭 소진)
	* VM을 독립된 물리장비로 이전하여 네트워크 병목 해소
* 네트워크 대역폭 초과로 인한 성능 지연
	* Private Cloud의 외부로 전송되는 대역폭이 100Mbps로 제한 걸려 있음
	* Access log로 서버의 네트워크 사용비율을 확인한 결과 font가 80% 비중
	* 네트워크 대역폭 100 -> 300Mbps 증설
	* font 경량화 및 캐싱 적용
* DB 백업 시 NIC 병목에 의한 성능 지연
	* 트랜잭션 구간 상세 분석을 수행(동일한 두 트랜잭션 분석 결과 특정 구간이 아닌 각 구간 전체의 처리 시간이 선형적으로 증가하는 것으로 판단할 때 자원 이슈가 원인일 가능성이 큼)
	* 서버 및 DB 자원 사용률 점검(DB서버 NIC의 네트워크 패킷 전송량이 20분 주기로 급격하게 증가 -> NIC 교체)
* 대량 I/O로 인한 성능 저하
> Disk I/O 급증 -> DB서버의 CPU 사용량 증가  
> 대용량 테이블 Full Scan -> Index Scan 되도록 SQL 튜닝  
* NLB idle Connection Timeout 에 의한 인터페이스 오류 발생
> 30분 동안 기간과 WEB서버간 트랜잭션 없어서 WEB서버에서 keepalive 패킷을 전송하지만, RST 응답이 오고, 신규 세션을 생성함  
> 대외기간과 WEB서버간 네트워크 장비에서 Connection이 끊겼을 것으로 추정 -> 방화벽, NLB의 idle timeout 값 조사   
> NLB timeout 보다 WEB 서버의 keep-alive 파라미터의 값이 작아야 함  
> 서버의 커널 파라미터(net.ipv4.tcp_keepalive_time_을 default 2시간에서 5분으로 변경하여 5분마다 keep-alive packet을 전송하여 세션을 유지하도록 함  
* Binary Log I/O 지연에 따른 MariaDB 성능 저하
> MariaDB의 binary log는 replcation과 복구에 사용, 해당 파일의 IO성능은 DBMS 성능과 밀접한 관련이 있으므로 성능이 빠른 Local Disk로 변경  
*
1. 비효율적인 아키텍처 구조 및 부족한 용량에 따른 시스템 처리 한계
> 대규모 거래 집중으로 인한 DB CPU 병목  
> DB서버 CPU 자원이 부족하여 시스템 증설 방안 필요   
> WAS의 DB 연결 설정이 미흡하여 DB1에 부하가 집중, WAS Thread 개수 및 DB 연결 개수 설계 필요  
> * DB서버 1대당 목표처리건수 = 목표TPS처리건수 / DB서버수  
> * DB서버 1대당 Connection 수 = DB서버 1대당 목표처리건수 * SQL응답시간(3초)  
> * WAS 응답시간 중 DB비율 = 70%  
> * WAS 1대당 Thread 수   
2. DB서버 네트워크 및 스토리지 병목으로 인한 지연 방지
> 대규모 거래 집중 시 DISK I/O 부하 증가  
> DB에서 DML SQL 지연 및 log file sync wait event 관측 -> DISK I/O 병목 판단  
> DB서버간 통신을 담당하는 Private NW Switch 병목(1Gbps -> 10Gbps)  
> 서버와 스토리지 사이에 SAN Switch 추가하여 확장이 용이한 구조로 변경  
> IO 민감한 Redo/Temp 영역을 별도의 고성능 스토리지로 분리하여 지연 요소 제거  
> Private NW Switchs -> DB서버들 -> SAN Switch -> 스토리지   
3. 정적 콘텐츠 전송 시 네트워크 병목으로 인한 지연 방지
> L7/SSL 가속기 CPU사용률, WEB서버의 네트워크 대역폭 사용률 고려  
> 정적/동적 콘텐츠가 시스템 내에 처리하고 있어 트래픽이 증가될수록 시스템 부하가 심화됨  
> 정적 콘텐츠에 대한 용량 개선(압축)과 CDN으로 이동하여 네트워크 사용량 개선  
> WEB 이더넷 대역폭 증설(1Gbps -> 2Gbps)   
4. 본인 인증 시 암호화 및 서비스 제공기관 한계로 인한 지연
> 본인 인증 시 암호화된 데이터를 https 방식으로 인증 기관에  전송 시 사용자가 집중되면 지연이 발생함, call stack 분석 시 https 통신을 위한 key 생성 함수에서 지연이 확인  
> JVM 옵션 적용(pkcs11 비활성화), 이미 암호화 된 데이터이므로 통신방식을 HTTP로 변경  
> 본인 인증 방식 다변화(휴대폰 인증, 공동인증, PASS, 네이버, 카카오 인증)  
5. 비효율 SQL로 인한 CPU 과점 및 응답시간 지연
> SQL 응답시간 저하 및 DB서버 CPU 100% 사용  
> 실행횟수가 많고 응답시간이 느린 SQL 중 CPU 사용량이 높은 SQL 확인  
> SQL 튜닝(인덱스 컬럼 추가, View 해체 필요한 로직만 기술, EXISTS 부분을 조인 형태로 수정  

