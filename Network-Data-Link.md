---
title: Network Data Link
date: 2021-04-05 21:01:32
tags:
---

# 데이터 링크

## 데이터링크 계층과 스위치

### 데이터 링크 계층이란

OSI 7 Layer 의 2계층으로 인접한 네트워크 노드끼리 데이터를 전송하는 기능과 절차를 제공

물리계층에서 발생할 수 있는 오류를 감지하고 수정

대표적인 프로토콜로 이더넷이 있으며 장비로는 스위치가 있다.

프로세스 유닛이 프레임단위

**2계의 부계층**

MAC(Media Access Control) : 물리적인 부분으로 매체간의 연결방식을 제어하고 1계층과 연결

LLC(Logical Link Control) : 논리적인 부분으로 Frame을 만들고 3계층과 연결

**MAC주소**

명령어 cmd > ipconfig /all 또는 네트워크 설정에서 확인

6바이트 로 6자리 구성 16진수로 표현

3자리는 oui 나머지 3은 벤더정보

### 주요 기능

**Framing**

데이터그램을 캡슐화하여 프레임 단위로 만들고 헤더와 트레일러를 추가

헤더는 목적지 출발지 주소 그리고 데이터 내용을 정의

트레일러는 비트 에러를 감지

**회선제어**

신호간의 충돌이 발생하지 않도록 제어

enq / ack 방법

전용 전송링크 1:1

enq - ack - data - ack - eot

polling 1: 1

select 모드 :송신자가 나머지 수신자들을 선택하여 전송

sel -ack -data - ack

poll 모드 수신자에게 데이터 수신여부를 확인하여 응답을 확인하고 전송

**흐름제어**

송신자와 수신자의 데이터를 처리하는 속도 차이를 해결하기 위한제어

feedback 방식의 flow control 이며 상위 계층은 rate 기반

stop & wait : 데이터를 보내고 ack 응답이 올때까지 기다린다. 간단한 구현이지만 비효율적이다.

문제점 : frame을 전달하고 ack이 회선문제로 응답하지 않는 경우가 있다. 일정시간후 응답하는 방식으로 처리해야한다.

frame을 재전송하게 되면 dubplicate frame문제가 발생할 수 있다.

sequence number을 사용하여 동일 frame인지 구분하여 상위 계층으로 전달

**sliding window**

ack 응답없이 여러개의 프레임이 연속으로 전송 가능

window size는 전송과 수신측의 데이터가 저장되는 버퍼의 크기

**오류제어**

전송 중에 오류나 손실 발생 시 수신측은 에러를 탐지 및 재전송

arq : 프레임 손상시 재전송이 수행되는 과정

**go back in arq** : 전송측 frame 6개 전송 수신측 3번 frame 문제 발생 수신측 nak3으로 손상 전달

전송측 3번 포함해서 345 재전송

### 이더넷 프레임구조

ethernet v2

데이터링크 계층에서 mac 통신과 프로토콜 형식을 정의

preamble : 이더넷 프레임의 시작과 동기화

dest addr : 목적지 mac 주소 ,

source addr : 출발지 mac 주소

type : 캡슐화되어 있는 패킷 프로토콜 정의

data : 상위 계층의 데이터로 46 바이트보다 작은 면 뒤에 패딩이 붙는다

fcs : 에러체크

## 스위치와 ARP

### 스위치

정의 : 데이터링크의 대표적인 장비로 mac 주소 기반 통신

허브의 단점을 보완

half duplex -> full duplex

1 collision domain -> 포트별 collision domain

라우팅 기능이 있는 스위치를 l3 스위치라고도 부른다.

**동작방식**

목적지 주소를 mac 주소 테이블에서 확인하여 연결된 포트로 프레임전달

learning : 출발지 주소가 mac 주소 테이블에 없다면 해당 주소를 저장

flooding - broadcasting : 목적지 주소가 mac 주소 테이블에 있으면 해당 포트로 전달

filltering - collision domain : 출발지와 목적지가 같은 네트워크 영역이면 다른 네트워크로 전달하지 않음

aging : mac 주소 테이블의 각주소는 일정시간이후 업데이트

**Learning**

스위치는 연결된 컴퓨터의 포트와 주소정보를 보고 테이블에 저장해둠

**flooding**

컴퓨터가 신규로 연결된 컴퓨터를 찾으려고 한다. mac table에 없다면 전체포트로 프레임을 날린다.

**forwarding**

스위치는 해당 주소가 테이블에 존재하므로 프레임을전달

**filtering**

프레임을 전달하려는 주소가 동일 영역이면 다른 포트로 전달하지 않는다. 효율적 통신가능

**aging**

스위치의 mac 주소 테이블은 시간이 지나면 삭제

저장공간을 효율적으로 사용

해당 포트에 연결된 pc가 다른 포트로 옮겨진 경우도 발생

기본 300초 , 다시 프레임이 발생되면 다시 카운트

**정리**

프레임 유입

### ARP

address resolution protocol

ip 주소를 통해서 mac 주소를 알려주는 프로토콜

**동작과정**

1. 패킷 전송시 목적지 mac 주소를 알기 위해서 우선 자신의 arp cache table을 확인
   1. arp -a
2. arp cache table에 있으면 패킷 전송, 없으면 ,arp request 전송 broadcasting
3. 목적지 mac 주소를 arp reply 로 전달
4. 목적지 mac 주소는 arp cache 테이블에 저장되고 패킷 전송

**arp 헤더구조**

hardware type : arp가 동작하는 네트워크 환경, 이더넷, 다음은 mac 주소 6byte와 ip 주소 4byte

- hardware length
- protocol length

protocol type : 프로토콜 종류 대부분 IPv4

- operation :명령 코드 1 = arp request, 2 = arp reply

sender hardware address : mac, protocol address = ip

sender protocol address

target hardware address

target protocol address

## 스패닝 트리 프로토콜

### Looping

- 같은 네트워크 대역 대에서 스위치에 연결된 경로가 2개 이상인 경우에 발생

- PC가 브로드캐스팅 패킷을 스위치들에게 전달하고 전달받은 스위치들은 Flooding을 한다.
- 스위치들끼리 Flooding된 프레임이 서로 계속 전달되어 네트워크에 문제를 일으킨다.
- 회선 및 스위치 이중화 또는 증축 등에 의해 발생
- 물리적인 포트 연결의 실수 또는 잘못된 이중화 구성으로 l2에서 가장 빈번히 발생하는 이슈

**구조**

PC가 브로드 캐스팅을 스위치에 전송하고 스위치1 도 다른 스위치 2,3 에게 브로드 캐스팅을 전송한다.

전달받은 브로드 캐스팅 프레임을 스위치 2, 3도 모든 포트에 전송한다.

브로드캐스팅 스톰이 발생

### STP

자동으로 루핑을 막아주는 알고리즘(스패닝 트리 알고리즘)

ieee 802.1d

stp는 두가지 개념을 가지고 있다.

1. bridge ID : 스위치의 우선순위로 낮을 수록 우선순위가 높다.
2. path cost : 링크의 속도 (대역폭), 작을 수록 우선순위가 높다.

**stp 요소**

root bridge : 네트워크당 1개 선출

root port : root bridge 가 아닌 스위치들은 1개 포트 선출

deignated port : 각 세그먼트별 1개 포트 지정

**BPDU**

스패닝 트리 프로토콜에 의해 스위치간 서로 주고받는 제어프레임

1. Configuration BPDU: 구성관련

root bid - 루트브리지로 선출될 스위치 정보

pathcost - 루트 브리지 까지의 경로 비용

bridge id, port id - 나머지 스위치와 포트의 우선순위

2. tcn (topology change notification) bpdu

네트워크 내 구성 변경 시 통보

우선 순위 - 낮은 숫자가 더 높은 priority 를 가진다.

누가 더 작은 root bid / 루트 브리지까지 더 낮은 path cost / 연결된 스위치 중 누가 더 낮은 bid / 연결된 포트 중 누가 더 낮은 port id

**root bridge 선출**

각 스위치는 고유의 bid 를 가진다. 2바이트 + 6바이트mac 주소

서로 bpdu를 교환하고 가장 낮은 숫자가 루트 브리지가 된다.

우선 순위 숫자는 명령어로 설정 가능하다.

**root 포트 선출**

나머지 스위치들은 루트 브리지와 가장 빠르게 연결되는 루트 포트를 선출한다.

루트 포트는 가장 낮은 root path cost 값을 가진다.

switch2는 p1 = 4 + 19, switch3는 p0 = 19

**designated port 선출**

각 세그먼트 별 루트 브리지와 가장 빠르게 연결되는 포트를 designated 포트로 선출

우선순위는 루트 브리지 id > path cost > 브리지 id > 포트 id

switch 1 p0 & p1, 1Gbps 라인에서는 switch 3 p1이 designated port

**상태변화**

스위치의 포트는 스패닝 트리 프로토콜 안에서 5가지 상태로 표현된다.

1. disabled : 포트가 shut down 상태로 데이터 전송 불가 , mac 학습 불가 , bpdu 송수신

2. blocking : 부팅하거나 disabled 상태를 up 했을때 첫번째 거치는 단계 bpdu만 송수신
3. listening : blocking 포트가 루트또는 데지그네이티드 포트로 선정되는 단계
4. learning : 리스닝 상태에서 특정 시간이 흐른 후 러닝 상태가 됨, mac 학습시작

5. forwarding : 러닝 상태에서 특정 시간이 흐른 후 포워딩 상태가 됨, 데이터 전송 시작

### RSTP & MST

**RSTP**

stp를 적용하면 포워딩 상태까지 30 ~ 50초 걸림, 이 **컨버전스 타임을 1 ~ 2 초 내외로 단축**

learning & listening 단계가 없음

**mst**

ieee 802.1s

네트워크 그룹이 많아지면 stp or rstp bpdu 프레임이 많아지고 스위치 부하 발생

여러 개의 stp 그룹들을 묶어서 효율적으로 관리

## VLAN

### **Virtual Local Area Network**

물리적 구성이 아닌 논리적인 가상의 LAN을 구성하는 기술

- 불필요한 데이터 차단 : 브로드캐스트 도메인 별로 나누어 관리
- 관리의 용이성과 보안 : 호스트의 물리적 이동 없이 LAN 그룹 변경이 가능
- 비용 절감 : 새로운 LAN 추가시 물리적 스위치 구매 필요없음

### 종류

**Port 기반 VLAN**

여러개의 VLAN을 설정하고 각각의 LAN에 물리적인 포트를 지정

VLAN 변경이 필요한 호스트는 물리적인 포트 또는 스위치의 VLAN 설정을 변경

**MAC 주소 기반 VLAN**

각 호스트 또는 네트워크 장비의 MAC주소를 각각의 VLAN에 정의

호스트가 이동되어도 VLAN 변경 필요 없음, 신규 호스트 연결시 설정 변경 필요

**IP 주소 기반 VLAN**

IP 주소 서브넷 기반으로 VLAN을 나누는 방법

IP(Internet Protocol) : 3계층에서 사용하는 프로토콜

서브넷: IP주소의 네트워크영역의 크기를 나눈것

### Trunk

물리적 스위치간 VLAN 연결 시 하나의 물리적 연결로 VLAN 그룹들을 공유

대규모 망에서 스위치의 개수는 증가 -> VLAN 그룹 개수도 증가

물리적 연결 케이블은 복잡하다

여러 VLAN 케이블 하나의 물리적 연결 케이블로 커버

**트렁크 프로토콜**

이더넷 프레임에 식별용 vlan ID 를 삽입하여 데이터를 구분하여 통신 및 제어 가능

**802.1q tagged format**

이더넷 프레임에 삽입되며 4바이트로 구성

TPID(Tag Protocol IDentifier) : 태그되지 않은 프레임과 태깅된 프레임을 구별

TCI(Tag Control Information): 태그 제어 정보

1. PCP(Priority Code Point): 프레임의 우선순위
2. DEI(Drop Eligible Indicator): 트래픽 혼잡시 제거되기 적합한 프레임들을 가리킴
3. VID(VLAN Identifier): VLAN이 어느 프레임에 속하는지를 결정

### VLAN 구성

**설계**

1. vlan 그룹정의
2. vlan 구성 방법 정의 : 포트 / mac주소/ ip 주소
3. 트렁크 포트 정의 : 대역폭 확인 , tagged 할 프레임 정의

**설정**

1. vlan 그룹설정
2. 액세스 모드:사용할 포트에 1개의 vlan id 설정
3. 트렁크 모드 : 사용할 포트에 여러개의 vlan id 설정
4. 다이나믹 모드 : 연결된 포트들의 상태에 따라서 액세스 또는 트렁크로 변경되는 모드
