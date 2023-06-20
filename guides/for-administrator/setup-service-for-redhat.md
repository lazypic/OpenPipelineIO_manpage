---
description: CentOS, RockeyLinux, AWS 리눅스에서 서비스를 설정하는 방법을 다룹니다.
---

# Setup Service for RedHat

Linux에서 서비스를 설정하기 위해 먼저 서비스 파일을 생성합니다.

```bash
sudo vim /lib/systemd/system/OpenPipelineIO.service
```

파일의 내용은 다음과 같습니다.

```
[Unit]
# 서비스 설명
Description=OpenPipelineIO
# 파일이 존재하는지 절대 경로로 된곳에 없으면 실패처리
ConditionPathExists=/usr/local/OpenPipelineIO/bin/OpenPipelineIO
# mongoDB가 필요한 서비스이다. DB가 돌아야 실행될 수 있다.
Requires=mongodb.service
# network가 커널에서 active 된 이후 이 서비스를 실행한다.
After=network.target mongodb.service

[Service]
# 자식 프로세스를 생성이 완료되는 단계까지를 systemd 가 시작이 완료되었다고 판단하게 된다.
# 부모 프로세스를 추적할 수 있도록 PIDFile= 필드에 PID 파일을 선언해 주어야 한다.
Type=simple
# 해당 서비스를 실행시킬 계정
User=root
# 해당 서비스를 실행시킬 그룹
Group=root
# Restart 조건 (on-failure : 오류발생시 재시작, always : 항상)
Restart=on-failure
# 재시작 간격 10초 (단위 표기가 없음 초단위, 5min 20s : 5분 20초, 100ms : 0.1초)
RestartSec=10
# 작업경로
WorkingDirectory=/usr/local/OpenPipelineIO/bin/
#ExecStartPre=pkill openpipelineio
#ExecStartPre=pkill :80
#실행파일 경로
ExecStart=/usr/local/OpenPipelineIO/bin/OpenPipelineIO -http :443 -reviewrender -scanplaterender -certfullchanin /etc/letsencrypt/live/openpipeline.io/fullchain.pem -certprivkey /etc/letsencrypt/live/openpipeline.io/privkey.pem -authmode -signupaccesslevel 0
ExecStop=/bin/kill -TERM $MAINPID
Environment="LD_LIBRARY_PATH=/root/app/OpenImageIO/lib64:/root/app/OpenImageIO_src/ext/dist/lib:/root/app/OpenImageIO_src/ext/dist/lib64"
Environment="OCIO=/root/app/OpenColorIO-Configs/aces_1.2/config.ocio"
Environment="UNIDOC_LICENSE_API_KEY=0000000000000000000000000000000000000000000000000000000000000000"
#ExecStopPost=/bin/pkill openpipelineio

#로그 설정
PermissionsStartOnly=true
ExecStartPre=/bin/mkdir -p /var/log/OpenPipelineIO
ExecStartPre=/bin/chown root:root /var/log/OpenPipelineIO
ExecStartPre=/bin/chmod 755 /var/log/OpenPipelineIO
StandardOutput=syslog
StandardError=syslog
# 로그 인식자
SyslogIdentifier=OpenPipelineIO

[Install]
# 모든 유저를 위해 실행(runlevel3 환경이 셋업될 때)
WantedBy=multi-user.target
```

편집이 끝나면 644 권한을 줍니다.

```
chmod 644 /lib/systemd/system/OpenPipelineIO.service
```

## 로그 설정

rsyslog.d에 OpenPipelineIO를 에 추가합니다.

```
vim /etc/rsyslog.d/OpenPipelineIO.conf
```

```
if $programname == 'OpenPipelineIO' then
/var/log/OpenPipelineIO/output.log
& stop
```

rsyslog 서비스를 재시작 합니다.

```
systemctl restart rsyslog.service
```

root 유저라면 다음처럼 실행합니다.

```
systemctl daemon-reload
systemctl enable OpenPipelineIO.service
systemctl start OpenPipelineIO
systemctl status OpenPipelineIO
```

로그 확인은 다음처럼 볼 수 있다.

```
cat /var/log/OpenPipelineIO/output.log
```
