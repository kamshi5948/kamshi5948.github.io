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
* CPU
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
		* 
