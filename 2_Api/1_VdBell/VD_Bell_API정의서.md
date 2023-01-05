---
layout: default
nav_order: 1
description: ""
parent: VD Bell
permalink: /api/vdbell/doc
---


## VD Bell API 정의서

이 서비스는 푸두 클라우드를 기반으로한 API를 사용한 구현 방법을 안내합니다.

http 프로토콜 요청을 통해 제어하는 관련 기능 및 기술을 얻을 수 있도록 제작되었습니다.



- **개정이력**

|     작성일     |    버전     |       설명        | 비고 |
| :------------: | :---------: | :---------------: | :--: |
| **2020-04-07** | **API 1.0** | **API 최초 작성** |      |



## 서버 실행 파일

##### 정의

실행, 먼저 푸두 클라우드 플랫폼에서 DEVICE ID를 필수로 생성해야 합니다. (VD에 발급 확인요청)

**현재 linux、mac os、win을 지원**

- PuduRobotOpenServer-linux
- PuduRobotOpenServer-macos
- PuduRobotOpenServer-win



##### 작동파라미터

| Name  | Description                                        |
| :---- | :------------------------------------------------- |
| -p    | 자체정의 포트 (지정되지 않으면, 9050포트)          |
| -outl | 로그 output 경로 (지정되지 않으면 현재디렉토리)    |
| -outc | 배치파일 input 경로 (지정되지 않으면 현재디렉토리) |

예시: ./PuduRobotOpenServer.exe -p 9050 -outl c:/log -outc c:/config







## API



### 로봇 리스트 가져오기

##### Request

```http
POST /remote/getRobotList_PMS HTTP/1.1
Host: 
Content-Type: application/json
```



##### Parameter

```json
{
	"deviceid":"",
	"groupid":""
}
```

| Name     | Type     | Description                                |
| :------- | :------- | :----------------------------------------- |
| deviceid | `String` | 푸두 클라우드 플랫폼에서 발급받은 deviceid |
| groupid  | `String` | 푸두 클라우드 플랫폼에서 발급받은 groupid  |



##### Response Body

```json
{
    "msg": "OK",
    "code": "RESP_000",
    "data": {
        "robots": [
            {
                "name": "name",
                "id": "robotId",
                "shop_name": "shop_name"
            },
            {
                "name": "name",
                "id": "robotId",
                "shop_name": "shop_name"
            }
        ]
    }
}
```

| Name | Type         | Description                                 |
| :--- | :----------- | :------------------------------------------ |
| code | `String`     | 푸두 클라우드에 요청하는 서버의 상태값      |
| data | `JsonObject` | 푸두 클라우드에서 받아오는 Body             |
| msg  | `String`     | 푸두 클라우드에 요청하는 서버의 상태 메시지 |



##### Response Body - data

푸두 클라우드에서 받아오는 Body 정의

| Name   | Type        | Description |
| ------ | :---------- | ----------- |
| robots | `JsonArray` | 로봇 리스트 |



##### Response Body - data- robots

| Name      | Type     | Description |
| --------- | -------- | ----------- |
| name      | `String` | 로봇명      |
| id        | `String` | 로봇ID      |
| shop_name | `String` | 사업장명    |







### 로봇 리스트 상태 가져오기

##### Request

```http
POST /remote/getRobotListState HTTP/1.1
Host: 
Content-Type: application/json
```



##### Parameter

```json
{
	"deviceid":"",
	"groupid":""
}
```

| Name     | Type     | Description                                |
| :------- | :------- | :----------------------------------------- |
| deviceid | `String` | 푸두 클라우드 플랫폼에서 발급받은 deviceid |
| groupid  | `String` | 푸두 클라우드 플랫폼에서 발급받은 groupid  |



##### Response Body

```json
{
    "code": "RESP_000",
    "contents": {
        "msg": "",
        "code": 0,
        "data": {
            "state": [
                {
                    "chargeStage": "Idle",
                    "robotState": "Free",
                    "moveState": "Idle",
                    "robotPose": {
                        "x": 7.369246177510859,
                        "angle": -1.5588354408704386,
                        "y": 0.33250640064917336
                    },
                    "robotId": "aabbccddeeff",
                    "robotPower": 56
                }
            ]
        }
    },
    "message": "OK"
}
```

| Name     | Type     | Description                                 |
| :------- | :------- | :------------------------------------------ |
| code     | `String` | 푸두 클라우드에 요청하는 서버의 상태값      |
| contents | `String` | 푸두 클라우드에서 받아오는 Body             |
| message  | `String` | 푸두 클라우드에 요청하는 서버의 상태 메시지 |



##### Response Body - contents

푸두 클라우드에서 받아오는 Body 정의

| Name | Type         | Description                            |
| :--- | :----------- | :------------------------------------- |
| msg  | `String`     | 푸두 클라우드에서 받아오는 상태 메시지 |
| code | `Integer`    | 푸두 클라우드에서 받아오는 코드        |
| data | `JsonObject` | 푸두 클라우드에서 받아오는 데이터      |



##### Response Body - contents - data

| Name  | Type        | Description      |
| :---- | :---------- | :--------------- |
| state | `JsonArray` | 로봇 상태 리스트 |



##### Response Body - contents - data - state

로봇 상태 리스트 정의

| Name        | Type         | Description                                                  |
| :---------- | :----------- | :----------------------------------------------------------- |
| chargeStage | `String`     | 배터리 상태                                                  |
| robotState  | `String`     | FREE : 로봇이 업무가 없는 상태로 즉시 호출 임무 수행 가능, <br />BUSY : 로봇이 업무 수행중이거나 누군가 조작중, 업무가 완료되거나 조작 완료가 되면 FREE 상태로 돌아감 |
| moveState   | `String`     | 로봇 움직임 상태<br />Stuck : 장애물 발견<br />Moving : 이동중<br />Pause : 일시멈춤<br />Avoid : 로봇끼리 만남, 운행 조절<br />Error : 운행중 이상 발생시 작동 멈춤<br />Approaching : 로봇이 어딘가로 접근 상태 (목적지, 출발지등) |
| robotPose   | `JsonObject` | 로봇 자세 상태                                               |
| robotId     | `String`     | 로봇 ID                                                      |
| robotPower  | `Integer`    | 전량 (0~100)                                                 |



##### Response Body - contents - data - state - robotPose

로봇 자세 상태  정의

| Name  | Type     | Description                                                  |
| :---- | :------- | :----------------------------------------------------------- |
| x     | `Double` | 지도상 X축 좌표                                              |
| y     | `Double` | 지도상 Y축 좌표                                              |
| angel | `Double` | 로봇 각도에서 X축 좌표 정방향기준0, X축 정방향에서 Y축으로부터 회전하면 X반대방향으로 0~180변화, X축 정방향기준 Y축으로부터 반대방향으로 회전하면 X축 0~ -180 변화 |







### 로봇 상태 가져오기

##### Request

```http
POST /remote/getRobotState HTTP/1.1
Host: 
Content-Type: application/json
```



##### Parameter

```json
{
    "deviceid":"",
	"groupid":"", 
    "robotid" : "aabbccddeeff"
}
```

| Name     | Type     | Description                                |
| :------- | :------- | :----------------------------------------- |
| deviceid | `String` | 푸두 클라우드 플랫폼에서 발급받은 deviceid |
| groupid  | `String` | 푸두 클라우드 플랫폼에서 발급받은 groupid  |
| robotid  | `String` | 로봇 ID                                    |



##### Response Body

```json
{
    "code": "RESP_000",
    "contents": {
        "msg": "",
        "code": 0,
        "data": {
            "chargeStage": "Idle",
            "moveState": "Idle",
            "robotState": "Free",
            "robotPose": {
                "x": 7.369245544456752,
                "angle": -1.559163615271345,
                "y": 0.3325600862478676
            },
            "robotPower": 44
        }
    },
    "message": "OK"
}
```

| Name     | Type     | Description                                 |
| :------- | :------- | :------------------------------------------ |
| code     | `String` | 푸두 클라우드에 요청하는 서버의 상태값      |
| contents | `String` | 푸두 클라우드에서 받아오는 Body             |
| message  | `String` | 푸두 클라우드에 요청하는 서버의 상태 메시지 |



##### Response Body - contents

푸두 클라우드에서 받아오는 Body 정의

| Name | Type         | Description                            |
| :--- | :----------- | :------------------------------------- |
| msg  | `String`     | 푸두 클라우드에서 받아오는 상태 메시지 |
| code | `Integer`    | 푸두 클라우드에서 받아오는 코드        |
| data | `JsonObject` | 푸두 클라우드에서 받아오는 데이터      |



##### Response Body - contents - data

로봇 상태 정의

| Name        | Type         | Description                                                  |
| :---------- | :----------- | :----------------------------------------------------------- |
| chargeStage | `String`     | 배터리 상태                                                  |
| robotState  | `String`     | FREE : 로봇이 업무가 없는 상태로 즉시 호출 임무 수행 가능, <br />BUSY : 로봇이 업무 수행중이거나 누군가 조작중, 업무가 완료되거나 조작 완료가 되면 FREE 상태로 돌아감 |
| moveState   | `String`     | 로봇 움직임 상태<br />Stuck : 장애물 발견<br />Moving : 이동중<br />Pause : 일시멈춤<br />Avoid : 로봇끼리 만남, 운행 조절<br />Error : 운행중 이상 발생시 작동 멈춤<br />Approaching : 로봇이 어딘가로 접근 상태 (목적지, 출발지등) |
| robotPose   | `JsonObject` | 로봇 자세 상태                                               |
| robotPower  | `Integer`    | 전량 (0~100)                                                 |



##### Response Body - contents - data - robotPose

로봇 자세 상태  정의

| Name  | Type     | Description                                                  |
| :---- | :------- | :----------------------------------------------------------- |
| x     | `Double` | 지도상 X축 좌표                                              |
| y     | `Double` | 지도상 Y축 좌표                                              |
| angel | `Double` | 로봇 각도에서 X축 좌표 정방향기준0, X축 정방향에서 Y축으로부터 회전하면 X반대방향으로 0~180변화, X축 정방향기준 Y축으로부터 반대방향으로 회전하면 X축 0~ -180 변화 |







### 로봇을 목적지로 호출 요청

로봇을 목적지로 호출합니다. 로봇이 작업 상태인 경우 대기 상태에 위치 합니다.
(호출요청 -> 대기 -> 이동 -> 목적지 도착 -> 로봇에서 확인 -> 출발지로 복귀)

##### Request

```http
POST /remote/insQueueCall HTTP/1.1
Host: 
Content-Type: application/json
```



##### Parameter

```json
{
	"deviceid":"deviceid",
	"groupid":"groupid",
	"selectrobot":"",
	"exetype":"custom",
	"destination":{
		"name":"name",
		"type":"table"
	}
}
```

| Name        | Type         | Description                                |
| :---------- | :----------- | :----------------------------------------- |
| deviceid    | `String`     | 푸두 클라우드 플랫폼에서 발급받은 deviceid |
| groupid     | `String`     | 푸두 클라우드 플랫폼에서 발급받은 groupid  |
| selectrobot | `String`     | 특정로봇을 지정하여 호출 (로봇ID)          |
| exetype     | `String`     | 로봇 호출 타입 (고정)                      |
| destination | `JsonObject` | 목적지 정보                                |

##### Parameter - destination

로봇 자세 상태  정의

| Name | Type     | Description                |
| :--- | :------- | :------------------------- |
| name | `String` | 목적지명 (테이블 이름)     |
| type | `String` | 목적지 타입 ("table" 고정) |





##### Response Body

```json
{
    "code": "RESP_000",
    "message": "OK"
}
```

| Name    | Type     | Description                                 |
| :------ | :------- | :------------------------------------------ |
| code    | `String` | 푸두 클라우드에 요청하는 서버의 상태값      |
| message | `String` | 푸두 클라우드에 요청하는 서버의 상태 메시지 |







### 대기중인 목적지 호출 취소

##### Request

```http
POST /remote/delQueueCall HTTP/1.1
Host: 
Content-Type: application/json
```



##### Parameter

```json
{
	"deviceid":"deviceid",
	"groupid":"groupid",
	"destination":{
		"name":"name",
		"type":"table"
	}
}
```

| Name               | Type         | Description                                |
| :----------------- | :----------- | :----------------------------------------- |
| deviceid           | `String`     | 푸두 클라우드 플랫폼에서 발급받은 deviceid |
| groupid            | `String`     | 푸두 클라우드 플랫폼에서 발급받은 groupid  |
| destination        | `JsonObject` | 목적지 정보                                |
| destination - name | `String`     | 목적지명                                   |
| destination - type | `String`     | 목적지 타입                                |



##### Response Body

```json
{
    "code": "RESP_000",
    "message": "OK"
}
```

| Name    | Type     | Description                                 |
| :------ | :------- | :------------------------------------------ |
| code    | `String` | 푸두 클라우드에 요청하는 서버의 상태값      |
| message | `String` | 푸두 클라우드에 요청하는 서버의 상태 메시지 |







### 대기중인 목적지, 목적지로 이동중인 로봇 리스트 가져오기

##### Request

```http
POST /remote/getPollingQueue HTTP/1.1
Host: 
Content-Type: application/json
```



##### Parameter

```json
{
	"deviceid":"deviceid",
	"groupid":"groupid"
}
```

| Name     | Type     | Description                                |
| :------- | :------- | :----------------------------------------- |
| deviceid | `String` | 푸두 클라우드 플랫폼에서 발급받은 deviceid |
| groupid  | `String` | 푸두 클라우드 플랫폼에서 발급받은 groupid  |



##### Response Body

```json
{
    "code": "RESP_000",
    "contents": {
        "waiting_Queue": {
            "wait_queuecount": 1,
            "wait_queue": [
                {
                    "groupid": "groupid",
                    "destination": {
                        "name": "목적지명",
                        "type": "table"
                    },
                    "state": "normal",
                    "keydest": "목적지명",
                    "uuid": null,
                    "selectrobot": "",
                    "order": 1,
                    "ts": 1649319192508
                }
            ]
        },
        "working_Queue": {
            "work_queue": [
                {
                    "ts_work": 1649319208314,
                    "groupid": "groupid",
                    "destination": {
                        "name": "목적지명",
                        "type": "table"
                    },
                    "state": "normal",
                    "robotid": "robotid",
                    "keydest": "목적지명",
                    "uuid": null,
                    "order": 1,
                    "ts": 1649319189910
                }
            ],
            "work_queuecount": 1
        }
    },
    "message": "OK"
}
```

| Name     | Type         | Description                                 |
| :------- | :----------- | :------------------------------------------ |
| code     | `String`     | 푸두 클라우드에 요청하는 서버의 상태값      |
| message  | `String`     | 푸두 클라우드에 요청하는 서버의 상태 메시지 |
| contents | `JsonObject` | 서버에서 조회한 데이터                      |



##### Response Body - contents

서버에서 조회한 데이터 정의

| Name          | Type         | Description        |
| :------------ | :----------- | :----------------- |
| waiting_Queue | `JsonObject` | 대기중인 호출 정보 |
| working_Queue | `JsonObject` | 작업중인 호출 정보 |



##### Response Body - contents - waiting_Queue

대기중인 호출 정의

| Name            | Type        | Description        |
| :-------------- | :---------- | :----------------- |
| wait_queuecount | `Integer`   | 대기중인 호출 수   |
| wait_queue      | `JsonArray` | 대기중인 호출 정보 |



##### Response Body - contents - wait_queue

대기작업 정보 정의

| Name        | Type         | Description                               |
| :---------- | :----------- | :---------------------------------------- |
| groupid     | `String`     | 푸두 클라우드 플랫폼에서 발급받은 groupid |
| destination | `JsonObject` | 호출한 목적지 정보                        |
| state       | `String`     | 상태값                                    |
| keydest     | `String`     | 목적지명                                  |
| uuid        | `String`     | 사용안함                                  |
| selectrobot | `String`     | 호출 지정 로봇ID                          |
| order       | `Integer`    | 대기순번                                  |
| ts          | `Long`       | 호출시간                                  |



##### Response Body - contents - wait_queue - destnation

대기작업 정보 정의

| Name | Type     | Description |
| :--- | :------- | :---------- |
| name | `String` | 목적지명    |
| type | `String` | 목적지 타입 |



##### Response Body - contents - working_Queue

작업중인 호출 정의

| Name            | Type        | Description        |
| :-------------- | :---------- | :----------------- |
| work_queuecount | `Integer`   | 작업중인 호출 수   |
| work_queue      | `JsonArray` | 진행중인 작업 정보 |



##### Response Body - contents - work_queue

작업중인 정보 정의

| Name        | Type         | Description                               |
| :---------- | :----------- | :---------------------------------------- |
| ts_work     | `Long`       | 목적지 출발시간                           |
| groupid     | `String`     | 푸두 클라우드 플랫폼에서 발급받은 groupid |
| destination | `JsonObject` | 호출한 목적지 정보                        |
| state       | `String`     | 상태값                                    |
| robotid     | `String`     | 작업중인 로봇ID                           |
| keydest     | `String`     | 목적지명                                  |
| uuid        | `String`     | 사용안함                                  |
| order       | `Integer`    | 작업 순번                                 |
| ts          | `Long`       | 호출 시간                                 |



##### Response Body - contents - work_queue- destnation

작업중인 정보 정의

| Name | Type     | Description |
| :--- | :------- | :---------- |
| name | `String` | 목적지명    |
| type | `String` | 목적지 타입 |







### 로봇 맵정보 가져오기

##### Request

```http
POST /remote/getMapData HTTP/1.1
Host: 
Content-Type: application/json
```



##### Parameter

```json
{
    "deviceid":"deviceid",
    "robotid":"robotid"
}
```

| Name     | Type     | Description                                |
| :------- | :------- | :----------------------------------------- |
| deviceid | `String` | 푸두 클라우드 플랫폼에서 발급받은 deviceid |
| groupid  | `String` | 푸두 클라우드 플랫폼에서 발급받은 groupid  |



##### Response Body

```json
{
    "code": "RESP_000",
    "contents": {
        "msg": "",
        "code": 0,
        "data": {
            "total": 76,
            "pageIndex": 1,
            "destinations": [				
				{
                    "name": "목적지명",
                    "type": "table"
                }
            ],
            "pageSize": 200
        }
    },
    "message": "OK"
}
```

| Name     | Type         | Description                                 |
| :------- | :----------- | :------------------------------------------ |
| code     | `String`     | 푸두 클라우드에 요청하는 서버의 상태값      |
| contents | `JsonObject` | 푸두 클라우드에서 받아오는 Body             |
| message  | `String`     | 푸두 클라우드에 요청하는 서버의 상태 메시지 |



##### Response Body - contents

푸두 클라우드에서 받아오는 Body 정의

| Name | Type         | Description                            |
| :--- | :----------- | :------------------------------------- |
| msg  | `String`     | 푸두 클라우드에서 받아오는 상태 메시지 |
| code | `Integer`    | 푸두 클라우드에서 받아오는 코드        |
| data | `JsonObject` | 푸두 클라우드에서 받아오는 데이터      |



##### Response Body - contents - data

맵데이터 정의

| Name         | Type        | Description                     |
| :----------- | :---------- | :------------------------------ |
| total        | `String`    | 목적지 수량                     |
| pageIndex    | `Integer`   | 푸두 클라우드에서 받아오는 코드 |
| destinations | `JsonArray` | 등록된 목적지 리스트            |
| pageSize     | `Integer`   | 한 페이지에 표시되는 목적지 수  |



##### Response Body - contents - data - destinations

맵데이터 정의

| Name | Type      | Description |
| :--- | :-------- | :---------- |
| name | `String`  | 목적지명    |
| type | `Integer` | 목적지 타입 |







### 푸두 클라우드 서버에서 발생하는 상태 코드

| Name  | Description                                                  |
| :---- | :----------------------------------------------------------- |
| 0     | 정상                                                         |
| 10001 | 외부 인터넷 요청 실패                                        |
| 10002 | 외부 인터넷 요청 오류                                        |
| 10003 | 설비(디바이스) 부족                                          |
| 10004 | 인터넷 요청 실패 (로봇이 푸두 클라우드에 연결되어 있지 않음) |
| 10005 | 파라미터 오류                                                |
| 20004 | 호출기 오류                                                  |
| 20006 | 호출 취소 실패                                               |
| 20007 | 호출 실패                                                    |
| 20008 | 호출 실패, 호출 목적지 존재하지 않음                         |
| 20009 | 호출 실패, 로봇 호출기능 종료                                |
| 20010 | 호출 실패, 다른 호출기가 이미 다른 지점으로 호출함           |







### 푸두 클라우드에 요청하는 서버의 상태 코드

| Name     | Description                           |
| :------- | :------------------------------------ |
| RESP_000 | 정상                                  |
| RESP_002 | 처리결과 비정상(실패)                 |
| RESP_003 | 커넥션 타임아웃                       |
| RESP_004 | 알수없는 오류                         |
| RESP_021 | 파라미터 없음 - groupid               |
| RESP_041 | wait 작업 큐에 이미 Call요청을 등록함 |
| RESP_042 | 큐에 Call 명령이 없음                 |
| RESP_043 | work 작업 큐에 이미 Call요청을 등록함 |
| RESP_044 | 큐 리셋 불가                          |
| RESP_045 | groupid를 찾을 수 없음                |
| RESP_046 | 잘못된 Json 데이터                    |










