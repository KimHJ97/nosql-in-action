# 2. 빨리 시작해보기

## 2.1 리눅스에 설치하기

리눅스에서는 컴파일과 빌드를 거쳐야 실행 가능한 레디스 실행 모듈을 만들 수 있다.
 - 설치 환경: 오라클 버추얼박스(CentOS 5.9)
 - 공식 다운로드([링크](http://redis.io/download))

```bash
$ wget http://download.redis.io/redis-stable.tar.gz
$ tar xvf redis-stable.tar.gz
$ cd redis-stable
$ make

# 레디스 서버 실행
$ ./src/redis-server

# 대화형 레디스 클라이언트로 서버 접속
# 기본 localhost, 6379 포트
$ ./src/redis-cli

# 레디스 정보 출력: info
redis 127.0.0.1:6379> info

```

<br/>

---
## 2.2 도커를 이용한 설치

```bash
# Redis 이미지 다운로드
$ docker pull redis

# Redis 실행
$ docker run -d --name my-reids -p 6379:6379 redis

# Redis 서버 접속
$ docker exec -it my-redis /bin/bash

# Redis-Client 접속
$ redis-cli

# 레디스 정보 출력
redis 127.0.0.1:6379> info
```

<br/>

---
## 2.3 info 정보
 - redis_version: 실행 중인 레디스 서버의 버전 정보
 - arch_bits: 실행 중인 레디스 서버의 아키텍처 비트
 - process_id: 실행 주인 레디스 서버의 시스템 프로세스 ID
 - connected_clients: 현재 연결되어 있는 클라이언트의 커넥션 수
 - connected_slaves: 복제를 위해서 연결되는 슬레이브 노드 수
 - used_memory: 레디스 서버가 사용하는 메모리의 양(바이트 단위)
 - used_memory_human: used_memory를 사람이 읽기 쉽도록 메가바이트 또는 기가바이트 단위로 표현
 - used_memory_peak: 레디스 서버가 최대로 사용했던 메모리 크기(바이트 단위)
 - used_memory_peak_human: used_memory_peak을 사람이 읽기 쉽도록 메가바이트 또는 기가 바이트 단위로 표현
 - mem_fragmentation_ratio: 레디스 서버에 저장된 데이터 중에 연속되지 않은 메모리 공간에 저장되어 있는 비율
 - role:master: 마스터-슬레이브 복제 모드에서 동작하는 모드, 단일모드로 동작할 때도 마스터로 표시된다.

<br/>

---
## 2.4 레디스 버전 정보

레디스 버전 정보는 X.X.X와 같은 세 자리 수 버전으로 관리한다.  
첫 번째 자리의 숫자를 메이저 버전이라 하고, 두 번째 자리의 숫자를 마이너 버전, 세 번째 자리의 숫자를 패치레벨이라 한다.  
 - 메이저 버전: 커다란 기능의 변화가 발생했을 때 변경된다.
 - 마이너 버전: 작은 기능이 추가되었을 때 변경된다.
    - 마이너 버전의 숫자가 짝수이면 안정화 버전을 뜻하고, 홀수이면 비안정화 버전을 뜻한다.
- 패치레벨: 마이너 버전에 대하여 패치를 수행한 횟수다.

### 의미론적 버전 명명법

이전에는 아래와 같이 버전 관리 방법이 다양했다.  
이러한 문제로 기튼허브의 창시자인 톰 프레스턴 베너가 의미론적 버전 명명법을 제창했다.  
단순히 버전 정보만으로도 해당 소프트웨어의 상태까지 알 수 있다.  
 - [의미론적 버전 명명법 사이트](http://semver.org/)
 - 표기법1: <Major>.<Minor>.<Revision>
    - ex) 3.2.1, 3.0.0, 3.0.1
 - 표기법2: AppName_<Major>.<Minor>.<Patch/Upgrade>.<BuildNo>
    - ex) SampleApp_3.4.2.1214, SampleApp_3.0.0.1214, SampleApp_3.0.1.125
 - 표기법3: <Major>.<Minor>.[Point]
    - ex) 3.0.3, 3.1, 3.0

<br/>

---
## 2.5 Hello World 출력하기

프로그래밍 언어를 처음 배울 때 "Hello World"를 출력한다.  
레디스 클라이언트를 이용하여 "Hello World"를 출력해보자.  

```bash
# Redis-Client 접속
$ redis-cli

# set 명령은 키와 문자열 값을 받아 저장한다.
# 레디스 클라이언트는 명령과 인자의 구분자로 공백 문자를 사용한다.
# 때문에, 인자를 3개를 보낸 것으로 문법 에러 문구가 출력된다.
127.0.0.1:6379> set message hello world
(error) ERR syntax error

# 공백 문자가 포함된 인자는 따옴표로 감싸서 하나의 인자로 표현할 수 있다.
# 정상적으로 저장되면 "OK"를 반환한다.
127.0.0.1:6379> set message "hello world"
OK

# 저장한 문자열을 출력한다.
127.0.0.1:6379> get message
"hello world"
```

<br/>

---
## 2.6 기본 명령어

레디스는 데이터형에 따라서 실행할 수 있는 명령이 달라진다.  
때문에, 레디스가 지원하는 데이터형과 각 데이터형에 대한 명령을 알아야한다. 
 - 레디스의 키 이름에는 별다른 제한이 없다.  
 - 즉, user:name 키에 사용된 콜론(:)은 아무런 의미가 없다.  
 - 다만, 관례상 콜론은 키에 의미를 부여하는 구분자로 사용된다.  
 - 통상적으로 키는 알파벳과 숫자 그리고 콜론 기호를 사용하여 표기한다. 

<br/>

### 2.6.1 문자열 명령

문자열을 다루는 데 사용하는 레디수 만자열 명령은 20여 개에 이른다.  
기본적인 문자열 명령을 살펴본다.  
 - set <키> <문자열>: 주어진 키에 값을 저장한다.
    - 최초 지원: 1.0.0
    - 시간 복잡도: O(1)
    - 응답: <상태응답>, 항상 OK
 - append <키> <값>: 주어진 키가 존재하면 입력된 값 뒤에 문자열을 추가한다. 키가 존재하지 않으면 set 명령과 동일하게 동작한다.
    - 최초 지원: 2.0.0
    - 시간 복잡도: O(1)
    - 응답: <숫자응답>, 추가된 문자열을 포함한 전체 문자열의 길이
 - incr <키>: 저장된 데이터의 값을 1씩 증가시킨다. 단, 저장된 값이 숫자일 때만 수행된다.
    - 최초 지원: 1.0.0
    - 시간 복잡도: O(1)
    - 응답: <숫자응답>, 명령이 실행된 후의 키의 값
 - decr <키>: 저장된 데이터의 값을 1씩 감소시킨다. 단, 저장된 값이 숫자일 때만 수행된다.
    - 최초 지원: 1.0.0
    - 시간 복잡도: O(1)
    - 응답: <숫자응답>, 명령이 실행된 후의 키의 값

```bash
# "user:name" 키에 "david" 문자열 저장
127.0.0.1:6379> set user:name "david"
OK

# "user:name" 키에 "kris" 문자열 저장
127.0.0.1:6379> set user:name "kris"
OK

# "user:name" 키가 존재하여 뒤에 "ironman" 문자열 추가 저장
127.0.0.1:6379> append user:name "ironman"
(integer) 11

# "user:name" 키의 값 출력
127.0.0.1:6379> get user:name
"krisironman"

# incr을 문자열에 사용한 경우
127.0.0.1:6379> set login:counter "a"
OK
127.0.0.1:6379> incr login:counter
(error) ERR value is not an integer or out of range

# incr을 숫자에 사용한 경우
127.0.0.1:6379> set login:counter "0"
OK
127.0.0.1:6379> incr login:counter
(integer) 1
```

<br/>

## 2.6.2 리스트 명령

리스트 명령은 레디스에서 지원하는 리스트 데이터를 다루기 위한 명령의 집합이다.  
레디스의 리스트 데이터는 논리적으로 링크드 리스트의 구현이다.  
이것은 수천만 건이 저장된 리스트 데이터에 하나의 요소를 추가할 때 O(1)*시간 안에 처리할 수 있는 특징이 있다.  
즉, 얼마나 많은 자료가 저장되어 있는지 상관없이 동일한 처리 시간을 가진다.  
 - 데이터가 입력된 순서대로 저장되고 조회된다.
 - 즉, 먼저 입력한 자료를 먼저 처리하는 큐로 사용된다.
 - lpush <키> <값>: 지정된 리스트의 맨 앞쪽에 입력된 요소를 저장한다.
    - 최초 지원: 1.0.0
    - 시간 복잡도: O(1)
    - 응답: <숫자응답>, 명령이 수행되고 난 후의 요소 수
 - lrange <키> <시작인덱스> <종료인덱스>: 지정된 리스트의 시작인덱스부터 종료인덱스 범위의 요소를 조회한다. 레디스 명령의 인덱스 표현에서 '-1'은 인덱스의 마지막 자리를 의미한다.
    - 최초 지원: 1.0.0
    - 시간 복잡도: O(S+N) S는 시작인덱스, N 범위에 솏하는 요소의 개수(표준 빅 오 표기법으로는 O(N)으로 표기)
    - 응답: <멀티 벌크응답>, 해당 범위의 요소들. 존재하지 않을 때 nil

 ```bash
127.0.0.1:6379> lpush my:list:recommand java
(integer) 1
127.0.0.1:6379> lpush my:list:recommand javascript
(integer) 2
127.0.0.1:6379> lpush my:list:recommand python redis "oracle" "mysql"
(integer) 6
127.0.0.1:6379> lrange my:list:recommand 0 -1
1) "mysql"
2) "oracle"
3) "redis"
4) "python"
5) "javascript"
6) "java"
 ```

 <br/>

 ## 2.6.3 셋 명령

 셋 명령은 레디스에서 지원하는 셋 데이터를 다루는 명령의 집합이다.  
 레디스의 셋 데이터는 순서가 보장되지 않으며 중복을 허용하지 않는 컬렉션이다.  
 즉, 입력 순서와 상관없이 중복된 데이터가 제거되어 조회된다.
  - sadd <키> <값>: 셋에 입력된 값을 저장한다.
    - 최초 지원: 1.0.0
    - 시간 복잡도: O(N), N은 입력된 값의 개수
    - 응답: <숫자응답>, 성공이면 입력된 값의 개수, 이미 존재하는 값이면 0
 - smembers: 지정된 셋에 저장된 모든 값의 목록을 조회한다.
    - 최초 지원: 1.0.0
    - 시간 복잡도: O(N), N은 입력된 값의 개수
    - 응답: <멀티 벌크응답>, 조회된 값 목록, 값이 존재하지 않을 때 nil

```bash
127.0.0.1:6379> sadd my:test:set my
(integer) 1
127.0.0.1:6379> sadd my:test:set name
(integer) 1
127.0.0.1:6379> sadd my:test:set is
(integer) 1
127.0.0.1:6379> sadd my:test:set log
(integer) 1

# 순서가 보장되지 않는다.
127.0.0.1:6379> smembers my:test:set
1) "is"
2) "log"
3) "name"
4) "my"
```

<br/>

### 2.6.4 정렬된 셋 명령

정렬된 셋 명령은 레디스에서 지원하는 정렬된 셋 데이터를 다루는 명령의 집합이다.  
정렬된 셋은 레디스에서 지원하는 셋 데이터와 동일한 특징을 가지며 부가적으로 저장된 요소에 가중치를 부여하여 작은 값부터 큰 값으로(오름차순) 정렬을 제공한다.  
 - zadd <키> <가중치> <값>: 정렬된 셋에 가중치와 값으로 이루어진 데이터를 저장한다. 단, 이미 존재하는 값일 때는 기존의 가중치를 입력된 가중치로 덮어쓴다.
    - 최초 지원: 1.2.0
    - 시간 복잡도: O(log(N)), N은 입력되어 있는 값의 개수
    - 응답: <숫자응답>, 성공이면 입력된 값의 개수, 이미 존재하는 값이면 0
 - zrange <키> <시작인덱스> <종료인덱스> [withscores]: 정렬된 셋의 시작인덱스부터 종료인덱스 범위에 해당하는 값들을 가중치 오름차순으로 조회한다. withscores 인자를 주면 출력 결과에 요소와 가중치가 함께 표시된다.
    - 최초 지원: 1.2.0
    - 시간 복잡도: O(log(N) + M), N은 입력되어 있는 값의 개수, M은 조회된 값의 개수
    - 응답: <멀티 벌크응답>, 조회된 값 목록, 값이 존재하지 않을 때 nil

```bash
127.0.0.1:6379> zadd user:ranking 1 kris
(integer) 1     
127.0.0.1:6379> zadd user:ranking 2 anna
(integer) 1     
127.0.0.1:6379> zadd user:ranking 3 james
(integer) 1     
127.0.0.1:6379> zadd user:ranking 4 jina
(integer) 1     

127.0.0.1:6379> zrange user:ranking 0 -1 withscores
1) "kris"
2) "1"
3) "anna"
4) "2"
5) "james"
6) "3"
7) "jina"
8) "4"
```

<br/>

### 2.6.5 해시 명령

해시 명령은 레디스에서 지원하는 해시 데이터를 다루는 명령의 집합이다.  
레디스가 지원하는 해시 데이터는 키와 값이 쌍으로 이루어진 데이터를 저장하는 자료구조로 자바에서 Map과 동일한 구조이다.
 - hset: 지정된 해시에 요청한 피륻와 값을 저장한다. 단, 요청한 필드가 존재할 때는 저장된 값이 업데이트된다.
    - 최초 지원: 2.0.0
    - 시간 복잡도: O(1)
    - 응답: <숫자응답>, 존재하지 않는 필드일 때 1, 존재하는 필드일 때 0
 - hget: 지정된 해시에 저장된 필드의 값을 조회한다.
    - 최초 지원: 2.0.0
    - 시간 복잡도: O(1)
    - 응답: <벌크응답>, 지정된 필드가 존재할 때 저장된 값, 아니면 nil
 - hgetall: 지정된 키에 저장된 모든 필드와 값을 조회한다.
    - 최초 지원: 2.0.0
    - 시간 복잡도: O(N), N은 저장된 필드 수
    - 응답: <멀티 벌크응답>, 지정된 키에 저장된 모든 필드와 값의 목록

```bash
127.0.0.1:6379> hset user:1 name log
(integer) 1     
127.0.0.1:6379> hset user:1 address "Seoul, Korea"
(integer) 1     
127.0.0.1:6379> hset user:1 age 27
(integer) 1     
127.0.0.1:6379> hget user:1 address
"Seoul, Korea"  
127.0.0.1:6379> hgetall user:1
1) "name"
2) "log"
3) "address"
4) "Seoul, Korea"
5) "age"
6) "27"
```

<br/>

---
## 2.7 레디스 성능 측정

서비스에 새로운 솔루션을 도입하려면 성능 측정이 수반된다.  
얼마나 많은 요청을 한꺼번에 처리할 수 있는지와 요청 하나를 처리하는 데 걸리는 시간(레이턴시) 등을 측정하여 도입 여부를 결정하고 도입할 하드웨어 사양을 결정한다.  
일반적으로 이러한 성능 측정에는 많은 시간이 소요되며, 때로는 별도의 팀에서 진행하기도 한다.  
레디스는 성능 측적을 위한 redis-benchmark 라는 강력한 도구를 자체적으로 제공한다.  

<br/>

 - 만 개의 명령을 처리하는 데 걸린 시간 0.45 초
 - 50개의 클라이언트 동시 연결
 - 저장 데이터의 크기 3바이트
 - 클라이언트 연결 유지 상태 정보
 - 초당 처리된 명령 수
```bash
$ redis-benchmark

..
  100000 requests completed in 0.45 seconds
  50 parallel clients
  3 bytes payload
  keep alive: 1
  host configuration "save": 3600 1 300 100 60 10000
  host configuration "appendonly": no
  multi-thread: no
```

 - redis-benchmark 도구는 성능 측정을 위한 세부적인 설정을 제공한다.

```bash
# 50 바이트의 데이터를 문자열, 리스트, 셋 데이터형으로 저장하고 읽어내는 데 걸리는 시간 측정
$ redis-benchmark -d 50 -q -t get,set,lpush,lpop,sadd,spop
SET: 147275.41 requests per second, p50=0.175 msec
GET: 146412.88 requests per second, p50=0.175 msec
LPUSH: 162866.44 requests per second, p50=0.159 msec
LPOP: 153139.36 requests per second, p50=0.167 msec
SADD: 143472.02 requests per second, p50=0.175 msec
SPOP: 147492.62 requests per second, p50=0.167 msec

# 1024 바이트의 데이터를 문자열, 리스트, 셋 데이터형으로 저장하고 읽어내는 데 걸리는 시간 측정
$ redis-benchmark -d 1024 -q -t get,set,lpush,lpop,sadd,spop
SET: 150150.14 requests per second, p50=0.175 msec
GET: 181488.20 requests per second, p50=0.143 msec
LPUSH: 167504.19 requests per second, p50=0.151 msec
LPOP: 200803.22 requests per second, p50=0.127 msec
SADD: 141643.06 requests per second, p50=0.175 msec
SPOP: 143266.47 requests per second, p50=0.175 msec

# 백만 개의 키를 문자열 데이터로 추가하는 테스트
    # 백만 개의 데이터를 입력하는 데 약 5.12초가 소요되었다.
    # 초당 19만 5천개 이상의 데이터가 저장됐다.
$ redis-benchmark -t set -n 1000000 -r 100000000
  1000000 requests completed in 5.12 seconds
  50 parallel clients
  3 bytes payload
  keep alive: 1
  host configuration "save": 3600 1 300 100 60 10000
  host configuration "appendonly": no
  multi-thread: no

..

Summary:
  throughput summary: 195236.23 requests per second
  latency summary (msec):
          avg       min       p50       p95       p99       max
        0.137     0.032     0.135     0.191     0.279     2.527
```

<br/>

### 2.7.1 레디스 성능 측정 도구 옵션

|옵션|설명|기본값|
|---|---|---|
|-h &lt;hostname&gt;|테스트를 수행하기 위해 접속할 레디스 서버의 호스트명|127.0.0.1|
|-p &lt;port&gt;|테스트를 수행하기 위해 접속할 레디스 서버의 포트|6379|
|-s &lt;socker&gt;|테스트를 수행하기 위해 접속할 레디스 서버의 유닉스 서버 소켓|-|
|-c &lt;clients&gt;|테스트를 위한 가상 클라이언트의 동시 접속 수|50|
|-n &lt;requests&gt;|각 명령의 테스트 횟수|10000|
|-d &lt;size&gt;|테스트에 사용할 데이터 크기|3*|
|-k &lt;boolean&gt;|테스트를 위한 가상 클라이언트의 접속 유지 여부|1: 접속유지, 0: 접속유지X|
|-r &lt;keyspacelen&gt;|테스트에 사용할 랜덤 키의 범위|0|
|P &lt;number&gt;|파이프라인 명령을 사용한 테스트와 파이프라인당 요청할 명령의 개수|0: 파이프라인 미사용|
|-q|테스트 진행 상황을 출력하지 않고 결과만 출력하기|-|
|--csv|테스트 결과를 csv 포맷으로 출력하기|-|
|-l|브레이크(Ctrl+C)를 걸기 전까지 계속 수행하기|-|
|-t &lt;tests&gt;|쉼표로 구분된 테스트 명령의 목록|-|
|-l|명령 전송 없는 연결 생성 후 브레이크(Ctrl+C) 입력 때까지 대기하기|-|

<br/>

---
## 2.8 redis-cli

예제를 실행하기 위해 사용한 도구는 레디스 명령행 클라이언트이다.  
redis-cli는 레디스의 번들 프로그램으로서 간단한 명령어 처리와 데이터 벌크 입력과 같은 다양한 기능을 제공한다.  

<br/>

### 2.8.1 대화형 레디스 클라이언트 실행 옵션

|옵션|설명|기본값|
|---|---|---|
|-h &lt;hostname&gt;|접속할 서버의 호스트명|127.0.0.1|
|-p &lt;port&gt;|접속할 서버의 포트|6379|
|-s &lt;socket&gt;|접속할 서버의 유닉스 서버 소켓|-|
|-a &lt;password&gt;|연결에 필요한 패스워드|-|
|-r &lt;repeat&gt;|지정된 명령을 반복 횟수만큼 실행한다.|-|
|-i &lt;interval&gt;|-r 옵션 사용 시 명령 사이의 대기 시간을 초 단위로 설정한다.|0|
|-n &lt;db&gt;|접속할 데이터베이스 인덱스 지정|-|
|-x|레디스 명령이 입력될 때 리눅스 명령행의 표준 입력으로부터 마짐가 인자를 입력받는다.|-|
|-d &lt;delimiter&gt;|멀티 벌크 입력 시 원시 데이터의 구분자를 지정|&lt;LF&gt;(\n)|t
|-c|클러스터 접속 모드를 사용한다. 명령 뒤에 추가 요구 문자열이 필요하다.|-|
|--raw|멀티 벌크 입력을 사용한다. 결과는 시스템 표준 출력을 통해서 출력된다.|시스템 표준 출력|
|--latency|서버의 명령 응답 속도를 측정하는 모드로 실행한다. <br/>종료하지 않으면 계속 실행되어 서버에 부하를 준다.|-|
|--slave|클러스터의 슬레이브 연결로 에뮬레이트하여 슬레이브가 받는 명령 목록을 출력한다.|-|
|--pipe|벌크 입력을 시스템 표준 입력으로부터 받아들인다.|-|
|--bigkeys|레디스에 저장된 키 중 가장 긴 길이의 키와 그 키에 저장된 데이터의 크기를 출력한다. <br/>종료하지 않으면 계속 실행되어 서버에 부하를 준다.|-|
|--eval &lt;file&gt;|루아 스크립트를 서버에서 실행한다.|-|
|--help|명령 목록과 예제를 출력하고 종료한다.|-|
|--version|대화형 레디스 클라이언트의 버전을 출력하고 종료한다.|-|

<br/>

### 2.8.2 응답시간 측정

레디스 명령행 클라이언트에서 자주 사용되는 명령 중의 하나인 응답시간 측정에 대해서 알아본다.  
운영 중인 레디스가 정상적으로 동작하는지, 얼마나 빨리 응답하는지 확인하는 방법이다.  

 - --latency 옵션을 사용하면 응답시간 측정을 할 수 있다.
    - latency 옵션은 명령이 서버로 전달되고 나서부터 응답이 돌아오기까지의 시간을 측정하는 데 사용한다.
    - 사용자의 종료 명령(Ctrl+C)을 전달받기 전까지 계속 실행된다.
    - 시스템의 자원을 적게 사용하여 운영 중에 사용해도 서비스에 큰 지장을 주지 않지만, 몇 시간 동안 계속 실행하는 것은 바람직하지 않다.
```bash
$ redis-cli --latency
```

<br/>

### 2.8.3 주기적인 통계 정보 조회

메모리 상태, 저장된 키의 개수와 같은 서버의 통계 정보를 주기적으로 확인해야 한다면 별도의 프로그램을 개발하기보다는 간단한 리눅스 쉘명령과 크론탭을 사용할 수 있다.  
일정 시간마다 info 명령을 전송하고 그 결과를 DB나 파일로 저장하여 실시간으로 서버의 상태를 모니터링하기 위해 활용할 수 있다.

 - info 명령의 인자 목록
    - server: 레디스 서버의 기초적인 정보, 프로세스 ID, 포트 등을 출력한다.
    - clients: 접속된 클라이언트 정보 및 통계를 출력한다.
    - memory: 메모리 사용량 통계 정보를 출력한다.
    - persistence: 영구 저장소 상태 및 통계 정보를 출력한다.
    - stats: 키 사용률, 명령 개수에 대한 통계 정보를 출력한다.
    - replication: 복제에 대한 통계 정보를 출력한다.
    - cpu: CPU의 사용 정보에 대한 통계 정보를 출력한다.
    - keyspace: 저장된 키의 개수 정보를 출력한다.

```bash
$ redis-cli info cpu
# CPU
used_cpu_sys:19.973200
used_cpu_user:16.003675
used_cpu_sys_children:0.014521
used_cpu_user_children:0.314988
used_cpu_sys_main_thread:19.982279
used_cpu_user_main_thread:15.983166

$ redis-cli info keyspace
# Keyspace
db0:keys=994909,expires=0,avg_ttl=0

root@75028881e250:/data# redis-cli info stats
# Stats
total_connections_received:1661
total_commands_processed:4206206
instantaneous_ops_per_sec:0
total_net_input_bytes:407488380
total_net_output_bytes:1590859112
total_net_repl_input_bytes:0
total_net_repl_output_bytes:0
..
total_reads_processed:4207863
total_writes_processed:4206206
io_threaded_reads_processed:0
io_threaded_writes_processed:0
reply_buffer_shrinks:224
reply_buffer_expands:1
```