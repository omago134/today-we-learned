
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

    

---

### Q2. 🔴 Configure YUM Repositories

Configure YUM repositories on Node1 using the following URLs:

- **BaseOS:** `http://repo.lab.example.com/rocky9.5/repo/BaseOS`
- **AppStream:** `http://repo.lab.example.com/rocky9.5/repo/AppStream`

> This task must be completed on **both** Node1 and Node2.

---

### Q3. 🔴 Troubleshoot SELinux and Web Server on Port 82

A web server running on non-standard port **82** is experiencing issues serving content. Debug and fix the issue according to these requirements:

- The web server must serve all existing HTML files from `/var/www/html`. Do not delete or modify existing content.
- The web server must serve content on **port 82**.
- The web server must **start automatically** on system reboot.


    

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


    

---

### Q5. 🟡 Configure Cron Jobs and Deny Crontab Access

**Part A:** Configure a cron job with the following specifications:
- **User:** `harry`
- **Schedule:** Every day at **12:30**
- **Command:** Print `"EX200"` using the `echo` command.

**Part B:** Deny the user `natasha` from using crontab.



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


---

### Q7. 🔴 Configure File ACLs

Copy the file `/etc/fstab` to `/var/tmp/fstab`. Configure the permissions of `/var/tmp/fstab` so that:

- The file `/var/tmp/fstab` is owned by the root user.
- The file `/var/tmp/fstab` belongs to the group root.
- The file is **not executable** by anyone.
- The user `natasha` is able to **read and write** `/var/tmp/fstab`.
- The user `harry` can **neither read nor write** `/var/tmp/fstab`.
- All other users (current or future) have the ability to **read** `/var/tmp/fstab`.


---

### Q8. 🟡 Configure NTP Time Synchronization

Configure your system to synchronize time from `ntp.lab.example.com`.


---

### Q9. 🟡 Configure Autofs for Remote User Home Directory

Configure autofs to automount the home directory of `remoteuser5`. Note the following:

- **NFS Server:** `nfs.lab.example.com`
- **Shared directory:** `/export/home/remoteuser5`
- **Local mount point:** `/rhome/remoteuser5`
- Home directory must be **writable** by the user.
- The autofs service must start automatically on boot.


    

---

### Q10. 🔴 Find Files by User and Copy

Search and copy files according to the following requirements:

- Find all files owned by user `natasha` across the entire system.
- Copy all found items to the `/root/natashafiles` directory.



---

### Q11. 🟡 Search for a String

- Search for all lines containing the string `"ich"` in the file `/usr/share/dict/words`.
- Save the results to `/root/lines`.


    

---

### Q12. 🟡 Create a Compressed Archive

Create a compressed archive with the following specifications:

- **Source directory:** `/etc`
- **Compression:** bzip2
- **Output file:** `/root/backup.tar.bz2`


    

---

### Q13. 🟡 Create a Shell Script

Create an executable script named `/usr/local/bin/findfiles` that performs the following:

- Search the `/usr/share` directory for **regular files** larger than **3 KiB** and smaller than **5 KiB**.
- Copy the found files to the `/root/foundfiles` directory.


    

---

### Q14. 🟡 Configure SSH Key-Based Authentication

Configure passwordless SSH login from Node1 to Node2 as the `natasha` user.

- Use SSH key pair authentication.
- After configuring, verify by connecting from Node1 to Node2 without entering a password.
- Using the configured key, securely copy the file `/etc/hostname` from Node1 to Node2’s `/tmp/` directory.



---

### Q15. 🟡 Build a Container Image from Containerfile

Perform the following tasks as the `semi` user:

1. Log in to the container registry at `registry.redhat.io`.
    - **Username:** `admin`
    - **Password:** `redhat123`
2. Download the Containerfile from `http://utility.lab.example.com/Containerfile`.
3. Build a container image named `myimage` using the downloaded Containerfile.


    

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


    

---

## 📌 Node 2 Tasks

---

### Q17. 🔴🔵 Reset Root Password and Set Boot Target

You do not know the root password for the Node2 server.

- Reset the root password to `redhat`.
- After gaining access, configure the system to boot into **multi-user.target** by default.


    

---

### Q18. 🔴 Configure YUM Repositories (Node2)

Configure YUM repositories on Node2 using the following URLs:

- **BaseOS:** `http://repo.lab.example.com/rocky9.5/repo/BaseOS`
- **AppStream:** `http://repo.lab.example.com/rocky9.5/repo/AppStream`



---

### Q19. 🔴 Create LVM with Specific PE Size and Extents

Create an LVM configuration according to the following specifications:

- Create a partition on `/dev/vdb` with type **Linux LVM**.
- **Volume Group:** `datastore` with PE size **8 MiB**
- **Logical Volume:** `database` with size **50 extents**
- **Filesystem:** ext3
- **Mount point:** `/mnt/database`
- The mount must persist after reboot.


    

---

### Q20. 🟡 Create a Swap Partition

Create a swap partition with the following requirements:

- **Size:** 512 MiB
- Do **not** modify or remove existing swap partitions.
- The swap must be automatically activated after reboot.


    

---

### Q21. 🔴 Resize a Logical Volume

Resize an existing logical volume according to the following specifications:

- **Target:** `/dev/datastore/vo`
- **Goal:** Resize to approximately **300 MiB** (acceptable range: 270-330 MiB)
- The logical volume is already mounted at `/mnt/vo`. The mount must persist after resize.
    
   
    

---

### Q22. 🟢 Configure Tuned Profile

Set the **recommended** Tuned profile for your system.


    

---

### Q23. 🟢 Configure User-Specific Umask

Configure the default file creation permissions for user `natasha`:

- New **files** created by natasha must have permissions: `r--------` (400)
- New **directories** created by natasha must have permissions: `dr-x------` (500)


    

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

    
    ### Q25. 🟢 Configure Login Message Script
    

---

Create a script that displays a welcome message when user `ablerate` logs in:

- Create user `ablerate` with password `trootent`.
- Create a script named `/usr/local/bin/welcome` that prints `"Welcome to user ablerate"`.
- The message must be displayed automatically every time `ablerate` logs in.


    

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
