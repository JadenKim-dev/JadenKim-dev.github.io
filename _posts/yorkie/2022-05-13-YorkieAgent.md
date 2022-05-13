---
title:  "Yorkie Agent 사용하기"

categories: 
  - yorkie
tags: 
  - [Open Source, yorkie]

toc: true
toc_sticky: true

date: 2022-05-08
last_modified_at: 2022-05-08
---
`yorkie agent --mongo-connection-uri mongodb://localhost:27017`를 통해 mongodb와 연동하여 yorkie agent 실행

`docker exec -it mongo /bin/bash`를 통해 mongo 컨테이너 위치에서 bash 진입

`mongo`를 통해 mongodb 진입

데이터베이스, 테이블 등의 조회 가능

문제점 : 공식 페이지에서는 docker-compose up을 한 후 docker agent를 실행하라고 되어 있지만,

이렇게 할 경우 포트가 겹쳐서 실행이 안 되는 문제 발생

→ docker 없이 agent를 실행해봤지만, docker-team.github.io가 정상적으로 작동하지 않음

→ yorkie 프로젝트 안의 docker-compose 파일에는 yorkie, envoy에 대한 컨테이너 설정이 포함되어 있지 않음, docker-compose up 할 때 캐시 되어 있는 것을 읽어오는 과정에서 문제가 발생하는 것으로 보임

docker system prune을 통해 저장되어 있는 파일을 모두 제거

→ docker-compose up 후 agent 실행 성공!
    그러나 docker-team.github.io가 여전히 정상적으로 작동하지 않음...
→ envoy가 실행되지 않기 때문에 발생했던 문제. yorkie 자체는 envoy에 의존적이지 않기 때문에 yorkie 프로젝트 안의 docker-compose.yml 안에는 envoy에 대한 설정이 작성되어 있지 않았던 것.

→ docker-compose.yml에 envoy관련 설정을 적어주고, envoy.Dockerfile과 envoy.yml 파일을 작성해주면 실행 성공!

즉, docker agent 실행 만으로는 도커 서버가 온전히 돌아가지 않고 있는 것

⇒ yorkie agent에 대해서 더 자세히 알아보자!

```bash
# yorkie agent -h

Usage:
  yorkie agent [options] [flags]

Flags:
      --auth-webhook-cache-auth-ttl duration         TTL value to set when caching authorized webhook response. (default 10s)
      --auth-webhook-cache-size int                  The cache size of the authorization webhook. (default 5000)
      --auth-webhook-cache-unauth-ttl duration       TTL value to set when caching unauthorized webhook response. (default 10s)
      --auth-webhook-max-retries uint                Maximum number of retries for an authorization webhook. (default 10)
      --auth-webhook-max-wait-interval duration      Maximum wait interval for authorization webhook. (default 3s)
      --auth-webhook-methods strings                 List of methods that require authorization checks. If no value is specified, all methods will be checked.
      --auth-webhook-url string                      URL of remote service to query authorization
      --backend-snapshot-interval uint               Interval of changes to create a snapshot. (default 1000)
      --backend-snapshot-threshold uint              Threshold that determines if changes should be sent with snapshot when the number of changes is greater than this value. (default 500)
  -c, --config string                                Config path
      --enable-pprof                                 Enable runtime profiling data via HTTP server.
      --etcd-dial-timeout duration                   ETCD's dial timeout (default 5s)
      --etcd-endpoints strings                       Comma separated list of etcd endpoints
      --etcd-lock-lease-time duration                ETCD's lease time for lock (default 30s)
      --etcd-password string                         ETCD's password
      --etcd-username string                         ETCD's user name
  -h, --help                                         help for agent
      --housekeeping-candidates-limit int            candidates limit for a single housekeeping run (default 500)
      --housekeeping-deactivate-threshold duration   time after which clients are considered deactivate (default 168h0m0s)
      --housekeeping-interval duration               housekeeping interval between housekeeping runs (default 1m0s)
  -l, --log-level string                             Log level: debug, info, warn, error, panic, fatal (default "info")
      --mongo-connection-timeout duration            Mongo DB's connection timeout (default 5s)
      --mongo-connection-uri string                  MongoDB's connection URI
      --mongo-ping-timeout duration                  Mongo DB's ping timeout (default 5s)
      --mongo-yorkie-database string                 Yorkie's database name in MongoDB (default "yorkie-meta")
      --profiling-port int                           Profiling port (default 11102)
      --rpc-cert-file string                         RPC certification file's path
      --rpc-key-file string                          RPC key file's path
      --rpc-max-requests-bytes uint                  Maximum client request size in bytes the server will accept. (default 4194304)
      --rpc-port int                                 RPC port (default 11101)
```

Flags:

**`--auth-webhook-cache-auth-ttl [duration]`**
: TTL value to set when caching authorized webhook response. (default 10s)
    **authorized된 webhook 응답을 캐싱할 때의 TTL 값** (해당 시간이 지나면 캐시가 사라짐?)

**`--auth-webhook-cache-size [int]`**
: The cache size of the authorization webhook. (default 5000)
    **auth webhook의 캐시 크기**

**`--auth-webhook-cache-unauth-ttl [duration]`**
: TTL value to set when caching unauthorized webhook response. (default 10s)
  **authorized되지 않은 webhook 응답을 캐싱할 때 설정할 TTL 값**

**`--auth-webhook-max-retries [uint]`
:** Maximum number of retries for an authorization webhook. (default 10)
  **auth webhook에 대한 최대 재시도 횟수**

`**--auth-webhook-max-wait-interval [duration]`
:** Maximum wait interval for authorization webhook. (default 3s)
 **auth webook에 대한 최대 대기 시간**

`**--auth-webhook-methods [strings]**`

: List of methods that require authorization checks. 
  If no value is specified, all methods will be checked.
 **권한 확인이 필요한 메서드 목록을 지정. 값을 지정하지 않으면 모든 method에 대해서 권한을 체크함**

`**--auth-webhook-url [string]**` 
: URL of remote service to query authorization
  **authorization을 query하기 위한 remote service의 URL**

**`--backend-snapshot-interval [uint]`
:** Interval of changes to create a snapshot. (default 1000)
  **snapshot 생성에 대한 change interval**

**`--backend-snapshot-threshold [uint]`** 
: Threshold that determines if changes should be sent with snapshot when the number of changes is greater than this value. (default 500)
**변경사항 수에 대한 threshold 지정. 변경 사항 수가 이 값보다 클 때, 변경 사항을 스냅샷과 함께 보내야 함**

 `**-c**`, `**--config [string]**`  : Config path

`**--enable-pprof**`  : Enable runtime profiling data via HTTP server. 
                                 **HTTP 서버를 통한 런타임의 데이터 프로파일링을 활성화합니다.**

**`--etcd-dial-timeout [duration]`**  : ETCD's dial timeout (default 5s)

`**--etcd-endpoints [strings]**`  : Comma separated list of etcd endpoints
                                                         ,**로 구분된 etcd endpoint의 목록**
`**--etcd-lock-lease-time [duration]**` : ETCD's lease time for lock (default 30s)
                                                                      **ETCD가 lock이 될 수 있는 lease time(허용 시간)**

`**--etcd-password [string]**`  : ETCD's password

`**--etcd-username [string]**`  : ETCD's user name

 `**-h, --help`** : help for agent

`**--housekeeping-candidates-limit [int]`
:** candidates limit for a single housekeeping run (default 500)
  **하나의 housekeeping 실행에 대한 후보 제한 (???)**

`**--housekeeping-deactivate-threshold [duration]**`  
: time after which clients are considered deactivate (default 168h0m0s)
  **해당 시간 이후로 클라이언트는 비활성화된 것으로 간주됨**

`**--housekeeping-interval [duration]`
:** housekeeping interval between housekeeping runs (default 1m0s)
  housekeeping **실행 사이의 간격**

  
`**-l**`, `**--log-level [string]**`  : Log level: debug, info, warn, error, panic, fatal (default "info")

`**--mongo-connection-timeout duration**`  : Mongo DB's connection timeout (default 5s)

`**--mongo-connection-uri [string]**`  : MongoDB's connection URI

`**--mongo-ping-timeout [duration]**`  : Mongo DB's ping timeout (default 5s)

`**--mongo-yorkie-database [string]**`  : Yorkie's database name in MongoDB (default "yorkie-meta")

`**--profiling-port [int]` :** Profiling port (default 11102)

`**--rpc-cert-file [string]**`  : RPC certification file's path

`**--rpc-key-file [string]**`  : RPC key file's path

`**--rpc-max-requests-bytes [uint]` :** Maximum client request size in bytes the server will accept. (default 4194304)

**`--rpc-port [int]`**  : RPC port (default 11101)