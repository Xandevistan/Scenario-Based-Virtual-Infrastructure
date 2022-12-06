# Scenario-Based-Virtual-Infrastructure

## Scenario

## 가상 인프라 구성 및 요구 사항
**1. Web Service Server**
```
- 웹 페이지를 외부에서 접속 할 때 서비스를 제공하는 서버
- 서버구성: 웹(Apache) 및 WAS(Tomcat)
- DB 서버와 웹 소켓 연결
```
**2. DB Server**
```
- 해당 웹 서비스의 회원 정보를 저장하는 서버
- MariaDB
```
**3. Develeopment Server**
```
- 웹 서비스 서버에 소스 코드를 업로드 하기 전 테스팅 및 개발 시 사용하는 서버
- FTP를 이용하여 웹 서비스 서버와 통신 구간 연결
- 개발자 PC(호스트)와 SSH 터미널 연결
```

**4. Log Server**
```
- DB 서버의 데이터를 덤프 저장
```

**5. DB Backup Server**
```
- 웹 서비스, DB 서버의 로그 저장
- 주기적으로 웹 서비스 서버 및 DB 서버에서 로그 전송
```
 
#
### 각 구성 요소 버전 및 정보

- **Apache** : Apache/2.4.29
- **Tomcat** : Apache Tomcat/9.0.16
- **MariaDB** : 10.1.48-MariaDB

| 서비스 명 | 고정아이피 | Hard Disk | RAM |
| --- | --- | --- | --- |
| Web Service Server | 192.168.0.151 | 20GB | 2GB |
| DB Server | 192.168.0.152 | 20GB | 2GB |
| Development Server | 192.168.0.153 | 20GB | 2GB |
| Log Server | 192.168.0.154 | 20GB | 2GB |
| DB Backup Server | 192.168.0.155 | 20GB | 2GB |
