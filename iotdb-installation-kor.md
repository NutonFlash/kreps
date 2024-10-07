# 오프라인 Linux 서버에서 Apache IoTDB 1.3.2에 대한 포괄적인 설치 가이드

이 가이드는 인터넷 접속이 없는 깨끗한 Linux 서버에 **Apache IoTDB 1.3.2**(단일 노드 및 클러스터 설정 모두)를 설치하고 구성하는 단계별 지침을 제공합니다. 모든 필수 도구와 바이너리는 미리 다운로드하고 USB를 통해 전송한 다음 대상 서버에 설치해야 합니다.

## 목차

1. [필수 구성 요소](#prerequisites)
2. [필수 도구 및 바이너리](#required-tools-and-binaries)
3. [USB 드라이브 준비](#preparing-the-usb-drive)
4. [설치 단계](#installation-steps)
5. [Java Runtime Environment(JRE) 8 설치](#1-install-java-runtime-environment-jre-8)

   - [a. JRE를 대상 서버로 전송](#a-transfer-jre-to-target-server)
   - [b. JRE 설치](#b-install-jre)

6. [추가 도구 설치](#2-install-additional-tools)

   - [a. 추가 도구를 대상 서버로 전송](#a-transfer-additional-tools-to-target-server)
   - [b. 추가 도구 설치](#b-install-additional-tools)

7. [Apache IoTDB 설치](#3-install-apache-iotdb)

   - [a. IoTDB 패키지 전송](#a-transfer-iotdb-package)
   - [b. IoTDB 설치](#b-install-iotdb)

8. [단일 노드 IoTDB 구성 및 시작](#4-configure-and-launch-single-node-iotdb)

   - [a. 단일 노드 구성](#a-single-node-configuration)
   - [b. IoTDB 단일 노드 시작](#b-launch-iotdb-single-node)

9. [IoTDB 클러스터 구성 및 시작](#5-configure-and-launch-iotdb-cluster)

   - [a. 클러스터 구성](#a-cluster-configuration)
   - [b. IoTDB 클러스터 시작](#b-launch-iotdb-cluster)

10. [설치 후 검증](#post-installation-verification)
11. [문제 해결](#troubleshooting)
12. [Apache IoTDB 공식 배포 가이드](#apache-iotdb-official-deployment-guide)

## 필수 조건

- **Linux 서버**: 깨끗한 Linux OS가 설치된 실제 서버(예: Ubuntu, CentOS).
- **USB 드라이브**: 필요한 모든 설치 파일과 도구를 보관할 수 있는 충분한 저장 용량.
- **액세스 자격 증명**: 모든 대상 서버에서 루트 또는 sudo 액세스.
- **네트워크 구성**: 클러스터 설정을 위해 서버가 네트워크를 통해 서로 통신할 수 있는지 확인합니다.

## 필수 도구 및 바이너리

1. **Java Runtime Environment(JRE) 8**

   - **버전**: JRE 8
   - **다운로드**: [Oracle JRE 8 다운로드](https://www.oracle.com/java/technologies/downloads/?er=221886#license-lightbox)

2. **Apache IoTDB 1.3.2**

   - **버전**: Apache IoTDB 1.3.2
   - **다운로드**: [Apache IoTDB 1.3.2 바이너리](https://www.apache.org/dyn/closer.cgi/iotdb/1.3.2/apache-iotdb-1.3.2-all-bin.zip)

3. **추가 도구**

   - **SSH 서버 및 클라이언트**: 클러스터 통신용
   - **압축 해제 유틸리티**: 압축 파일 추출용
   - **텍스트 편집기**: Nano

4. **Java 스크립트**

   - IoTDB 작업을 위해 개발된 모든 사용자 지정 Java 스크립트.

## USB 드라이브 준비

1. **디렉토리 구조 만들기**:

   ```bash
   mkdir -p USB_Install/{jre,iotdb,scripts,tools,additional_tools}
   ```

2. **바이너리 및 도구 다운로드**:

   - **JRE 8**: 적절한 JRE 8 tarball 또는 설치 프로그램을 다운로드하여 `USB_Install/jre/`에 넣습니다.
   - **Apache IoTDB 1.3.2**:
   - `apache-iotdb-1.3.2-all-bin.zip` 파일을 다운로드하여 `USB_Install/iotdb/`에 넣습니다.
   - **추가 도구**:
   - SSH 서버 및 클라이언트 패키지, 압축 해제 유틸리티 및 기타 필요한 도구를 다운로드합니다. 바이너리를 `USB_Install/additional_tools/`에 넣습니다.
   - **Java 스크립트**: 모든 사용자 지정 Java 스크립트를 `USB_Install/scripts/`에 넣습니다.

3. **다운로드 확인**: 모든 파일이 올바르게 다운로드되었고 손상되지 않았는지 확인합니다.

4. **USB 안전하게 꺼내기**: 모든 파일이 복사되면 USB 드라이브를 안전하게 꺼내 데이터 손상을 방지합니다.

## 설치 단계

### 1. Java Runtime Environment(JRE) 8 설치

#### a. JRE를 대상 서버로 전송

1. **USB를 대상 Linux 서버에 삽입**합니다.
2. **USB 마운트**(자동으로 마운트되지 않은 경우):

   ```bash
   sudo mkdir -p /mnt/usb
   sudo mount /dev/sdX1 /mnt/usb # sdX1을 실제 장치 식별자로 바꿉니다.
   ```

3. **JRE 패키지 복사**:

   ```bash
   cp /mnt/usb/USB_Install/jre/jre-8uXXX-linux-x64.tar.gz /opt/
   ```

   _`jre-8uXXX-linux-x64.tar.gz`를 실제 JRE 8 파일 이름으로 바꿉니다._

#### b. JRE 설치

1. **JRE 추출**:

   ```bash
   cd /opt/
   sudo tar -xzf jre-8uXXX-linux-x64.tar.gz
   ```

2. **환경 변수 설정**:

   - 텍스트 편집기로 `/etc/profile`을 엽니다.

   ```bash
   sudo nano /etc/profile
   ```

   - 마지막에 다음 줄을 추가합니다.

   ```bash
   export JAVA_HOME=/opt/jre1.8.0_XXX
   export PATH=$PATH:$JAVA_HOME/bin
   ```

   _`jre1.8.0_XXX`를 실제 JRE 디렉터리 이름으로 바꿉니다._

   - 편집기를 저장하고 종료합니다.

3. **변경 사항 적용**:

   ```bash
   source /etc/profile
   ```

4. **설치 확인**:

   ```bash
   java -version
   ```

   _예상 출력_: JRE 8을 나타내는 Java 버전 정보.

### 2. 추가 도구 설치

#### a. 추가 도구를 대상 서버로 전송

1. **위에서 설명한 대로 USB가 마운트되었는지 확인합니다.**
2. **추가 도구 패키지 복사**:

   ```bash
   cp /mnt/usb/USB_Install/additional_tools/ssh-server.deb /opt/
   cp /mnt/usb/USB_Install/additional_tools/unzip.deb /opt/
   cp /mnt/usb/USB_Install/additional_tools/nano.deb /opt/
   ```

#### b. 추가 도구 설치

1. **SSH 서버 및 클라이언트 설치**:

   ```bash
   sudo dpkg -i /opt/ssh-server.deb
   ```

2. **압축 해제 유틸리티 설치**:

   ```bash
   sudo dpkg -i /opt/unzip.deb
   ```

3. **텍스트 편집기(나노) 설치**:

   ```bash
   sudo dpkg -i /opt/nano.deb
   ```

4. **SSH 서비스 활성화 및 시작**:

   ```bash
   sudo systemctl enable ssh
   sudo systemctl start ssh
   ```

5. **설치 확인**:

   ```bash
   ssh -V
   unzip -v
   nano --version
   ```

### 3. Apache IoTDB 설치

#### a. IoTDB 패키지 전송

1. **IoTDB 패키지 복사**:

   ```bash
   cp /mnt/usb/USB_Install/iotdb/apache-iotdb-1.3.2-all-bin.zip /opt/
   ```

   #### b. IoTDB 설치

   1. **IoTDB 추출**:

   ```bash
   cd /opt/
   sudo unzip apache-iotdb-1.3.2-all-bin.zip
   ```

2. **IoTDB 환경 변수 설정**(선택 사항이지만 권장):

   - `/etc/profile`을 엽니다.

   ```bash
   sudo nano /etc/profile
   ```

   - 다음을 추가합니다.

   ```bash
   export IOTDB_HOME=/opt/apache-iotdb-1.3.2
   export PATH=$PATH:$IOTDB_HOME/bin
   ```

   - 저장하고 종료합니다.

   - 변경 사항 적용:

   ```bash
   source /etc/profile
   ```

### 4. 단일 노드 IoTDB 구성 및 시작

#### a. 단일 노드 구성

1. **IoTDB 구성 디렉토리로 이동**:

   ```bash
   cd $IOTDB_HOME/conf/
   ```

2. **공통 구성 편집**:

   ```bash
   sudo nano iotdb-common.properties
   set cluster_name=defaultCluster
   set schema_replication_factor=<number> // 예를 들어 3
   set data_replication_factor=<number> // 예를 들어 2
   종료하고 파일 저장
   ```

3. **ConfigNode에 대한 메모리 할당 설정**:

   ```bash
   sudo nano confignode-env.sh
   set MEMORY_SIZE='your value'
   종료하고 파일 저장
   ```

4. **DataNode에 대한 메모리 할당 설정**:

   ```bash
   sudo nano datanode-env.sh
   set MEMORY_SIZE='your value'
   종료하고 파일 저장
   ```

5. **ConfigNode 구성 편집**:

   ```bash
   sudo nano iotdb-confignode.properties
   set cn_internal_address=<머신의 이름/IP> // 예를 들어 node-1 중 하나
   set cn_seed_config_node=<머신의 이름/IP>:<10710> // 예를 들어 node-1:10710
   종료하고 파일 저장
   ```

6. **DataNode 구성 편집**:

   ```bash
   sudo nano iotdb-datanode.properties
   set dn_rpc_address=0.0.0.0
   set dn_internal_address=<머신의 이름/IP> // 예를 들어 node-1 중 하나
   set dn_seed_config_node=<머신의 이름/IP>:<10710> // 예를 들어 node-1:10710
   종료하고 파일 저장
   ```

#### b. IoTDB 단일 노드 시작

1. **IoTDB 시작**:

   ```bash
   ./$IOTDB_HOME/sbin/start-standalone.sh
   ```

2. **IoTDB 요구 사항 충족 확인**:

   ```bash
   ./$IOTDB_HOME/sbin/health_check.sh -ips <node-1>
   ```

3. **IoTDB CLI 액세스**:

   ```bash
   ./$IOTDB_HOME/sbin/start-cli.sh
   ```

   _참고_: `exit`를 사용하여 CLI를 종료합니다.

### 5. IoTDB 클러스터 구성 및 시작

#### a. 클러스터 구성

1. **IoTDB 구성 디렉토리로 이동**:

   ```bash
   cd $IOTDB_HOME/conf/
   ```

2. **클러스터 구성 편집**":

   ```bash
   sudo nano iotdb-cluster.properties
   set confignode_address_list=<구성 노드 목록> // 예를 들어, node-1, node-2, node-3
   set datanode_address_list=<데이터 노드 목록> // 예를 들어, node-1, node-2, node-3
   set confignode_deploy_path=<iotdb/data 경로> // 예를 들어, /opt/iotdb/data
   set datanode_deploy_path=<iotdb/data 경로> // 예를 들어, /opt/iotdb/data
   종료하고 파일 저장
   ```

3. **일반 구성 편집**:

   ```bash
   sudo nano iotdb-common.properties
   set cluster_name=defaultCluster
   set schema_replication_factor=<숫자> // 예를 들어 3
   set data_replication_factor=<숫자> // 예를 들어 2
   종료하고 파일 저장
   ```

4. **ConfigNode에 대한 메모리 할당 설정**:

   ```bash
   sudo nano confignode-env.sh
   set MEMORY_SIZE='사용자 값'
   종료하고 파일 저장
   ```

5. **DataNode에 대한 메모리 할당 설정**:

   ```bash
   sudo nano datanode-env.sh
   set MEMORY_SIZE='사용자 값'
   종료하고 파일 저장
   ```

6. **ConfigNode에 대한 구성 편집**:

   ```bash
   sudo nano iotdb-confignode.properties
   set cn_internal_address=<머신의 이름/IP> // 예를 들어 node-1 | node-2 | node3
   set cn_seed_config_node=<name/ip of main machine>:<10710> // 예를 들어, node-1:10710(모든 노드에서 동일해야 함)
   종료하고 파일 저장
   ```

7. **DataNode 구성 편집**:

   ```bash
   sudo nano iotdb-datanode.properties
   set dn_rpc_address=0.0.0.0
   set dn_internal_address=<name/ip of machine> // 예를 들어, node-1 | node-2 | node3
   set dn_seed_config_node=<name/ip of main machine>:<10710> // 예를 들어, node-1:10710(모든 노드에서 동일해야 함)
   종료하고 파일 저장
   ```

8. **호스트 파일 구성**(각 서버에서):

   - `/etc/hosts`를 엽니다.

   ```bash
   sudo nano /etc/hosts
   ```

   - 모든 클러스터 노드에 대한 항목을 추가합니다.

   ```plaintext
   192.168.1.1 node-1
   192.168.1.2 node-2
   192.168.1.3 node-3
   ```

   - 저장하고 종료합니다.

9. **구성 배포**:

   - 모든 클러스터 노드에 동일한 구성이 있는지 확인합니다.
   - 필요한 경우 USB를 사용하여 고유한 구성 파일을 전송합니다.

#### b. IoTDB 클러스터 시작

1. **구성 노드 시작**:

   먼저 기본 구성 노드를 시작하여 시드 구성 노드 노드가 먼저 시작되도록 한 다음, 두 번째 및 세 번째 구성 노드 노드를 순서대로 시작합니다.

   각 Config Node 서버에서:

   ```bash
   ./$IOTDB_HOME/sbin/start-confignode.sh
   ```

2. **데이터 노드 시작**:

   각 데이터 노드 서버에서:

   ```bash
   ./$IOTDB_HOME/sbin/start-datanode.sh
   ```

3. **IoTDB 요구 사항 충족 확인**:

   ```bash
   ./$IOTDB_HOME/sbin/health_check.sh -ips <node-1>
   ```

4. **IoTDB CLI 액세스**:

   ```bash
   ./$IOTDB_HOME/sbin/start-cli.sh
   ```

   _참고_: `exit`를 사용하여 CLI를 종료합니다.

## 설치 후 확인

1. **서비스 상태 확인**:

   - 모든 IoTDB 서비스가 활성화되어 있는지 확인합니다.

   ```bash
   ps -aux | grep java
   ```

2. **테스트 데이터 수집 및 쿼리**:

   - IoTDB CLI를 사용하여 테스트 데이터베이스를 만들고, 데이터를 삽입하고, 쿼리를 수행합니다.

   ```sql
   CREATE DATABASE root.test;
   INSERT INTO root.test.d1(timestamp, value) VALUES(1, 100);
   SELECT * FROM root.test.d1;
   ```

3. **로그 검토**:

   - IoTDB 로그에서 오류나 경고를 확인합니다.

   ```bash
   tail -f $IOTDB_HOME/logs/<needed-log-file>.log
   ```

## 문제 해결

- **Java를 찾을 수 없음**:

  - `JAVA_HOME`이 올바르게 설정되어 내보내졌는지 확인합니다.
  - Java 설치 경로를 확인합니다.

- **IoTDB 서비스 시작 실패**:

  - 오류 메시지에 대한 로그 파일을 확인합니다.
  - 필수 포트가 열려 있고 사용 중이 아닌지 확인합니다.
  - `conf` 폴더에서 적절한 구성을 확인합니다.

- **클러스터 노드가 통신하지 않음**:

  - 서버 간 네트워크 연결을 확인합니다.
  - 정확성을 위해 `/etc/hosts` 항목을 확인합니다.
  - 모든 노드에서 일관된 구성을 확인합니다.

- **권한 문제**:
  - IoTDB 디렉터리에 적절한 읽기/쓰기 권한이 있는지 확인합니다.
  - 충분한 권한으로 시작 스크립트를 실행합니다.

## Apache IoTDB 공식 배포 가이드

- [독립 실행형 배포](https://iotdb.apache.org/UserGuide/latest/Deployment-and-Maintenance/Stand-Alone-Deployment_apache.html)
- [클러스터 배포](https://iotdb.apache.org/UserGuide/latest/Deployment-and-Maintenance/Cluster-Deployment_apache.html)
