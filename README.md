# 🛠️Elasticsearch & Kibana 설치 및 NAT 네트워크 설정 가이드

## 📋목차 
1. [📚 프로젝트 개요](#-프로젝트-개요)
2. [🔧 활용 도구 및 기술](#-활용-도구-및-기술)
3. [🖥️ 환경 구성 및 서버 접속](#️-환경-구성-및-서버-접속)
4. [🗃️ Elasticsearch 설치 (myserver1)](#️-elasticsearch-설치-myserver1)
5. [📊 Kibana 설치 (myserver2)](#-kibana-설치-myserver2)
6. [🌐 NAT 네트워크 설정 및 서버 간 통신 및 브라우저에서 접속 설정](#-nat-네트워크-설정-및-서버-간-통신-및-브라우저에서-접속-설정)
7. [🌍 윈도우 브라우저에서 접속 설정](#-윈도우-브라우저에서-접속-설정)
8. [💭 고찰](#-고찰)
---
## 📚 프로젝트 개요
>VirtualBox에서 두 개의 Ubuntu 서버를 생성한 후, 포트 포워딩을 통해 MobaXterm으로 접속하고 각각 Elasticsearch와 Kibana를 설치한 뒤, 두 서버 간 통신을 위해 NAT 네트워크를 설정하는 과정을 진행했습니다. 또한, Windows 환경에서 브라우저로 Elasticsearch와 Kibana에 접속할 수 있도록 설정하는 과정을 진행했습니다.

### 목표
- VirtualBox를 사용하여 두 개의 Ubuntu 서버를 생성
- 각각의 서버에 포트 포워딩 설정 후 MobaXterm으로 접속
- myserver1에 Elasticsearch, myserver2에 Kibana 설치
- 두 서버 간 통신을 위해 NAT 네트워크를 구성
- Windows 브라우저에서 Elasticsearch와 Kibana에 접근할 수 있도록 설정

### 서버 구성
- myserver1: Elasticsearch 설치 서버
- myserver2: Kibana 설치 서버

## 🔧 활용 도구 및 기술
![VirtualBox](https://img.shields.io/badge/VirtualBox-4E2A84?style=for-the-badge&logo=virtualbox&logoColor=white)
![Ubuntu](https://img.shields.io/badge/Ubuntu-E95420?style=for-the-badge&logo=ubuntu&logoColor=white)
![MobaXterm](https://img.shields.io/badge/MobaXterm-3C3C3C?style=for-the-badge&logo=mobaxterm&logoColor=white) <br>
![Elasticsearch](https://img.shields.io/badge/Elasticsearch-005571?style=for-the-badge&logo=elasticsearch&logoColor=white)
![Kibana](https://img.shields.io/badge/Kibana-005571?style=for-the-badge&logo=kibana&logoColor=white) <br>
![YAML](https://img.shields.io/badge/YAML-11B48A?style=for-the-badge&logo=yaml&logoColor=white)
![curl](https://img.shields.io/badge/curl-2E8C8C?style=for-the-badge&logo=curl&logoColor=white)

## 🖥️ 환경 구성 및 서버 접속

>### 초기 환경 구성 및 서버 접속

### 1. 두 개의 가상 서버 생성 및 초기 설정

#### myserver1
- OS: `Ubuntu 24.04.1 LTS`
- 네트워크 방식 : `NAT`
#### myserver2
- OS: `Ubuntu 24.04.1 LTS`
- 네트워크 방식 : `NAT`

<br>

```bash
# OpenSSH 서버 설치 후 상태 확인
sudo apt update
sudo apt install openssh-server -y
sudo systemctl status ssh
sudo systemctl start ssh
sudo systemctl status ssh

# 방화벽 설정
sudo ufw allow ssh
sudo ufw status

# ip 주소 확인
ip addr 
# 또는
sudo apt install net-tools
ifconfig

# 시간 변경
timedatectl 
timedatectl list-timezones | grep Seoul
sudo timedatectl set-timezone Asia/Seoul
timedatectl
```
### 2. 포트 포워딩 설정
VirtualBox에서 각 우분투 가상 서버(myserver1, myserver2)에 MobaXterm으로 SSH 접속할 수 있도록 포트 포워딩을 설정합니다.  
이를 통해 호스트 머신에서 가상 머신의 SSH 서비스에 접근할 수 있습니다.
<br><br>
각 가상 머신에 고유한 호스트 포트를 할당하여, 호스트 머신에서 여러 가상 머신을 동시에 관리할 수 있도록 구성합니다. <br>
가상 머신의 게스트 포트는 SSH 서비스의 기본 포트인 `22`를 유지하며, 호스트 포트를 변경하여 가상 머신을 구분합니다.

#### myserver1
- 호스트 ip: `127.0.0.1`
- 호스트 포트: `22`
- 게스트 ip: `10.0.2.15`
- 게스트 포트: `22`
#### myserver2
- 호스트 ip: `127.0.0.1`
- 호스트 포트: `23`
- 게스트 ip: `10.0.2.15`
- 게스트 포트: `22`

<br>

<div style="display: flex; justify-content: space-between;">
    <img src="https://github.com/user-attachments/assets/c078309a-0cfb-4669-aa54-a28f33d9ce17" alt="Image 1" style="width: 49%;"/>
    <img src="https://github.com/user-attachments/assets/08c12bb3-78b0-46f6-b851-1a23debba519" alt="Image 2" style="width: 49%;"/>
</div>

### 3. MobaXterm으로 서버 접속

#### myserver1 접속
- 프로토콜: `SSH`
- 호스트: `127.0.0.1`
- 포트: `22`
#### myserver2 접속
- 프로토콜: `SSH`
- 호스트: `127.0.0.1`
- 포트: `23`

<br>

<div style="display: flex; justify-content: space-between;">
    <img src="https://github.com/user-attachments/assets/7df608fd-ffe8-4996-8931-18911dd15388" alt="Image 1" style="width: 49%;"/>
    <img src="https://github.com/user-attachments/assets/4e25247c-43df-46dd-859b-0fe72d348ab5" alt="Image 2" style="width: 49%;"/>
</div>

<br>

>### 결과적인 환경 구성 및 서버 접속

#### myserver1
- OS: `Ubuntu 24.04.1 LTS`
- 네트워크 방식 : `NAT 네트워크`
#### myserver2
- OS: `Ubuntu 24.04.1 LTS`
- 네트워크 방식 : `NAT 네트워크`

<br>

#### VirtualBox 네트워크 방식 중 `NAT`, `NAT 네트워크` 비교
| 네트워크 방식 | 설명 | 특징 | 
|-----------|--------|--------|
| **NAT**   | 네트워크 주소 변환(Network Address Translation)을 사용하여, 가상 머신을 외부 네트워크와 연결   | 하나의 공인 IP 주소를 사용해 여러 가상 머신을 연결. 외부와 연결은 가능하지만, 내부 네트워크 간 통신은 불가 |
| **NAT 네트워크** | 동일한 서브넷에 속하는 가상 머신들끼리 네트워크 연결이 가능한 NAT 방식 네트워크 | 외부 통신 가능, 가상 머신들 간에 서로 통신 가능. 여러 가상 머신이 동일 네트워크에 연결됨  | 
<br>

## 🗃️ Elasticsearch 설치 (myserver1)
#### Elasticsearch 버전: `7.11.1`

### 1. Elasticsearch 설치
```bash
# Elasticsearch GPG 키 추가
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -

# Elasticsearch APT 저장소 추가
echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-7.x.list

# 시스템의 패키지 목록을 업데이트
sudo apt update

# Elasticsearch 설치
sudo apt install -y elasticsearch=7.11.1
```

### 2. Elasticsearch 시작
```bash
sudo systemctl status elasticsearch
sudo systemctl enable elasticsearch
sudo systemctl start elasticsearch
sudo systemctl status elasticsearch
```
### 3. 테스트
```bash
curl -X GET "http://127.0.0.1:9200"
```
![Image](https://github.com/user-attachments/assets/d69e27b7-e73a-4562-9ffa-e308f7d6dbd2)

<br>

## 📊 Kibana 설치 (myserver2)
#### Kibana 버전: `7.11.1`

### 1. Kibana 설치
```bash
# 시스템의 패키지 목록을 업데이트
sudo apt update

# 필요한 의존성 패키지를 설치
sudo apt install -y apt-transport-https

# Elasticsearch GPG 키 추가
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -

# Kibana APT 저장소 추가
echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-7.x.list

# 시스템의 패키지 목록을 업데이트
sudo apt update

# Kibana 설치
sudo apt install kibana=7.11.1
```

### 2. Kibana 시작
```bash
sudo systemctl status kibana
sudo systemctl enable kibana
sudo systemctl start kibana
sudo systemctl status kibana
```

### 3. Elasticsearch와 Kibana의 통신 문제

Elasticsearch가 설치된 서버와 Kibana가 설치된 서버의 네트워크 구성이 `NAT`이기 때문에, 기본적으로 서로 통신할 수 없습니다.

**⭐ Kibana가 Elasticsearch에 연결할 수 있도록 설정이 필요합니다.**

<br>

## 🌐 NAT 네트워크 설정 및 서버 간 통신

### 1. `NAT 네트워크` 생성
새 NAT 네트워크를 추가합니다. <br>

![Image](https://github.com/user-attachments/assets/96d58e39-8ba0-4bfa-b970-c8b0e1db93c3)

### 2. 서버의 네트워크 어댑터 변경
myserver1과 myserver2의 네트워크 어댑터를 `NAT 네트워크`로 변경합니다. <br>

<div style="display: flex; justify-content: space-between;">
    <img src="https://github.com/user-attachments/assets/22a2bb2d-c0e9-4b2b-a4f7-fca7d8f9d0d1" alt="Image 1" style="width: 49%;" />
    <img src="https://github.com/user-attachments/assets/e1cc88ab-d3e3-4e8a-9a10-d702b6ff8332" alt="Image 2" style="width: 49%;" />
</div> 

### 3. 포트 포워딩 설정
각 서버를 재시작하여 새 설정이 적용되도록 하고, 각 서버의 `NAT 네트워크` IP를 확인합니다. <br>

#### myserver1
- 호스트 ip: `127.0.0.1`
- 호스트 포트: `22`
- 게스트 ip: `10.0.2.15`
- 게스트 포트: `22`
#### myserver2
- 호스트 ip: `127.0.0.1`
- 호스트 포트: `23`
- 게스트 ip: `10.0.2.4`
- 게스트 포트: `22`

<br>

![Image](https://github.com/user-attachments/assets/7ade1076-d421-4e8d-a89b-897010982c1e)

### 4. MobaXterm으로 다시 서버 접속

#### myserver1 접속
- 프로토콜: `SSH`
- 호스트: `127.0.0.1`
- 포트: `22`
#### myserver2 접속
- 프로토콜: `SSH`
- 호스트: `127.0.0.1`
- 포트: `23`

### 5. 서버 간 통신 테스트 및 설정

### [myserver2에서 myserver1로 ping 테스트]
```bash
ping 10.0.2.15
```
![Image](https://github.com/user-attachments/assets/4fae48a8-c12b-4684-b55e-50b2fb726497)

### [Elasticsearch와 Kibana 연결 설정]
![Image](https://github.com/user-attachments/assets/67ebe2ae-fa58-45e5-b089-4f23b0bab5eb)

Elasticsearch는 기본적으로 localhost에서만 접속 가능하도록 설정되어 있습니다. NAT 네트워크에서 myserver2가 myserver1의 Elasticsearch에 접근하려면 elasticsearch.yml 파일을 수정해야 합니다.
```bash
sudo vi /etc/elasticsearch/elasticsearch.yml
```
```yaml
# 수정 내용
# 외부 접속 허용
network.host: 0.0.0.0
# 기본 포트 명시
http.port: 9200
# 단일 노드 클러스터 모드 설정
discovery.type: single-node
```
```bash
# Elasticsearch 재시작
sudo systemctl restart elasticsearch
```
<br>

myserver2의 Kibana 설정 파일을 수정하여 myserver1의 Elasticsearch와 연결되도록 합니다.
```bash
sudo vi /etc/kibana/kibana.yml
```
```yaml
# 수정 내용
elasticsearch.hosts: ["http://10.0.2.15:9200"]
```
```bash
# Kibana 재시작
sudo systemctl restart kibana

# myserver1의 Elasticsearch가 정상적으로 작동하는지 확인
curl -X GET "http://10.0.2.15:9200"
```
![Image](https://github.com/user-attachments/assets/3a83434c-9f08-4151-b345-c328affef069)

<br>

## 🌍 윈도우 브라우저에서 접속 설정
#### Elasticsearch와 Kibana 각각의 설정 파일에서 외부 접속 허용을 설정하고, 포트 포워딩을 통해 호스트 머신에서 브라우저로 접근할 수 있도록 설정합니다.
<br>

### [Elasticsearch, Kibana 설정 변경]

#### myserver1에서 Elasticsearch 설정 파일 수정
```bash
sudo vi /etc/elasticsearch/elasticsearch.yml
```
```yaml
# 수정 내용(이미 이전 과정에서 수정함)
# 외부 접속 허용
network.host: 0.0.0.0
# 기본 포트 명시
http.port: 9200
# 단일 노드 클러스터 모드 설정
discovery.type: single-node
```
```bash
# Elasticsearch 재시작
sudo systemctl restart elasticsearch
```

#### myserver2에서 Kibana 설정 파일 수정
```bash
sudo vi /etc/kibana/kibana.yml
```
```yaml
# 수정 내용
# 외부 접속 허용
server.host: "0.0.0.0"
```
```bash
# Kibana 재시작
sudo systemctl restart kibana
```
<br>

### [포트 포워딩 설정]
VirtualBox에서 각 우분투 가상 서버(myserver1, myserver2)에 브라우저를 통해 Elasticsearch와 Kibana에 접근할 수 있도록 포트 포워딩을 설정합니다. <br>
이를 통해 호스트 머신에서 가상 머신의 Elasticsearch 및 Kibana 서비스에 접근할 수 있습니다.

#### myserver1
- 호스트 ip: `127.0.0.1`
- 호스트 포트: `9200`
- 게스트 ip: `10.0.2.15`
- 게스트 포트: `9200`
#### myserver2
- 호스트 ip: `127.0.0.1`
- 호스트 포트: `5601`
- 게스트 ip: `10.0.2.4`
- 게스트 포트: `5601`

<br>

<div style="display: flex; justify-content: space-between;">
    <img src="https://github.com/user-attachments/assets/9d19d7ce-15d1-45a1-83de-01196d8d4ffd" alt="Image 1" style="width: 49%;" />
    <img src="https://github.com/user-attachments/assets/2220f283-c09f-48e6-81f4-b8c354295e45" alt="Image 2" style="width: 49%;" />
</div>
<br>

### [윈도우 브라우저에서 접속 테스트]
<div style="display: flex; justify-content: space-between;">
    <img src="https://github.com/user-attachments/assets/d3d4c422-1dca-4377-8f38-e84b36315467" alt="Image 1" style="width: 49%;" />
    <img src="https://github.com/user-attachments/assets/bb1730b2-68e8-4eca-bf66-15fb5d4d7763" alt="Image 2" style="width: 49%;" />
</div>
<br>

## 💭 고찰
VirtualBox를 이용해 두 개의 Ubuntu 가상 서버를 생성하고, 각각 Elasticsearch와 Kibana를 설치하여 서로 연결하는 과정에서 NAT 네트워크를 활용했습니다. 
NAT 네트워크 방식은 서버 간 통신을 설정하는 데 유용했으나, 아쉬운 점이 있었습니다.

### 동적 IP 할당의 불편함
NAT 네트워크에서는 가상 서버가 부팅할 때마다 동적으로 IP가 할당됩니다. 
이는 각 서버의 IP 주소가 변경될 수 있음을 의미합니다.
따라서 Elasticsearch와 Kibana 서비스를 설정할 때마다 IP를 확인하고 설정을 변경해야 하는 번거로움이 있었습니다.

### 개선 방안
NAT 네트워크에서 고정 IP를 할당하는 방법을 적용하면, 매번 IP를 확인하고 설정을 변경하는 수고를 덜 수 있습니다.
