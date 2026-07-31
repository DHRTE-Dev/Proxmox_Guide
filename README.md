# Proxmox VE 초기 설치 및 최적화 가이드

본 문서는 Proxmox VE(PVE)를 설치한 직후 진행하는 필수 초기 설정 및 최적화 과정을 정리한 가이드라인입니다.

## 목차
- [설치 방법](#proxmox-ve-iso-다운로드-및-설치)
- [설치 후 스크립트](#PVE-Post-Install-스크립트-실행-및-최적화)
- [MOTD 비활성화](#motd-비활성화)
- [local / local-lvm 통합](#local--local-lvm-통합)
- [Tailscale 설치 및 연동](#Tailscale-설치-및-연동)

- [커스텀 테마 설치](#커스텀-테마-추가)
- [윈도우 EFI 복구](#윈도우-듀얼부팅-사용시-윈도우-EFI-복구)

---

## Proxmox VE ISO 다운로드 및 설치
1. **ISO 다운로드**: [Proxmox 공식 다운로드 페이지](https://www.proxmox.com/en/downloads)에서 최신 버전의 Proxmox VE ISO 이미지를 다운로드합니다.
2. **부팅 USB 제작**: Rufus 또는 Ventoy 유틸리티를 사용하여 다운로드한 ISO 파일을 USB 드라이브에 플래싱합니다.
3. **시스템 설치**: BIOS/UEFI 진입 후 부팅 순서를 USB로 변경하여 설치를 진행합니다.

---

## PVE Post Install 스크립트 실행 및 최적화
유료 구독 경고 팝업을 제거하고, 무료 사용자용 공식 저장소(Repository) 활성화 및 시스템 패키지 업그레이드를 자동화 스크립트로 처리합니다.

1. Proxmox 웹 GUI 셸(Shell) 또는 SSH 터미널에 `root` 계정으로 접속합니다.
2. [Proxmox VE Community Scripts](https://community-scripts.org/)의 최적화 스크립트를 실행합니다. (PVE Post Install)

```bash
bash -c "$(curl -fsSL [https://raw.githubusercontent.com/community-scripts/ProxmoxVE/main/tools/pve/post-pve-install.sh](https://raw.githubusercontent.com/community-scripts/ProxmoxVE/main/tools/pve/post-pve-install.sh))"
```

'pve-enterprise' 및 'ceph enterprise' 리포지토리 비활성화  
'pve-no-subscription' 및 'ceph(no-subscription)' 리포지토리 추가  
'pve-test' 리포지토리 추가는 하지 말 것 ('no' 선택)  
'subscription nag' (로그인 시 구독 메시지 창) 비활성화  
'high availabily' 비활성화  
'corosync' 비활성화  

---

## MOTD 비활성화
SSH 로그인 시 출력되는 불필요한 리눅스 기본 환영 메시지를 비워 터미널 접속 환경을 깔끔하게 정리합니다.  
다음 명령어를 셸(Shell)에 입력합니다.

```bash
cat /dev/null > /etc/motd
```

---

## local / local-lvm 통합
관리 편의성 및 저장소 용량 최대 활용을 위해 두 저장소를 통합합니다.  
다음 명령어를 셸(Shell)에 차례대로 입력합니다.

local-lvm 이 연결된 부분을 제거합니다.
```bash
lvremove /dev/pve/data
```

lvresize를 통해서 새로 생긴 남은 공간들을 /dev/pve/root (local) 에 할당합니다.
```bash
lvresize -l +100%FREE /dev/pve/root
```

resize2fs를 통해서 파일시스템의 크기를 변경합니다.
```bash
resize2fs -p /dev/pve/root
```

이후에  
Datacenter > Storage > local-lvm > Remove(상단) 순으로 선택해서 빈껍데기만 남아있는 local-lvm을 삭제합니다.  
Datacenter > Storage > local > Edit(상단) 순으로 선택한 후, Content란에 전부 체크를 합니다.

해당 노드 > local > Summary 의 Usage 탭에서 늘어난 용량을 확인해 봅시다.

---

## Tailscale 설치 및 연동
포트 포워딩이나 복잡한 VPN 설정 없이, Proxmox 웹 GUI 및 가상머신(VM/CT)에 외부에서 안전하게 원격 접속할 수 있도록 가상 사설망(Tailscale)을 구축합니다.

셸(Shell)에서 아래 명령어를 실행하여 리눅스용 Tailscale을 다운로드하고 설치합니다.

```bash
curl -fsSL https://tailscale.com/install.sh | sh
```

설치 완료후 다음 명령어를 실행하여 나타난 주소로 이동하여 Tailscale 로그인 및 연동을 진행합니다.
```bash
tailscale up
```

이후 다음 명령어를 실행하여 시작 서비스로 등록하고 서비스를 시작합니다.
```bash
systemctl enable --now tailscaled
```

해당 명령어로 현재 할당된 주소와 서비스 등록을 확인합니다.
```bash
tailscale ip -4
```

```bash
systemctl status tailscaled
```

---

## 커스텀 테마 추가

[GitHub Project](https://github.com/IT-BAER/proxmorph)

```
bash <(curl -fsSL https://raw.githubusercontent.com/IT-BAER/proxmorph/main/install.sh) install
```

테마 적용법
1. 브라우저 새로고침 (Ctrl+Shift+R)
2. 콘솔 우상단 : 유저 이름 > Color Theme
3. ProxMoph 테마 선택


---

## 윈도우 듀얼부팅 사용시 윈도우 EFI 복구
윈도우 듀얼부팅 사용시 윈도우 EFI를 복구합니다.

**복구 환경 진입 및 명령 프롬프트 열기**

   1. Windows 복구 USB로 부팅합니다.
   2. 언어 및 키보드 설정 화면이 나오면 다음을 누릅니다.
   3. 왼쪽 아래의 컴퓨터 복구(R)를 클릭합니다.
   4. 문제 해결 -> 고급 옵션 -> 명령 프롬프트(Command Prompt)를 선택합니다.

<br>

**EFI 파티션 확인 및 드라이브 문자 할당**

먼저 Windows가 설치된 드라이브와 복구할 EFI 파티션이 어디에 있는지 찾아야 합니다.  
명령 프롬프트창이 열리면 다음 명령어를 순서대로 입력합니다.

```bash
diskpart
list disk
```

```bash
sel disk 0  (해당 디스크 번호 입력)
list vol
```

**1. EFI 파티션이 이미 존재하는 경우**

```bash
sel vol 0  (EFI 볼륨 번호 입력)
assign letter=V  (EFI 파티션에 임의로 V 드라이브 문자 할당)
exit
```

```bash
bcdboot C:\Windows /s V: /f UEFI
```

**2. EFI 파티션이 아예 없는 경우**

LLM의 도움을 받도록 하자...

---
