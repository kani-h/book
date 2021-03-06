# 3.1 기본 네트워크 기술 

>  3.1.6부터 정리하기 시작 -_-;;;



### 3.1.6 부하 분산 기초

- 부하분산에는 여러가지 방법이 있다. 그중에 가장 대표적인 것이  Load Balancer를 이용하는 것


- 클라이언트의 요청을 일 정 알고리즘에 따라 여러 서버로 분담 시킴
- 균등하게 분담시키는 알고리즘 Round Robin이나, 요청 내용마다  분담시킬 곳을 정하는 알고리즘 등이 있다.
- Health Check 를 통해 장애가 있는 서버에는 요청을 할당하지 않는다. (처리 뿐 아니라, 가용성을 높임)
- 분산구조를 구조를 구성할때는 Load Balancer 자체가 SPOF가 되지 않도록 주의한다.
  - SPOF (Single Point Of Failure) : 단일 장애점
  - ex) Load Balencer 한대에 여러 서버를 물림, Load Balancer의 장애가 전체 장애로 이어짐



### 3.1.7 가상네트워크 기초

- 가상네트워크
  - 물리 네트워크에서 소프트웨어로 에뮬레이션한 네트워크
  - overly network(오버레이 네트워크)라고도 부르기도한다.
  - 여러 독립적인 네트워크를 구성할 수 있다.
  - 네트워크 추가/변경/삭제 등 네트워크 구성을 바꿀때 자유도가 높아짐. (물리 작업  X)
  - 물리 네트워크 구조가 은폐되고, 그아 래있는 물리 계층의 형태나, 제어 방식을 등을 의식하지 않고 이용 할 수 있는 것이 특징
- 물리네트워크 기초
  - L3라우터, L2스위치등의 물리 네트워크 기기르 서로 연결하는 것으로 구축이 시작된다.
  - 네트워크 관련서비스를 제공하는 서버(DNS, DHCP)를 조합해서 네트환경워크 환경을 갖추게된다.
  - 기업 시스템의 물리 네트워크에는 일반적인으로 기능마다 게층을 나눠 확장성과 유지 관리성을 향상하도록 네트워크 전체를 설계한다. 대표적으로 시스코의 '계층적 네트워크 설계'가 있다.
    - 시스코의 계층적 네트워크 설계
      1. 코어 계층 : 네트워크 백본으로 외부 네트워크를 접속하는 계층, 대용량 트래픽을 처리할 수 있는 네트워크 기기를 배치한다.
      2. 배포 계층 : 엑세스 계층의 서브넷을 집약하는 계층으로 L3스위치에서 접속해서 라우틴을 한다. 기업에서는 부서나, 층별로 분할
      3. 시스템 이용자가 PC나 플로어 서버 등에서 실제로 이용하는 계층, L2스위치로 배포 계층과 접속된다. 라우팅 기능을 가지지 않는다.
  - 물리네트워크에서는 트래픽 증가와 장애에 대비해서 기기의 증설이나 가용성을 고려한설계가 필요하다.
    - 물리적 배선을 이용함으로, 이사, 조직변경에 의한 네트워크 구성변경등은 쉽지 않음 따라서 자유도가 낮고 운용 부하도 높아진다.
- 가상 네트워크를 구현하는 기술
  - 여러기술 중 하나로 OpenFlow가 있다.
  - 일반적인 물리 네트워크 기기는 내부에 데이터 플레인과 컨트롤 플레인이라는 두 종류의 기능이 있음
    - 데이터 플레인 : 네트워크 패킷을 물리적으로 전송하는 기능 제공
    - 컨트롤 플레인 : 데이터 플레인에 대해서 어떻게 전송할지 지시를 내림
  - OpenFlow에서는 네트워크 제어를 관리하는 컨트롤 플레인을 네트워크 기기에서 떼어내 서버에서 실행하는 소프트웨어로 구현한다. 이것으로 여러 네트워크 기기를 단일 소프트웨어로 묶어서 제어 할 수 있게된다. OpenFlow에서 네트워크 기기를 집중적으로 제어함으로써 단일 물리 네트워크에 마치 여러 네트워크가 존재하는 것어럼 보이게 함. Google 내부에서도 OpenFlow를 이용하는것으로 알려져 있음.



## 3.2 GCP의 네트워크 구조

```
GCP의 글로벌 네트워크는 세계 최개 규모
수천 마일에 이르는 광케이블의 고속 네트워크망과 거대한 네트워크를 효율적으로 관리하고 제어하는 SDN (Software Define Network)구조등 기술적으로 봐야할 것들이 많다.
```



### 3.2.1 GCP의 네트워크 구성

- GCP에서는 하나의 프로젝트 안에 여러 네트워크를 정의 할 수 있다. (가상 네트워크) 두가지 네트워크 중 하나를 정해 정의한다.
  - Subnet 구성
    - 지역(Region)마다 독립된 서브넷을 배치한 구성의 네트워크
    - 각각의 지역은 전용 내부 네트워크에 서로 연결되어 인터넷을 경유하지 않고 가상 네트워크의 사설 IP채로 지역끼리통신 가능
    - 온프레미스 환경에서는 일반적으로 네트워크 기기의 물리적인 배치에 맞추서 서브넷을 정의한다.
    - 클라우드 같이 물리적인 배치를 의시작하지 않는 가상화 네트워크에서는 반드시 서브넷을 이용 할 필요는 없다.
    - 서브 넷을 이용하는 것으로 그룹과 조직을 바탕으로 IP주소를 관리 할 수 있는 운용상의 이점이 있다.
  - Legacy Network(비 서브넷)구성
    - GCP 프로젝트 안에서 **전 세계 모든 지역에 걸친 하나의 사설 네트워크**를 가진 네트워크 구성 한다.
    - Legacy Netowrk 구성의 네트워크에서는 인스턴스에 할당된 IP주소가 지역마다 그룹하되지 않는다.
    - 라벨읠 활용한 패킷필터링을 통해 유연하고 주성화된 접근 제어가 가능
- Cloud Console에서는 Subnet Network 구성의 네트워크만을 만들 수 있음
- Lecacy Network구성의 네트워크를 만들때는 gcloud 명령을 사용
  - Lacacy Network 구성 네트워크와 Subnet Network구성의 네트워크는 베타적인 관계가 아님 두 구성 모두 적용된 네트워크를 정의 해서 이용가능.

> #### Google Colud Platform내부 네트워크 구성
>
> Ourl Infrastructure
>
> URL : https://peering.google.com/#/infrastructure



### 3.2.2 글로벌 IP 주소 

- External IP(외부 IP) : GCP에서 인터넷에 접속하기 위한 글로벌 IP주소

  - Ephemeral(임시 유형) 

    -  인스턴스를 시작하면 자동 할당, 인스턴스를 정지나 재시작시 IP주소가 바뀔 가능성있음.

  - Static(정적 유형) 

    - 영구적으로 IP주소 확보

    ​

### 3.2.3 GCP의 방화벽 기능

- GCP에서는 VM 인스턴스와 통신을 제어하는 보안 기능이 있는 방화벽이 존재

- 프로젝트안에 준비된 네트워크에 연결된 기능

- 방화벽이 설정되지 않는 네트워크는 모두 블록

- SSH등 시스템 관리에 필요한 최소한의 트레픽을 허가하는 룰은 사전에 준비되어 있음

- 방화벽의 룰은 네트워크마다 정의

- 룰과 태그 조합으로 패킷 필터링

  - 룰
    - 패킷 전송을 허가 할 것인지 결정하는 ACL에 해당
    - 처리대상의 패킷을 특정하는 조건
      - 프로토콜
      - 포트번호
      - 송신처
        - IP범위
        - 서브넷
        - 태그
      - 수신처
        - 태그
  - 태그
    - 각각 인스턴스에 붙이는 임의의 라벨
    - 하나의 인스턴스에 여러 태그 설정 가능

  >#### AWS 방화벽 기능과 차이
  >
  >AWS의 방화벽 기능에는 보안그룹(Security Group)을 사용해서 패킷을 필터링한다. 하나의 보안 구룹은 여러 ACL을 묶어서 정의한 것으로, 인스턴스에 대한 보안 그룹을 적용해서 해당 ACL이 적용된다. 따라서 인스턴스 역할마다 해당하는 보안 그룹을 준비하고, 그 보안 그룹에 인스턴스를 할당하는 것이 기본개념이다.



### 3.2.4 Cloud Load Balancing으로 부하 분산 기능 사용하기

Cloud Load Baclancing은 여러 VM인스턴스에 대해서 인터넷 트래픽을 분산하는 서비다. 여러지 역이나 영역에 걸처 부하 분산하는 것으로, 독립된 Failure zone에 확상성을 이룰 수 있다.

해당하는 프로토콜에 따라서 다음 두가지 Load Balanc를  이용 할 수있다.

- HTTP(S) 

  - 웹 어플리케이션을 외부에 공개할때 적합한 서비스
  - 전 세계 여러지역에 처리 가능 (위치 의식 X) 
  - HTTPS 트래픽에 대해서는SSL Terminaltion도 Cloud Load Balancing에서 처리 한다. 

- TCP/UDP

  - HTTP(S)와 달리 여러지역에 걸친 Load Balancing을 하지 않음
  - 같은 지역의 다른 영역끼리 Load Balancing

  ​

### 3.2.4 Cloud DNS로 이름 해결

Cloud DNS는 DNS(Domain Name Service)의 관리형 서비스

Cloud DNS의 개요

- DNS 참조(Lookup)기능
- 높은 확장성과 가용성
  - Google이 자사 서비스용으로 전 세계에 운용하는 DNS 서버를 사용해, 여러 지역으로부터의 DNS 참조를 지원하고, 높은 가용성과 확장성을 자랑함. SLA는 Google의 권위 네임서버에 대해서 100%가용 성을 보장하는 것이 특징
- 관리 도구 제공
  - DNS레코드는 GUI인 Cloud Console나 gcloud 명령으로 레코드 관리 가능, REST API를 통해 내부에서 DNS 레코드를 동적으로 조작능



## 3.2 Cloud Load Balancing으로 부하 분산 시스템 구축하기

> 실습.

### 3.3.1 ~ 