# RHCSA_v9_통합_실전_모의시험 (1)

# RHCSA EX200 v9 통합 실전 모의시험

> **Exam Duration:** 3 hours (180 minutes)
**Passing Score:** 210 / 300 (70%)
**Environment:** Node1 (servera.lab.example.com) / Node2 (serverb.lab.example.com)
**Important:** All configurations must persist after a system reboot.
> 

> 📌 **이 문서의 구성**
- **파트 A:** 카테고리별 변형 레퍼런스 — 시험에서 바뀔 수 있는 옵션/조건 한눈에 보기
- **파트 B:** 모의시험 25문제 — 영어 시나리오 + 변형 노트 + 풀이 가이드
- **파트 C:** 변형 드릴 6개 — 모의시험에서 커버 못한 옵션 변형 추가 연습
> 

> 🏷️ **난이도 표시**
- 🔴 거의 매번 출제 — 못 풀면 불합격
- 🟡 자주 출제 — 합격 안정권
- 🟢 가끔 출제 — 고득점용
- 🔵 공식 목표에 있지만 실전 보고 없음 — 시간 남으면 공부
> 

---

### A1. tar 압축

# 파트 A: 카테고리별 변형 레퍼런스

> 시험에서 같은 카테고리인데 옵션이나 조건이 바뀌면 못 푸는 상황을 방지하기 위한 레퍼런스.
모의시험(파트 B) 풀기 전에 훑어보고, 풀고 나서 다시 확인.
> 

| 변형 | 옵션 | 확장자 |
| --- | --- | --- |
| gzip | `-czf` | `.tar.gz` |
| bzip2 | `-cjf` | `.tar.bz2` |
- `c` = create, `f` = file, `z` = gzip, `j` = bzip2
- 해제: `x` (extract). `xzf`(gzip) / `xjf`(bzip2)

### A2. find 옵션

| 변형 | 예시 |
| --- | --- |
| 사용자 소유 | `find / -user natasha` |
| 크기 초과 (단일) | `find /etc -size +4M` |
| 크기 범위 | `find /usr/share -type f -size +3k -size -5k` |
- `user` = 소유자, `size` = 크기, `type f` = 파일만
- 단위: `k` = KiB, `M` = MiB, `G` = GiB, `c` = bytes
- 복사: `exec cp -a {} /dest/ \;`
- 에러 숨기기: 끝에 `2>/dev/null`

### A3. LVM 생성 — 파일시스템 변형

| FS | 생성 명령 | 확장 시 |
| --- | --- | --- |
| ext3 | `mkfs.ext3` | `lvextend -r` (resize2fs 자동) |
| ext4 | `mkfs.ext4` | `lvextend -r` (resize2fs 자동) |
| xfs | `mkfs.xfs` | `lvextend -r` (xfs_growfs 자동) |
| vfat | `mkfs.vfat` | 확장 불가 |
- `lvextend -r`이면 FS 종류 상관없이 자동 처리
- fstab에 FS 타입 정확히 적어야 함 (ext3/ext4/xfs)

### A4. LVM 크기 지정 — `L` vs `l`

| 옵션 | 의미 | 예시 |
| --- | --- | --- |
| `-L` (대문자) | 절대 크기 (MB/GB) | `lvcreate -L 500M` / `lvextend -L 300M` |
| `-l` (소문자) | extent 개수 | `lvcreate -l 50` / `lvextend -l +100` |
| `-l +숫자` | 상대값 (추가) | `lvextend -l +100` = 현재 + 100 extents |
- PE 크기 × extent 수 = 최종 크기 (예: 8MB × 50 = 400MB)

### A5. sudo 설정

| 변형 | sudoers 문법 |
| --- | --- |
| 그룹 전체, 모든 명령, NOPASSWD | `%groupname ALL=(ALL) NOPASSWD: ALL` |
| 특정 사용자, 특정 명령, NOPASSWD | `username ALL=(ALL) NOPASSWD: /usr/bin/command` |
| 특정 사용자, 모든 명령, 패스워드 필요 | `username ALL=(ALL) ALL` |
- `%` = 그룹 표시, 없으면 사용자
- 명령어는 반드시 **전체 경로** (예: `/usr/bin/passwd`)
- 파일 위치: `/etc/sudoers.d/파일명`

### A6. 패스워드 에이징

| 변형 | 방법 |
| --- | --- |
| 모든 **새** 사용자 기본값 | `/etc/login.defs` → `PASS_MAX_DAYS 30` |
| 특정 사용자 개별 적용 | `chage -M 30 username` |
- `M` = Max days, `m` = min days, `W` = Warning days, `I` = Inactive days

### A7. cron 시간 표현

| 원하는 주기 | cron 표현 |
| --- | --- |
| 매일 14시 32분 | `32 14 * * *` |
| 매일 12시 30분 | `30 12 * * *` |
| 매 5분마다 | `*/5 * * * *` |
| 매 1분마다 | `*/1 * * * *` (또는 `* * * * *`) |
- 형식: `분 시 일 월 요일 명령어`

### A8. 협업 디렉터리 — 특수 퍼미션

| 비트 | 숫자 | 효과 | 사용처 |
| --- | --- | --- | --- |
| SGID | `2xxx` | 새 파일이 디렉터리의 그룹을 상속 | 협업 디렉터리 |
| Sticky bit | `1xxx` | 파일 소유자만 삭제 가능 | /tmp 같은 공유 디렉터리 |
- SGID 2770 = 그룹 rwx + 타인 없음 + 그룹 상속
- Sticky 1777 = 모두 rwx + 소유자만 삭제 가능

### A9. Podman 볼륨 매핑

| 요소 | 변형 |
| --- | --- |
| 호스트 경로 | 문제마다 다름 (예: `/opt/files`, `/opt/data`) |
| 컨테이너 경로 | 문제마다 다름 (예: `/opt/incoming`, `/usr/share/nginx/html`) |
| SELinux | `:Z` 필수 — 컨텍스트 자동 조정 |
- 형식: `v 호스트경로:컨테이너경로:Z`
- 호스트 디렉터리는 사용자 소유여야 함 (rootless)

### A10. 🔵 추가 공부 카테고리 (공식 목표에 있지만 실전 보고 없음)

| 항목 | 핵심 명령어 | 한 줄 설명 |
| --- | --- | --- |
| 하드 링크 | `ln source link` | 같은 inode 가리킴, 원본 삭제해도 유지 |
| 소프트 링크 | `ln -s source link` | 바로가기, 원본 삭제하면 깨짐 |
| scp | `scp /local/file user@host:/path/` | SSH 기반 파일 복사 |
| sftp | `sftp user@host` → `put file` | SSH 기반 대화형 전송 |
| persistent journal | `/etc/systemd/journald.conf` → `Storage=persistent` | 재부팅 후에도 로그 유지 |
| renice | `renice -n 10 -p PID` | 프로세스 우선순위 변경 |

---

# 파트 B: 모의시험 (25문제)

---

## 📌 Node 1 Tasks

---

Configure the network on Node1 with the following specifications:

### Q1. 🔴 Configure Network and Hostname

- **Hostname:** `servera.lab.example.com`
- **IP Address:** `192.168.1.150`
- **Netmask:** `255.255.255.0` (/24)
- **Gateway:** `192.168.1.1`
- **DNS:** `192.168.1.100`
- The network connection must start automatically on boot.

> 📝 **변형 포인트**
- IP, Gateway, DNS 값만 바뀜 — 명령어 구조는 동일
- DNS와 Gateway가 같은 경우도 있음
- “start automatically on boot” = `autoconnect yes` (nmcli 기본값이지만 확인 필수)
- 시험장에서는 인터페이스가 2개일 수 있음 — `nmcli con show`로 활성 연결 NAME 확인
> 
- 💡 풀이 가이드 (클릭하여 열기)
    
    네트워크 설정은 시험의 출발점이야. 이게 안 되면 repo, NFS, SSH 전부 불가능. 먼저 현재 연결 이름을 확인하고, 그 이름으로 설정을 수정해.
    
    ```bash
    # ==============================
    # 1. 현재 연결 확인
    # ==============================
    
    # con = connection, NAME 열의 이름을 사용해야 함
    # DEVICE 이름(enp1s0 등)이 아니라 CONNECTION NAME!
    nmcli con show
    # 예: NAME        UUID    TYPE      DEVICE
    #     enp1s0      xxx     ethernet  enp1s0
    
    # ==============================
    # 2. 네트워크 설정 변경
    # ==============================
    
    # con mod = connection modify
    # ipv4.method manual = DHCP 끄고 수동 IP 사용
    # 시험에서 method manual 안 하면 DHCP가 IP를 덮어씀!
    nmcli con mod enp1s0 \
      ipv4.addresses 192.168.1.150/24 \
      ipv4.gateway 192.168.1.1 \
      ipv4.dns 192.168.1.100 \
      ipv4.method manual \
      autoconnect yes
    
    # 설정 적용 — 수정만으로는 적용 안 됨!
    nmcli con up enp1s0
    
    # ==============================
    # 3. 호스트네임 설정
    # ==============================
    
    # hostnamectl = hostname control
    # set-hostname = 영구 설정 (reboot 후에도 유지)
    hostnamectl set-hostname servera.lab.example.com
    ```
    
    안 하면 어떻게 되나: `ipv4.method manual` 빼먹으면 재부팅 시 DHCP가 다른 IP를 받아옴 → 채점 스크립트가 접속 못함 → 0점 위험.
    
    ```bash
    # ==============================
    # 4. 검증
    # ==============================
    
    hostname
    # servera.lab.example.com
    
    ip addr show enp1s0
    # inet 192.168.1.150/24 확인
    
    nmcli con show enp1s0 | grep ipv4
    # ipv4.method: manual
    # ipv4.addresses: 192.168.1.150/24
    # ipv4.gateway: 192.168.1.1
    # ipv4.dns: 192.168.1.100
    
    ping -c 2 192.168.1.1
    # gateway 응답 확인
    ```
    

---

### Q2. 🔴 Configure YUM Repositories

Configure YUM repositories on Node1 using the following URLs:

- **BaseOS:** `http://repo.lab.example.com/rocky9.5/repo/BaseOS`
- **AppStream:** `http://repo.lab.example.com/rocky9.5/repo/AppStream`

> This task must be completed on **both** Node1 and Node2.
> 

> 📝 **변형 포인트**
- URL만 바뀜 — 파일 구조/문법은 동일
- `gpgcheck=0`이 안전 (시험 환경에서 GPG 키 없는 경우 많음)
- 파일 하나에 두 repo 넣어도 되고, 파일 2개 만들어도 됨
> 
- 💡 풀이 가이드 (클릭하여 열기)
    
    repo 파일은 `/etc/yum.repos.d/` 디렉터리에 `.repo` 확장자로 만들어야 해. 파일명은 자유지만 확장자가 `.repo`가 아니면 인식 안 됨.
    
    ```bash
    # ==============================
    # repo 파일 생성
    # ==============================
    
    vi /etc/yum.repos.d/local.repo
    ```
    
    ```
    # --- vi 에디터 내부 작성 내용 ---
    
    [BaseOS]
    name=BaseOS
    baseurl=http://repo.lab.example.com/rocky9.5/repo/BaseOS
    enabled=1
    gpgcheck=0
    
    [AppStream]
    name=AppStream
    baseurl=http://repo.lab.example.com/rocky9.5/repo/AppStream
    enabled=1
    gpgcheck=0
    
    # (저장 후 종료 :wq)
    ```
    
    ```bash
    # ==============================
    # 검증
    # ==============================
    
    # repo 목록 확인 — BaseOS, AppStream 둘 다 보여야 함
    dnf repolist
    # repo id     repo name
    # AppStream    AppStream
    # BaseOS       BaseOS
    
    # 실제 패키지 설치 테스트
    dnf install -y tree
    ```
    

---

### Q3. 🔴 Troubleshoot SELinux and Web Server on Port 82

A web server running on non-standard port **82** is experiencing issues serving content. Debug and fix the issue according to these requirements:

- The web server must serve all existing HTML files from `/var/www/html`. Do not delete or modify existing content.
- The web server must serve content on **port 82**.
- The web server must **start automatically** on system reboot.

> 📝 **변형 포인트**
- 포트 번호만 바뀔 수 있음 (82, 8080, 8443 등)
- 해결해야 할 3가지는 항상 동일: SELinux 포트 + 방화벽 + 서비스 enable
- `semanage` 명령어가 없으면 패키지 설치 필요 (`policycoreutils-python-utils`)
> 
- 💡 풀이 가이드 (클릭하여 열기)
    
    이 문제는 “왜 안 되는지” 디버깅하는 거야. httpd는 이미 설치돼 있고 포트 82로 설정돼 있는데 시작이 안 됨. 원인은 3가지: SELinux가 포트 82를 차단, 방화벽이 포트 82를 차단, 서비스가 enable 안 됨.
    
    ```bash
    # ==============================
    # 1. 현재 상태 확인
    # ==============================
    
    # httpd 설정에서 포트 확인
    grep Listen /etc/httpd/conf/httpd.conf
    # Listen 82 ← 이미 82로 설정돼 있을 것
    
    # httpd 시작 시도 — SELinux 때문에 실패할 거야
    systemctl start httpd
    # 에러 발생 시 journalctl -xe로 확인
    
    # ==============================
    # 2. SELinux 포트 레이블 추가
    # ==============================
    
    # semanage가 없으면 설치
    # semanage = SELinux manage
    # policycoreutils-python-utils 패키지에 포함
    dnf install -y policycoreutils-python-utils
    
    # semanage port = SELinux 포트 레이블 관리
    # -a = add (추가)
    # -t = type (http_port_t = httpd 허용 포트 타입)
    # -p = protocol
    semanage port -a -t http_port_t -p tcp 82
    
    # 만약 "already defined" 에러 나면 -m (modify) 사용:
    # semanage port -m -t http_port_t -p tcp 82
    
    # ==============================
    # 3. 방화벽에 포트 추가
    # ==============================
    
    # --permanent = 영구 설정 (reboot 후에도 유지)
    # --add-port = 포트 열기
    firewall-cmd --permanent --add-port=82/tcp
    firewall-cmd --reload
    
    # ==============================
    # 4. 서비스 시작 + 자동시작
    # ==============================
    
    # enable = 부팅 시 자동시작, --now = 지금 즉시 시작
    systemctl enable --now httpd
    ```
    
    안 하면 어떻게 되나: SELinux 포트 안 열면 httpd 시작 자체가 안 됨. 방화벽 안 열면 외부에서 접속 불가. enable 안 하면 재부팅 후 꺼져 있음.
    
    ```bash
    # ==============================
    # 5. 검증
    # ==============================
    
    systemctl is-enabled httpd
    # enabled
    
    systemctl is-active httpd
    # active
    
    curl http://localhost:82
    # HTML 내용 출력되면 성공
    
    # SELinux 포트 확인
    semanage port -l | grep 82
    # http_port_t   tcp   82, 80, ...
    
    # 방화벽 확인
    firewall-cmd --list-ports
    # 82/tcp
    ```
    

---

### Q4. 🔴 Create User Accounts and Configure Privileges

Create the following users, groups, and configure access as instructed:

- A group named `devops`.
- A user `harry` who belongs to `devops` as a secondary group.
- A user `natasha` who belongs to `devops` as a secondary group.
- A user `sarah` who does not have access to an interactive shell and is not a member of `devops`.
- A user `billy` with UID `3122`.
- All users must have the password `trootent`.
- Members of `devops` group can run all commands via sudo without a password.
- User `harry` can run `/usr/bin/passwd` via sudo without a password.
- The maximum password age for all **newly created users** on this system must be **30 days**.

> 📝 **변형 포인트**
- 그룹명, 사용자명, UID 숫자, 패스워드 → 값만 바뀜
- sudo: “그룹 전체 ALL NOPASSWD” vs “특정 사용자 + 특정 명령어만” — `%그룹` vs `사용자`
- 패스워드 에이징: “모든 새 사용자” → `/etc/login.defs` / “특정 사용자” → `chage -M`
- nologin: `-s /sbin/nologin` (시험 표준)
> 
- 💡 풀이 가이드 (클릭하여 열기)
    
    4단계로 나뉨: 그룹 생성 → 사용자 생성 → sudo 설정 → 패스워드 에이징. sudo는 `/etc/sudoers.d/` 디렉터리에 파일을 만드는 게 안전해 — `/etc/sudoers` 직접 수정하면 문법 실수 시 sudo 전체가 먹통.
    
    ```bash
    # ==============================
    # 1. 그룹 + 사용자 생성
    # ==============================
    
    groupadd devops
    
    # -G = supplementary Group (보조 그룹)
    # 대문자 G = 보조그룹, 소문자 g = 주 그룹(GID)
    useradd -G devops harry
    useradd -G devops natasha
    
    # -s = shell 지정
    # /sbin/nologin = 로그인 시 "This account is
    # currently not available" 출력하고 차단
    useradd -s /sbin/nologin sarah
    
    # -u = UID 직접 지정
    useradd -u 3122 billy
    
    # ==============================
    # 2. 패스워드 설정
    # ==============================
    
    # --stdin = 파이프로 패스워드 입력
    echo "trootent" | passwd --stdin harry
    echo "trootent" | passwd --stdin natasha
    echo "trootent" | passwd --stdin sarah
    echo "trootent" | passwd --stdin billy
    
    # ==============================
    # 3. sudo 설정
    # ==============================
    
    # %devops = devops 그룹 전체 (% = 그룹 표시)
    # ALL=(ALL) = 모든 호스트에서, 모든 사용자로
    # NOPASSWD: ALL = 비밀번호 없이 모든 명령
    echo "%devops ALL=(ALL) NOPASSWD: ALL" \
      > /etc/sudoers.d/devops
    
    # harry에게 passwd 명령만 NOPASSWD
    # 반드시 전체 경로! /usr/bin/passwd
    echo "harry ALL=(ALL) NOPASSWD: /usr/bin/passwd" \
      > /etc/sudoers.d/harry
    ```
    
    패스워드 에이징 — “모든 새 사용자” = login.defs (시스템 기본값), “특정 사용자” = chage. 이 문제는 “newly created users”니까 login.defs.
    
    ```bash
    # ==============================
    # 4. 패스워드 에이징 — 전체 새 사용자
    # ==============================
    
    # /etc/login.defs = 앞으로 만들 사용자의 기본값
    # 이미 존재하는 사용자에게는 적용 안 됨!
    vi /etc/login.defs
    # PASS_MAX_DAYS 30 ← 이 줄 찾아서 수정
    
    # ==============================
    # 5. 검증
    # ==============================
    
    id harry
    # uid=...(harry) gid=...(harry) groups=...(devops)
    
    id sarah
    # uid=...(sarah) gid=...(sarah) groups=...(sarah)
    
    grep sarah /etc/passwd
    # sarah:x:...::/home/sarah:/sbin/nologin
    
    id billy
    # uid=3122(billy)
    
    sudo -l -U harry
    # (ALL) NOPASSWD: ALL        ← devops 그룹
    # (ALL) NOPASSWD: /usr/bin/passwd ← harry 개별
    
    grep PASS_MAX_DAYS /etc/login.defs
    # PASS_MAX_DAYS   30
    ```
    

---

### Q5. 🟡 Configure Cron Jobs and Deny Crontab Access

**Part A:** Configure a cron job with the following specifications:
- **User:** `harry`
- **Schedule:** Every day at **12:30**
- **Command:** Print `"EX200"` using the `echo` command.

**Part B:** Deny the user `natasha` from using crontab.

> 📝 **변형 포인트**
- 시간 표현: `30 12 * * *`, `*/5 * * * *`, `32 14 * * *` 등 — 파트 A 참고
- 명령어: `echo`, `logger`, `uptime >> logfile` 등
- crontab deny는 `/etc/cron.deny`에 사용자 추가
> 

💡 풀이 가이드 (클릭하여 열기)

cron은 예약 작업 도구야. crontab deny는 특정 사용자의 crontab 사용을 차단하는 기능.

```bash
# ==============================
# Part A: 크론 작업 설정
# ==============================

# crontab = cron table (예약 작업 테이블)
# -e = edit (편집 모드)
# -u = user (다른 사용자의 crontab 편집 — root만 가능)
crontab -u harry -e

# --- vi 에디터 안에서 작성 ---
# 형식: 분 시 일 월 요일 명령어
# 30 = 30분, 12 = 12시
# * * * = 매일, 매월, 매요일
30 12 * * * /bin/echo "EX200"
# (저장 후 종료 :wq)

# ==============================
# Part B: crontab 사용 차단
# ==============================

# /etc/cron.deny = 이 파일에 적힌 사용자는
# crontab 명령 사용 불가
# RHEL 9에서 기본적으로 존재 (빈 파일)
echo "natasha" >> /etc/cron.deny

# ==============================
# 검증
# ==============================

# harry의 crontab 확인
# -l = list (목록 보기)
crontab -u harry -l
# 30 12 * * * /bin/echo "EX200"

# natasha의 crontab 차단 확인
su - natasha -c "crontab -l"
# You (natasha) are not allowed to use this program
```

---

### Q6. 🔴 Create a Collaborative Directory with SGID and ACL

Create a shared collaboration directory with the following requirements:

- **Directory:** `/common/devops`
- **Group ownership:** `devops`
- **Permissions:**
    - `devops` group members: full read/write/execute permissions
    - Others: **no permissions at all**
- Files created within this directory must **automatically inherit the `devops` group ownership**.
- User `sarah` must have **read-only** access to this directory using ACL.

> 📝 **변형 포인트**
- 순수 SGID만 (ACL 없이) 나올 수도 있음 — `chmod 2770`이면 끝
- ACL이 결합되면: SGID 먼저 → 그 위에 ACL 추가
- Sticky bit(`1777`)와 혼동 주의 — SGID는 “그룹 상속”, sticky는 “소유자만 삭제”
> 
> - 💡 풀이 가이드 (클릭하여 열기)
>     
>     SGID(Set Group ID)는 디렉터리에 걸면 “이 안에서 만든 파일은 자동으로 디렉터리의 그룹을 물려받는다”는 뜻이야. ACL은 표준 권한(ugo/rwx) 바깥에서 특정 사용자에게 별도 권한을 주는 기능.
>     
>     ```bash
>     # ==============================
>     # 1. 디렉터리 생성 + 그룹 소유권
>     # ==============================
>     
>     # -p = parents (상위 디렉터리도 같이 생성)
>     mkdir -p /common/devops
>     
>     # chgrp = change group (그룹 소유자 변경)
>     chgrp devops /common/devops
>     
>     # ==============================
>     # 2. SGID + 권한 설정
>     # ==============================
>     
>     # 2 = SGID bit
>     # 7 = rwx (소유자)
>     # 7 = rwx (그룹 — devops)
>     # 0 = --- (타인 — 접근 불가)
>     chmod 2770 /common/devops
>     
>     # ==============================
>     # 3. ACL로 sarah에게 읽기 전용
>     # ==============================
>     
>     # setfacl = set file access control list
>     # -m = modify (수정/추가)
>     # u:sarah:rx = user sarah에게 read+execute
>     # (디렉터리는 x 없으면 cd로 진입 불가이므로
>     #  읽기 접근하려면 r+x 필요)
>     setfacl -m u:sarah:rx /common/devops
>     
>     # ==============================
>     # 4. 검증
>     # ==============================
>     
>     ls -ld /common/devops
>     # drwxrws---+ 2 root devops ...
>     # s = SGID 활성, + = ACL 존재
>     
>     getfacl /common/devops
>     # user::rwx
>     # user:sarah:r-x
>     # group::rwx
>     # mask::rwx
>     # other::---
>     
>     # 기능 테스트 — devops 멤버가 파일 만들면
>     # 그룹이 자동으로 devops
>     su - harry -c "touch /common/devops/testfile"
>     ls -l /common/devops/testfile
>     # -rw-rw----. 1 harry devops ...  ← 그룹 상속 확인
>     ```
>     

---

### Q7. 🔴 Configure File ACLs

Copy the file `/etc/fstab` to `/var/tmp/fstab`. Configure the permissions of `/var/tmp/fstab` so that:

- The file `/var/tmp/fstab` is owned by the root user.
- The file `/var/tmp/fstab` belongs to the group root.
- The file is **not executable** by anyone.
- The user `natasha` is able to **read and write** `/var/tmp/fstab`.
- The user `harry` can **neither read nor write** `/var/tmp/fstab`.
- All other users (current or future) have the ability to **read** `/var/tmp/fstab`.

> 📝 **변형 포인트**
- 누구한테 rw 주고 누구를 차단하는지 이름만 바뀜
- “not executable by anyone” = 기본 cp 결과가 이미 실행권한 없음. 확인만 하면 됨
- `setfacl -m u:사용자:권한` 구조는 동일
> 
- 💡 풀이 가이드 (클릭하여 열기)
    
    ACL(Access Control List)은 기존 소유자/그룹/기타(ugo) 바깥에서 특정 사용자에게 개별 권한을 설정하는 기능이야.
    
    ```bash
    # ==============================
    # 1. 파일 복사
    # ==============================
    
    cp /etc/fstab /var/tmp/fstab
    
    # 소유권 확인 — cp하면 root:root로 복사됨 (OK)
    ls -l /var/tmp/fstab
    # -rw-r--r--. 1 root root ...
    # → 실행 권한(x) 이미 없음 ✅
    
    # ==============================
    # 2. ACL 설정
    # ==============================
    
    # setfacl = set file access control list
    # -m = modify (수정/추가)
    # u:natasha:rw = natasha에게 읽기+쓰기
    setfacl -m u:natasha:rw /var/tmp/fstab
    
    # u:harry:--- = harry에게 아무 권한 없음
    setfacl -m u:harry:--- /var/tmp/fstab
    
    # o::r = other(기타 사용자)에게 읽기만
    # :: 사이가 비어있으면 "기타 전체"
    setfacl -m o::r /var/tmp/fstab
    
    # ==============================
    # 3. 검증
    # ==============================
    
    getfacl /var/tmp/fstab
    # user::rw-
    # user:natasha:rw-
    # user:harry:---
    # group::r--
    # mask::rw-
    # other::r--
    
    # ls -l에서 끝에 + 보이면 ACL 적용됨
    ls -l /var/tmp/fstab
    # -rw-rw-r--+ 1 root root ...
    ```
    

---

### Q8. 🟡 Configure NTP Time Synchronization

Configure your system to synchronize time from `ntp.lab.example.com`.

> 📝 **변형 포인트**
- NTP 서버 주소만 바뀜 — 설정 방법 동일
- chrony.conf에서 기존 server/pool 줄 주석 처리 후 새 서버 추가
> 
- 💡 풀이 가이드 (클릭하여 열기)
    
    RHEL 9에서 NTP 클라이언트는 chronyd 서비스야. chrony.conf를 수정해서 동기화 서버를 지정하면 돼.
    
    ```bash
    # ==============================
    # 1. chrony.conf 수정
    # ==============================
    
    vi /etc/chrony.conf
    
    # --- 기존 pool/server 줄 앞에 # 붙여서 주석 처리 ---
    # pool 2.rocky.pool.ntp.org iburst  ← 주석 처리
    
    # --- 새 서버 추가 ---
    # iburst = 초기 동기화를 빠르게 (4번 연속 요청)
    server time.google.com iburst
    
    # (저장 후 종료 :wq)
    
    # ==============================
    # 2. 서비스 재시작 + 활성화
    # =============================="https://raw.githubusercontent.com/linuxacademy/content-cks-resources/main/S06L0506%20Static%20Analysis/Dockerfile"
    
    systemctl restart chronyd
    systemctl enable chronyd
    
    # ==============================
    # 3. 검증
    # ==============================
    
    # chronyc sources = 동기화 소스 확인
    # ^* = 현재 동기화 중인 서버
    chronyc sources
    # ^* ntp.lab.example.com ...
    
    timedatectl
    # NTP service: active 확인
    ```
    

---

### Q9. 🟡 Configure Autofs for Remote User Home Directory

Configure autofs to automount the home directory of `remoteuser5`. Note the following:

- **NFS Server:** `nfs.lab.example.com`
- **Shared directory:** `/export/home/remoteuser5`
- **Local mount point:** `/rhome/remoteuser5`
- Home directory must be **writable** by the user.
- The autofs service must start automatically on boot.

> 📝 **변형 포인트**
- NFS 서버 주소, 사용자명, 경로만 바뀜
- 와일드카드(`*`) 방식 vs 특정 사용자 지정 방식
- mount 옵션: `-rw` (읽기/쓰기) vs `-ro` (읽기 전용)
- 마운트 해제 타이머: 기본 300초, 문제에서 별도 지정 시 `--timeout=초`
> 
- 💡 풀이 가이드 (클릭하여 열기)
    
    autofs는 “사용자가 디렉터리에 접근할 때 자동으로 NFS 마운트”하는 서비스야. 설정 파일이 2개 필요: master 파일(어디에 마운트할지)과 map 파일(어떤 서버의 어떤 경로를).
    
    ```bash
    # ==============================
    # 1. autofs 설치 + 서비스 준비
    # ==============================
    
    dnf install -y autofs
    
    # ==============================
    # 2. master 파일 생성
    # ==============================
    
    # /etc/auto.master.d/ 디렉터리에 .autofs 확장자로
    # master 파일을 만들면 auto.master를 직접 수정
    # 안 해도 됨 (더 안전)
    vi /etc/auto.master.d/rhome.autofs
    
    # --- 내용 ---
    # /rhome = 이 디렉터리 아래에 마운트
    # /etc/auto.rhome = 세부 설정 파일 경로
    /rhome /etc/auto.rhome
    
    # (저장 후 종료 :wq)
    
    # ==============================
    # 3. map 파일 생성
    # ==============================
    
    vi /etc/auto.rhome
    
    # --- 내용 ---
    # remoteuser5 = 서브디렉터리 이름
    # -rw = 읽기/쓰기 마운트
    # nfs.lab.example.com:/export/home/remoteuser5
    #   = NFS 서버:공유 경로
    remoteuser5 -rw nfs.lab.example.com:/export/home/remoteuser5
    
    # (저장 후 종료 :wq)
    
    # ==============================
    # 4. 서비스 시작 + 활성화
    # ==============================
    
    systemctl enable --now autofs
    
    # ==============================
    # 5. 검증
    # ==============================
    
    # /rhome 디렉터리가 자동 생성되었는지
    ls /rhome/
    
    # 접근하면 자동 마운트
    cd /rhome/remoteuser5
    # NFS 서버가 실제로 있으면 마운트 성공
    
    # 마운트 확인
    mount | grep rhome
    ```
    

---

### Q10. 🔴 Find Files by User and Copy

Search and copy files according to the following requirements:

- Find all files owned by user `natasha` across the entire system.
- Copy all found items to the `/root/natashafiles` directory.

> 📝 **변형 포인트**
- 사용자 이름만 바뀜 — find 옵션 동일
- “files and directories” vs “files only” — files only면 `-type f` 추가
- 대상 디렉터리를 먼저 만들어야 함
> 
- 💡 풀이 가이드 (클릭하여 열기)
    
    find로 시스템 전체를 검색해서 특정 사용자 소유 파일을 복사하는 문제. RHCSA 단골.
    
    ```bash
    # ==============================
    # 1. 대상 디렉터리 생성
    # ==============================
    
    mkdir -p /root/natashafiles
    
    # ==============================
    # 2. find + copy
    # ==============================
    
    # find / = 루트부터 전체 검색
    # -user natasha = natasha 소유 파일
    # -exec cp -a {} /dest/ \; = 찾은 파일마다 cp 실행
    #   -a = archive (속성 보존 + 재귀 복사)
    #   {} = find가 찾은 파일 경로로 치환
    #   \; = -exec 종료 표시
    # 2>/dev/null = /proc, /sys 등의 권한 에러 숨기기
    find / -user natasha \
      -exec cp -a {} /root/natashafiles/ \; \
      2>/dev/null
    
    # ==============================
    # 3. 검증
    # ==============================
    
    ls -la /root/natashafiles/
    # natasha 소유 파일들이 복사됨
    ```
    

---

### Q11. 🟡 Search for a String

- Search for all lines containing the string `"ich"` in the file `/usr/share/dict/words`.
- Save the results to `/root/lines`.

> 📝 **변형 포인트**
- 검색 문자열, 소스 파일, 출력 파일만 바뀜
- `grep` + `>` (리다이렉션) 조합
> 
- 💡 풀이 가이드 (클릭하여 열기)
    
    ```bash
    # grep = global regular expression print
    # "ich" = 검색할 문자열
    # > = 출력을 파일로 저장 (덮어쓰기)
    grep "ich" /usr/share/dict/words > /root/lines
    
    # 검증
    cat /root/lines | head -5
    wc -l /root/lines
    # 줄 수가 0이 아니면 OK
    ```
    

---

### Q12. 🟡 Create a Compressed Archive

Create a compressed archive with the following specifications:

- **Source directory:** `/etc`
- **Compression:** bzip2
- **Output file:** `/root/backup.tar.bz2`

> 📝 **변형 포인트** — ⚠️ 이게 핵심!
- gzip: `-czf`, 확장자 `.tar.gz`
- bzip2: `-cjf`, 확장자 `.tar.bz2`
- 소스 디렉터리, 출력 경로만 바뀜
- 파트 A 표 참고
> 
- 💡 풀이 가이드 (클릭하여 열기)
    
    ```bash
    # tar = tape archive
    # -c = create (생성)
    # -j = bzip2 압축 (gzip이면 -z)
    # -f = file (출력 파일 지정 — 반드시 마지막 옵션!)
    tar -cjf /root/backup.tar.bz2 /etc
    
    # 검증
    # -t = list (내용 목록 확인)
    tar -tjf /root/backup.tar.bz2 | head -5
    # etc/hostname
    # etc/fstab
    # ...
    
    # 파일 존재 확인
    ls -lh /root/backup.tar.bz2
    ```
    

---

### Q13. 🟡 Create a Shell Script

Create an executable script named `/usr/local/bin/findfiles` that performs the following:

- Search the `/usr/share` directory for **regular files** larger than **3 KiB** and smaller than **5 KiB**.
- Copy the found files to the `/root/foundfiles` directory.

> 📝 **변형 포인트**
- 크기 단위: `k` (KiB), `M` (MiB)
- 범위: `+3k -5k` (초과/미만) vs `+4M` (단일 조건)
- 스크립트 경로, 대상 디렉터리만 바뀜
- `-type f` = 파일만, 빼면 디렉터리도 포함
> 
- 💡 풀이 가이드 (클릭하여 열기)
    
    셸 스크립트 안에 find 명령을 넣는 문제. 시험에서 간단한 스크립트가 나올 수 있어.
    
    ```bash
    # ==============================
    # 1. 대상 디렉터리 생성
    # ==============================
    
    mkdir -p /root/foundfiles
    
    # ==============================
    # 2. 스크립트 작성
    # ==============================
    
    vi /usr/local/bin/findfiles
    
    # --- 스크립트 내용 ---
    #!/bin/bash
    # -type f = regular files only (파일만)
    # -size +3k = 3 KiB 초과
    # -size -5k = 5 KiB 미만
    find /usr/share -type f -size +3k -size -5k \
      -exec cp {} /root/foundfiles/ \;
    # (저장 후 종료 :wq)
    
    # ==============================
    # 3. 실행 권한 부여
    # ==============================
    
    # +x = execute 권한 추가
    chmod +x /usr/local/bin/findfiles
    
    # ==============================
    # 4. 실행 + 검증
    # ==============================
    
    findfiles
    
    ls /root/foundfiles/ | head -5
    # 파일들이 복사되었는지 확인
    
    # 복사된 파일 크기 확인
    ls -lh /root/foundfiles/ | head -5
    # 3k~5k 범위인지 확인
    ```
    

---

### Q14. 🟡 Configure SSH Key-Based Authentication

Configure passwordless SSH login from Node1 to Node2 as the `natasha` user.

- Use SSH key pair authentication.
- After configuring, verify by connecting from Node1 to Node2 without entering a password.
- Using the configured key, securely copy the file `/etc/hostname` from Node1 to Node2’s `/tmp/` directory.

> 📝 **변형 포인트**
- 사용자, 호스트만 바뀜
- `ssh-keygen` → `ssh-copy-id` → `ssh` 순서는 고정
- scp는 SSH 키가 설정되면 자동으로 비밀번호 없이 작동
> 
- 💡 풀이 가이드 (클릭하여 열기)
    
    SSH 키 인증은 비밀번호 대신 공개키/개인키 쌍으로 로그인하는 방식이야. Node1에서 키를 생성하고, 공개키를 Node2에 복사하면 됨.
    
    ```bash
    # ==============================
    # 1. natasha로 전환
    # ==============================
    
    su - natasha
    
    # ==============================
    # 2. SSH 키 생성
    # ==============================
    
    # ssh-keygen = SSH key generate
    # 질문 3개 나옴 — 전부 Enter(기본값)
    # 1) 저장 위치: ~/.ssh/id_rsa (기본)
    # 2) 패스프레이즈: 빈 값 (Enter)
    # 3) 확인: 빈 값 (Enter)
    ssh-keygen
    
    # ==============================
    # 3. 공개키를 Node2에 복사
    # ==============================
    
    # ssh-copy-id = 공개키를 원격 서버의
    # ~/.ssh/authorized_keys에 추가
    # 이때 한번만 비밀번호 입력 필요
    ssh-copy-id natasha@serverb.lab.example.com
    
    # ==============================
    # 4. 비밀번호 없이 접속 테스트
    # ==============================
    
    ssh natasha@serverb.lab.example.com
    # 비밀번호 안 물어보면 성공!
    exit
    
    # ==============================
    # 5. scp로 파일 복사
    # ==============================
    
    # scp = secure copy (SSH 기반 파일 전송)
    # 형식: scp 소스 목적지
    scp /etc/hostname \
      natasha@serverb.lab.example.com:/tmp/
    
    # natasha에서 나오기
    exitenable_homedir
    ```
    

---

### Q15. 🟡 Build a Container Image from Containerfile

Perform the following tasks as the `semi` user:

1. Log in to the container registry at `registry.redhat.io`.
    - **Username:** `admin`
    - **Password:** `redhat123`
2. Download the Containerfile from `http://utility.lab.example.com/Containerfile`.
3. Build a container image named `myimage` using the downloaded Containerfile.

> 📝 **변형 포인트**
- registry 주소, 인증 정보만 바뀜
- `--tls-verify=false` — 시험 환경에서 인증서 문제 시 사용
- Containerfile 내용은 수정하지 말라는 조건이 일반적
> 
- 💡 풀이 가이드 (클릭하여 열기)
    
    Podman 빌드는 2단계: registry 로그인 → Containerfile로 이미지 빌드. root에서 사전 준비(사용자 생성)를 먼저 해야 해.
    
    ```bash
    # ==============================
    # root에서 사전 준비
    # ==============================
    
    # 사용자 생성 (이미 있으면 생략)
    useradd semi
    echo "trootent" | passwd --stdin semi
    
    # ==============================
    # semi로 전환 (SSH 권장!)
    # ==============================
    
    ssh semi@localhost
    
    # ==============================
    # 1. registry 로그인
    # ==============================
    
    # --tls-verify=false = 자체서명 인증서 허용
    # 시험 환경에서 인증서 에러 나면 이 옵션 사용
    podman login --tls-verify=false \
      registry.redhat.io
    # Username: admin
    # Password: redhat123
    
    # ==============================
    # 2. Containerfile 다운로드
    # ==============================
    
    # wget이 없으면: curl -O URL
    #-f (--fail): 만약 인터넷 주소가 틀려서 404 Not Found 에러가 났을 때, 쓰레기 데이터(에러 안내 HTML 페이지)를 파일로 저장하지 않고 즉시 다운로드를 실패(중단) 처리합니다.
    
    #-s (--silent): 다운로드 진행률 바(====>)나 자잘한 메시지를 화면에 출력하지 않고 숨김. 
    
    #-S (--show-error): 진짜 에러가 발생했을 때만큼은 숨기지 말고 화면에 에러 원인을 출력.
    
    #-L (--location): 주소가 바뀌었으면 끝까지 추적해서 원본을 찾아오는 옵션.
    #-o (--output): 이름 바꾸기 (Rename)
    #-O (--output): 원본 이름 유지 (Keep Original Name)
    
    curl -fsSLo Containerfile "https://raw.githubusercontent.com/linuxacademy/content-cks-resources/main/S06L0506%20Static%20Analysis/Dockerfile"
    
    # 내용 확인 (수정하지 말 것!)
    cat Containerfile
    
    # ==============================
    # 3. 이미지 빌드
    # ==============================
    
    # podman build = Containerfile로 이미지 생성
    # -t = tag (이미지 이름)
    # -f = file (Containerfile 경로)
    # . = build context (현재 디렉터리)
    podman build -t myimage -f Containerfile .
    
    # ==============================
    # 4. 검증
    # ==============================
    
    podman images
    # REPOSITORY   TAG      IMAGE ID   SIZE
    # localhost/myimage  latest  xxx    xxx
    
    exit
    ```
    

---

### Q16. 🔴 Deploy Rootless Container with Systemd Auto-start

Deploy a rootless container using the official Red Hat httpd image. Configure it according to the following requirements:

• **User:** All tasks must be performed as the `semi` user.

• **Image to pull:** `docker.io/library/nginx:latest`
• **Container name:** `exam-web`
• **Port mapping:** Host port **8080** to container port **80**.
• **Volume mounts:**
    ◦ Host `/opt/exam-data`  → Container `/usr/share/nginx/html`
• **Systemd & Linger:** The container must be managed via `systemd` and must **start automatically** after reboot, even if the `semi` user is not logged in.

> 📝 **변형 포인트**
- 포트 번호, 볼륨 경로, 컨테이너 이름만 바뀜
- 핵심 순서: linger → SSH로 사용자 접속 → 컨테이너 실행 → systemd 생성 → 기존 컨테이너 삭제 → enable
- `:Z` 빼먹으면 SELinux가 볼륨 접근 차단
- `su -`는 cgroup 문제 일으킬 수 있으니 SSH 사용
> 
- 💡 풀이 가이드 (클릭하여 열기)
    
    시험 핵심! Podman + systemd + linger로 재부팅 후에도 컨테이너가 자동으로 뜨게 만드는 거야. 순서가 생명이니까 꼭 기억해.
    
    ```bash
    # ==============================
    # root에서 사전 준비
    # ==============================
    
    # linger 활성화 — 로그아웃 후에도 사용자 서비스 유지
    # linger = "머무르다"
    loginctl enable-linger semi
    
    # 호스트 디렉터리 생성 + 소유권 설정
    mkdir -p /opt/exam-data
    chown semi:semi /opt/exam-data
    echo "Welcome to Semi's Web Server" > /opt/exam-data/index.html
    
    # ==============================
    # semi로 전환 (SSH 권장!)
    # ==============================
    
    # su -는 cgroup이 root 세션에 남아서
    # systemd 서비스 꼬일 수 있음
    ssh semi@localhost
    
    # ==============================
    # 1. 컨테이너 실행
    # ==============================
    
    # -d = detach (백그라운드 실행)
    # --name = 컨테이너 이름
    # -p = port mapping (호스트:컨테이너)
    # -v = volume mount (호스트:컨테이너:SELinux)
    # :Z = SELinux 컨텍스트 자동 조정 (★ 필수)
    podman run -d --name exam-web \
      -p 8080:80 \
      -v /opt/exam-data:/usr/share/nginx/html:Z \
      docker.io/library/nginx:latest
    
    # 실행 확인
    podman ps
    # PORTS 열에 0.0.0.0:8080->80/tcp 있어야 함
    
    # ==============================
    # 2. systemd 유닛 파일 생성
    # ==============================
    
    mkdir -p ~/.config/systemd/user
    cd ~/.config/systemd/user
    
    # generate systemd = 실행 중인 컨테이너 설정을
    #                     systemd 서비스 파일로 변환
    # --name = 컨테이너 이름 기준
    # --files = 파일로 저장
    # --new = 서비스 시작 시 컨테이너 새로 생성
    podman generate systemd \
      --name exam-web --files --new
    
    # ==============================
    # 3. 기존 컨테이너 삭제 (필수!)
    # ==============================
    
    # systemd가 --new로 새로 만들어야 하므로
    # 수동으로 띄운 건 제거해야 충돌 안 남
    podman rm -f exam-web
    
    # ==============================
    # 4. 서비스 등록 + 시작
    # ==============================
    
    # --user = 일반 사용자의 systemd
    systemctl --user daemon-reload
    systemctl --user enable --now container-exam-web.service
    
    # 상태 확인
    systemctl --user status container-exam-web.service
    podman ps
    ```
    
    안 하면 어떻게 되나: linger 없으면 사용자 로그아웃 시 컨테이너 종료. `--new` 없으면 컨테이너 이름 충돌. `rm -f` 안 하면 systemd와 수동 컨테이너 충돌.
    
    ```bash
    # ==============================
    # 5. 검증 — 최종
    # ==============================
    
    # semi에서 나가기
    exit
    
    # reboot으로 진짜 자동시작 확인
    reboot
    
    #root에서 확인
    loginctl user-status semi
    
    # reboot 후 root로 로그인
    # semi 로그인 안 해도 컨테이너 떠있어야 함!
    podman ps  # root에서는 안 보임 — 정상
                # rootless 컨테이너라서
    
    # semi의 컨테이너 확인하려면
    ssh semi@localhost
    podman ps
    # ascii2pdf 떠있으면 성공!
    
    curl http://localhost:8080
    # 응답 오면 포트 매핑도 OK
    ```
    

---

## 📌 Node 2 Tasks

---

### Q17. 🔴🔵 Reset Root Password and Set Boot Target

You do not know the root password for the Node2 server.

- Reset the root password to `redhat`.
- After gaining access, configure the system to boot into **multi-user.target** by default.

> 📝 **변형 포인트**
- 패스워드만 바뀜. rd.break 절차는 동일
- `touch /.autorelabel` 빼먹으면 SELinux 레이블 불일치로 로그인 불가
- 부트 타겟: `multi-user.target` vs `graphical.target`
> 
- 💡 풀이 가이드 (클릭하여 열기)
    
    root 패스워드 리셋은 GRUB 부트 프로세스를 인터럽트해서 하는 거야. 이게 안 되면 Node2 문제를 하나도 못 풀어.
    
    ```
    시험장에서의 화면 흐름:
    
    1. VM을 재부팅 (또는 시작)
    2. GRUB 부트 메뉴가 뜨면 아무 키나 눌러서 카운트다운 멈춤
    3. 기본 부팅 항목 선택된 상태에서 'e' 키 (edit)
    4. linux 줄 (vmlinuz가 있는 줄) 찾기
    5. 그 줄에서 ro를 찾아 rw init=/bin/bash 로 변경
       (ro 를 지우고 rw init=/bin/bash 를 적는 것)
    6. Ctrl+X 로 부팅
    ```
    
    ```bash
    # ==============================
    # 1. rw init=/bin/bash 로 부팅 후
    # ==============================
    
    # rw로 부팅했으므로 / 가 이미 읽기/쓰기 마운트됨
    # mount remount나 chroot 불필요!
    
    # ==============================
    # 2. 패스워드 변경
    # ==============================
    
    passwd root
    # 새 비밀번호 입력: redhat
    
    # ==============================
    # 3. SELinux 레이블 재설정 (필수!)
    # ==============================
    
    touch /.autorelabel
    
    # ==============================
    # 4. 재부팅
    # ==============================
    exec /sbin/init
    ```
    
    재부팅 후 autorelabel이 실행돼서 시간이 좀 걸릴 수 있어 (1~3분). 기다려.
    
    ```bash
    # ==============================
    # 5. 새 비밀번호로 로그인 후 — 부트 타겟
    # ==============================
    
    # systemctl set-default = 기본 부팅 타겟 설정
    # multi-user.target = CLI 모드 (GUI 없음)
    # graphical.target = GUI 모드
    systemctl set-default multi-user.target
    
    # 검증
    systemctl get-default
    # multi-user.target
    ```
    

---

### Q18. 🔴 Configure YUM Repositories (Node2)

Configure YUM repositories on Node2 using the following URLs:

- **BaseOS:** `http://repo.lab.example.com/rocky9.5/repo/BaseOS`
- **AppStream:** `http://repo.lab.example.com/rocky9.5/repo/AppStream`

> Q2와 동일한 방법. Node2에서도 반드시 수행.
> 
- 💡 풀이 가이드 (클릭하여 열기)
    
    Q2와 동일. repo 파일 생성 → dnf repolist로 확인.
    
    ```bash
    vi /etc/yum.repos.d/local.repo
    ```
    
    ```
    [BaseOS]
    name=BaseOS
    baseurl=http://repo.lab.example.com/rocky9.5/repo/BaseOS
    enabled=1
    gpgcheck=0
    
    [AppStream]
    name=AppStream
    baseurl=http://repo.lab.example.com/rocky9.5/repo/AppStream
    enabled=1
    gpgcheck=0
    ```
    
    ```bash
    # 검증
    dnf repolist
    ```
    

---

### Q19. 🔴 Create LVM with Specific PE Size and Extents

Create an LVM configuration according to the following specifications:

- Create a partition on `/dev/vdb` with type **Linux LVM**.
- **Volume Group:** `datastore` with PE size **8 MiB**
- **Logical Volume:** `database` with size **50 extents**
- **Filesystem:** ext3
- **Mount point:** `/mnt/database`
- The mount must persist after reboot.

> 📝 **변형 포인트** — ⚠️ 핵심 변형 많음!
- PE 크기: `8M`, `16M` — `vgcreate -s` 옵션
- LV 크기: extent 개수(`-l 50`) vs 고정 크기(`-L 500M`) — 파트 A 참고
- 파일시스템: ext3/ext4/xfs — `mkfs.xxx` 명령어 + fstab 타입
- 최종 크기 = PE 크기 × extent 수 (예: 8M × 50 = 400M)
> 
- 💡 풀이 가이드 (클릭하여 열기)
    
    LVM은 파티션 → PV → VG → LV → 포맷 → 마운트 → fstab 순서야. PE 크기와 extent 개수로 만드는 건 RHCSA 단골.
    
    ```bash
    # ==============================
    # 1. 디스크 상태 확인
    # ==============================
    
    # lsblk = list block devices
    lsblk -p
    # /dev/vdb에 빈 공간 확인
    
    # ==============================
    # 2. 파티션 생성
    # ==============================
    
    fdisk /dev/vdb
    
    # --- fdisk 대화형 모드 ---
    # n     : 새 파티션 (New)
    # p     : Primary
    # Enter : 기본 파티션 번호
    # Enter : 기본 시작 섹터
    # +2G   : 크기 (PE 8M × 50 extent = 400M이지만
    #          VG 메타데이터 + 여유분 고려해서 넉넉히)
    # t     : 타입 변경 (Type)
    # lvm   : Linux LVM 타입 (GPT에서는 alias 사용)
    # w     : 저장 (Write)
    
    # 커널에 파티션 테이블 알리기
    partprobe /dev/vdb
    
    # ==============================
    # 3. LVM 파이프라인
    # ==============================
    
    # pvcreate = Physical Volume 생성
    pvcreate /dev/vdb1
    
    # vgcreate = Volume Group 생성
    # -s 8M = PE(Physical Extent) 크기 8MiB
    vgcreate -s 8M datastore /dev/vdb1
    
    # lvcreate = Logical Volume 생성
    # -n = name (LV 이름)
    # -l = logical extents 개수 (소문자 L!)
    #      50 extents × 8M PE = 400M
    lvcreate -n database -l 50 datastore
    
    # ==============================
    # 4. 파일시스템 생성
    # ==============================
    
    mkfs.ext3 /dev/datastore/database
    
    # ==============================
    # 5. 마운트 + fstab 등록
    # ==============================
    
    mkdir -p /mnt/database
    
    # UUID 확인
    blkid /dev/datastore/database
    # UUID="xxxx-xxxx-..." TYPE="ext3" 확인
    
    # fstab에 추가
    vi /etc/fstab
    # UUID=xxxx-xxxx /mnt/database ext3 defaults 0 0
    
    # ★ mount -a로 반드시 테스트!
    # 에러 나면 fstab 즉시 수정 — 안 하면 부팅 불가!
    mount -a
    ```
    
    안 하면 어떻게 되나: fstab에 오타 있는 채로 reboot하면 부팅 실패 → 전체 0점.
    
    ```bash
    # ==============================
    # 6. 검증
    # ==============================
    
    # df = disk free, -h = human-readable, -T = type
    df -hT /mnt/database
    # Filesystem                  Type  Size  ...
    # /dev/mapper/datastore-database ext3 400M  ...
    
    lvs
    # LV       VG        LSize
    # database datastore 400.00m
    
    vgs
    # VG        #PV #LV PE Size
    # datastore  1   1  8.00m
    ```
    

---

### Q20. 🟡 Create a Swap Partition

Create a swap partition with the following requirements:

- **Size:** 512 MiB
- Do **not** modify or remove existing swap partitions.
- The swap must be automatically activated after reboot.

> 📝 **변형 포인트**
- 크기만 바뀜 (400M, 512M 등) — 명령어 동일
- “do not modify current swap” = 기존 fstab 건드리지 말 것
> 
- 💡 풀이 가이드 (클릭하여 열기)
    
    ```bash
    # ==============================
    # 1. 파티션 생성
    # ==============================
    
    fdisk /dev/vdb
    
    # --- fdisk 대화형 모드 ---
    # n     : 새 파티션
    # p     : Primary
    # Enter : 기본 번호
    # Enter : 기본 시작 섹터
    # +512M : 크기
    # t     : 타입 변경
    # (파티션 번호 선택)
    # swap  : Linux swap 타입 (GPT alias)
    # w     : 저장
    
    partprobe /dev/vdb
    
    # ==============================
    # 2. 스왑 초기화 + 활성화
    # ==============================
    
    # mkswap = 파티션을 swap 공간으로 포맷
    mkswap /dev/vdb2
    
    # swapon = swap 즉시 활성화
    swapon /dev/vdb2
    
    # ==============================
    # 3. fstab 등록 (영구 설정)
    # ==============================
    
    blkid /dev/vdb2
    # UUID 복사
    
    vi /etc/fstab
    # UUID=xxxx swap swap defaults 0 0
    
    # 문법 확인
    mount -a
    # (swap은 mount -a로 마운트 안 되지만
    #  fstab 문법 에러는 잡아줌)
    
    # ==============================
    # 4. 검증
    # ==============================
    
    swapon --show
    # NAME       TYPE       SIZE
    # /dev/vdb2  partition  512M  ← 추가된 swap
    # (기존 swap도 그대로 있어야 함!)
    ```
    

---

### Q21. 🔴 Resize a Logical Volume

Resize an existing logical volume according to the following specifications:

- **Target:** `/dev/datastore/vo`
- **Goal:** Resize to approximately **300 MiB** (acceptable range: 270-330 MiB)
- The logical volume is already mounted at `/mnt/vo`. The mount must persist after resize.
    
    > 📝 **변형 포인트**
    - 절대 크기(`-L 300M`) vs 상대 추가(`-l +100` extents) — 파트 A 참고
    - `-r` 옵션이면 xfs/ext4 구분 없이 자동 확장
    - “acceptable range”가 있으면 정확한 값 아니어도 OK
    > 
- 💡 풀이 가이드 (클릭하여 열기)
    
    LV 확장은 `lvextend -r` 한 줄이면 끝이야. `-r`이 파일시스템도 같이 늘려줌.
    
    ```bash
    # ==============================
    # 1. 현재 크기 확인
    # ==============================
    
    lvs /dev/datastore/vo
    # LSize 확인
    
    df -hT /mnt/vo
    # 파일시스템 타입 + 현재 크기 확인
    
    # ==============================
    # 2. LV 확장
    # ==============================
    
    # lvextend = logical volume extend (확장)
    # -r = --resizefs (파일시스템도 같이 확장)
    #   xfs면 xfs_growfs, ext면 resize2fs 자동 호출
    # -L 300M = 최종 크기 300MiB로
    lvextend -r -L 300M /dev/datastore/vo
    
    # ==============================
    # 3. 검증
    # ==============================
    
    lvs /dev/datastore/vo
    # LSize  300.00m
    
    df -hT /mnt/vo
    # 크기가 ~300M으로 늘어났는지 확인
    ```
    

---

### Q22. 🟢 Configure Tuned Profile

Set the **recommended** Tuned profile for your system.

> 📝 **변형 포인트**
- 거의 항상 “recommended” — 명령어 2줄 고정
- VM이면 보통 `virtual-guest`가 recommend 결과
> 
- 💡 풀이 가이드 (클릭하여 열기)
    
    ```bash
    # tuned-adm = tuned administration
    # recommend = 시스템에 맞는 프로파일 추천
    tuned-adm recommend
    # virtual-guest (VM인 경우)
    
    # profile = 프로파일 적용
    tuned-adm profile virtual-guest
    
    # 검증
    tuned-adm active
    # Current active profile: virtual-guest
    ```
    

---

### Q23. 🟢 Configure User-Specific Umask

Configure the default file creation permissions for user `natasha`:

- New **files** created by natasha must have permissions: `r--------` (400)
- New **directories** created by natasha must have permissions: `dr-x------` (500)

> 📝 **변형 포인트**
- umask 값 역산: 파일 기본 666, 디렉터리 기본 777에서lvs 빼기
- `.bash_profile`에 넣어야 로그인 시 적용
- ⚠️ **시험에서 umask는 맨 마지막에 설정!** — 중간에 하면 이후 파일 권한에 영향
> 
- 💡 풀이 가이드 (클릭하여 열기)
    
    umask 값 계산: 디렉터리 777 - 500 = 277. 파일은 실행 비트가 원래 안 붙으니까 666 - 277 = 400(실제론 666 & ~277 = 400). umask 0277이 맞아.
    
    ```bash
    # natasha의 .bash_profile에 umask 추가
    vi /home/natasha/.bash_profile
    
    # --- 맨 아래에 추가 ---
    umask 0277
    # (저장 후 종료 :wq)
    
    # ==============================
    # 검증
    # ==============================
    
    su - natasha
    umask
    # 0277
    
    touch ~/testfile
    ls -l ~/testfile
    # -r--------. 1 natasha natasha ... testfile (400)
    
    mkdir ~/testdir
    ls -ld ~/testdir
    # dr-x------. 2 natasha natasha ... testdir (500)
    
    # 테스트 파일 정리
    rm ~/testfile
    rmdir ~/testdir
    exit
    ```
    

---

### Q24. 🟢 Create a Shared Directory with Sticky Bit

Create a directory `/shared/projects` on Node2.

- Set permissions so that **all users** can create files in this directory.
- Only the **file owner** can delete their own files (other users cannot delete each other’s files).
- The directory must be owned by root.

> 📝 **변형 포인트**
- sticky bit = `chmod 1777` 또는 `chmod +t`
- SGID(2xxx)와 혼동 주의:
- SGID = 그룹 상속 (협업 디렉터리)
- Sticky = 소유자만 삭제 (공유 디렉터리)
> 
- 💡 풀이 가이드 (클릭하여 열기)
    
    sticky bit은 /tmp처럼 “누구나 쓸 수 있지만, 남의 파일은 못 지우는” 디렉터리를 만드는 거야.
    
    ```bash
    # ==============================
    # 디렉터리 생성 + sticky bit
    # ==============================
    
    mkdir -p /shared/projects
    
    # 1 = sticky bit
    # 7 = rwx (소유자)
    # 7 = rwx (그룹)
    # 7 = rwx (기타)
    chmod 1777 /shared/projects
    
    # ==============================
    # 검증
    # ==============================
    
    ls -ld /shared/projects
    # drwxrwxrwt. 2 root root ...
    # 마지막 t = sticky bit 활성
    
    # 기능 테스트
    su - harry -c "touch /shared/projects/harry_file"
    su - natasha -c "rm /shared/projects/harry_file"
    # rm: cannot remove ... Operation not permitted
    # → natasha가 harry 파일 삭제 못함 ✅
    ```
    
    ### Q25. 🟢 Configure Login Message Script
    

---

Create a script that displays a welcome message when user `ablerate` logs in:

- Create user `ablerate` with password `trootent`.
- Create a script named `/usr/local/bin/welcome` that prints `"Welcome to user ablerate"`.
- The message must be displayed automatically every time `ablerate` logs in.

> 📝 **변형 포인트**
- `.bashrc` = 셸 열릴 때마다 실행 (인터랙티브)
- `.bash_profile` = 로그인할 때만 실행
- 시험에서는 `.bashrc`가 더 안전 (SSH도 인터랙티브 셸)
> 
- 💡 풀이 가이드 (클릭하여 열기)
    
    ```bash
    # ==============================
    # 1. 사용자 생성
    # ==============================
    
    useradd ablerate
    echo "trootent" | passwd --stdin ablerate
    
    # ==============================
    # 2. 스크립트 생성
    # ==============================
    
    vi /usr/local/bin/welcome
    
    # --- 스크립트 내용 ---
    #!/bin/bash
    echo "Welcome to user ablerate"
    # (저장 후 종료 :wq)
    
    # 실행 권한 부여
    chmod +x /usr/local/bin/welcome
    
    # ==============================
    # 3. 로그인 시 자동 실행 설정
    # ==============================
    
    # ablerate의 .bashrc 맨 아래에 추가
    echo "/usr/local/bin/welcome" \
      >> /home/ablerate/.bashrc
    
    # ==============================
    # 4. 검증
    # ==============================
    
    su - ablerate
    # Welcome to user ablerate  ← 자동 출력되면 성공!
    exit
    ```
    

---

# 파트 C: 변형 드릴

> 모의시험(파트 B)에서 커버하지 못한 옵션 변형. 모의시험 완료 후 추가로 연습하세요.
각 드릴은 독립적인 단일 태스크입니다.
> 

---

### D1. tar — gzip 압축

> 모의시험 Q12에서 bzip2(`-cjf`)를 연습했으므로, 여기서는 gzip(`-czf`)을 연습.
> 

**Task:** Create a gzip-compressed archive of `/var/log` and save it as `/root/logs_backup.tar.gz`.

- 💡 풀이
    
    ```bash
    # -z = gzip (Q12의 -j = bzip2와 비교)
    tar -czf /root/logs_backup.tar.gz /var/log
    
    # 검증
    tar -tzf /root/logs_backup.tar.gz | head -5
    ```
    
    | 비교 | bzip2 (Q12) | gzip (이 드릴) |
    | --- | --- | --- |
    | 옵션 | `-cjf` | `-czf` |
    | 확장자 | `.tar.bz2` | `.tar.gz` |
    | 압축률 | 더 높음 (느림) | 보통 (빠름) |

---

### D2. LVM — xfs 파일시스템으로 생성 + lvextend -r

> 모의시험 Q19에서 ext3를 연습했으므로, 여기서는 xfs를 연습.
> 

**Task:** Create LV `datalv` in VG `datavg`, format with xfs, mount at `/mnt/xfsdata`. Then extend to 600M.

- 💡 풀이
    
    ```bash
    # 생성 (파티션/PV/VG는 이미 있다고 가정)
    lvcreate -n datalv -L 400M datavg
    mkfs.xfs /dev/datavg/datalv
    mkdir -p /mnt/xfsdata
    
    # fstab 등록 — 타입을 xfs로!
    blkid /dev/datavg/datalv
    vi /etc/fstab
    # UUID=xxxx /mnt/xfsdata xfs defaults 0 0
    mount -a
    
    # 확장 — -r이면 xfs_growfs 자동 호출
    lvextend -r -L 600M /dev/datavg/datalv
    
    # 검증
    df -hT /mnt/xfsdata
    # Type: xfs, Size: ~600M
    ```
    

---

### D3. LVM — ext4 파일시스템으로 생성 + lvextend -r

> ext4도 연습. ext3과 거의 동일하지만 명령어를 직접 쳐보는 게 중요.
> 

**Task:** Create LV `appslv` in VG `appsvg`, format with ext4, mount at `/mnt/ext4data`. Then extend to 500M.

- 💡 풀이
    
    ```bash
    lvcreate -n appslv -L 300M appsvg
    mkfs.ext4 /dev/appsvg/appslv
    mkdir -p /mnt/ext4data
    
    # fstab — 타입을 ext4로!
    blkid /dev/appsvg/appslv
    vi /etc/fstab
    # UUID=xxxx /mnt/ext4data ext4 defaults 0 0
    mount -a
    
    # 확장 — -r이면 resize2fs 자동 호출
    lvextend -r -L 500M /dev/appsvg/appslv
    
    # 검증
    df -hT /mnt/ext4data
    # Type: ext4, Size: ~500M
    ```
    

---

### D4. LVM 리사이즈 — extent 상대값

> 모의시험 Q21에서 절대 크기(`-L 300M`)를 연습했으므로, 여기서는 extent 추가(`-l +100`)를 연습.
> 

**Task:** Extend `/dev/datastore/database` by **100 additional extents**. The PE size is 8M.

- 💡 풀이
    
    ```bash
    # -l (소문자 L) = extent 개수
    # +100 = 현재에서 100 extents 추가
    # 100 × 8M = 800M 추가
    lvextend -r -l +100 /dev/datastore/database
    
    # 검증
    lvs /dev/datastore/database
    # 새 크기 = 기존 + 800M
    ```
    
    | 비교 | Q21 (절대) | 이 드릴 (상대) |
    | --- | --- | --- |
    | 옵션 | `-L 300M` | `-l +100` |
    | 의미 | 최종 크기 300M | 현재 + 100 extents |
    | 언제 | 문제가 “resize to 300M” | 문제가 “add 100 extents” |

명령어

---

### D5. find — 단일 크기 조건

> 모의시험 Q13에서 범위(`+3k -5k`)를 연습했으므로, 여기서는 단일 조건을 연습.
> 

**Task:** Find all files in `/etc` larger than 4 MiB and copy them to `/root/bigfiles`.

- 💡 풀이
    
    ```bash
    mkdir -p /root/bigfiles
    
    # -size +4M = 4 MiB 초과
    find /etc -size +4M \
      -exec cp -a {} /root/bigfiles/ \; \
      2>/dev/null
    
    # 검증
    ls -lh /root/bigfiles/
    ```
    

---

### D6. cron — 다른 시간 표현

> 모의시험 Q5에서 `30 12 * * *`(매일 12:30)을 연습했으므로, 여기서는 다른 표현을 연습.
> 

**Task A:** Run `uptime >> /home/harry/uptime.log` every **5 minutes** as user harry.
**Task B:** Run `logger "backup done"` at **2:00 AM on the 1st of every month** as root.

- 💡 풀이
    
    ```bash
    # Task A: 매 5분
    crontab -u harry -e
    # */5 * * * * /usr/bin/uptime >> /home/harry/uptime.log
    # */5 = 5분마다 (*/1 = 매 1분)
    
    # Task B: 매월 1일 새벽 2시
    crontab -e
    # 0 2 1 * * /usr/bin/logger "backup done"
    # 0분 2시 1일 매월 매요일
    
    # 검증
    crontab -u harry -l
    crontab -l
    ```
    
    | 주기 | cron 표현 |
    | --- | --- |
    | 매 5분 | `*/5 * * * *` |
    | 매일 12:30 | `30 12 * * *` |
    | 매월 1일 2시 | `0 2 1 * *` |
    | 매주 월요일 9시 | `0 9 * * 1` |

---

# 📋 최종 체크리스트

> 모의시험 완료 후, reboot하고 아래 항목을 전부 검증하세요.
> 

```bash
# === Node 1 ===
hostname                                    # servera.lab.example.com
ip addr show                                # 192.168.1.150/24
dnf repolist                                # BaseOS + AppStream
curl http://localhost:82                     # httpd 응답
id harry && id natasha && id sarah          # 사용자/그룹
sudo -l -U harry                            # sudo NOPASSWD
crontab -u harry -l                         # cron 작업
ls -ld /common/devops                       # SGID (drwxrws---+)
getfacl /var/tmp/fstab                      # ACL
chronyc sources                             # NTP
systemctl is-enabled autofs                 # autofs
ls /root/natashafiles/                      # find 결과
cat /root/lines | wc -l                     # grep 결과
ls -lh /root/backup.tar.bz2                # tar 아카이브
cat /usr/local/bin/findfiles                # 셸 스크립트
ssh natasha@serverb -o BatchMode=yes echo ok # SSH 키
podman images                               # 컨테이너 이미지 (semi)
ssh semi@localhost "podman ps"              # 컨테이너 자동시작

# === Node 2 ===
systemctl get-default                       # multi-user.target
dnf repolist                                # BaseOS + AppStream
df -hT /mnt/database                        # LVM ext3 마운트
lvs                                         # LV 크기 확인
swapon --show                               # swap 512M
df -hT /mnt/vo                              # LV 리사이즈 ~300M
tuned-adm active                            # recommended profile
su - natasha -c "umask"                     # 0277
ls -ld /shared/projects                     # sticky bit (t)
su - ablerate                               # Welcome 메시지
```