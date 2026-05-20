# Proxmox VE 미니 PC 초기 설치 및 최적화 가이드

본 문서는 Proxmox VE(PVE)를 설치한 직후 진행하는 필수 초기 설정 및 최적화 과정을 정리한 가이드라인입니다.

---

## 1. Proxmox VE ISO 다운로드 및 설치
1. **ISO 다운로드**: [Proxmox 공식 다운로드 페이지](https://www.proxmox.com/en/downloads)에서 최신 버전의 Proxmox VE ISO 이미지를 다운로드합니다.
2. **부팅 USB 제작**: Rufus 또는 Ventoy 유틸리티를 사용하여 다운로드한 ISO 파일을 USB 드라이브에 플래싱합니다.
3. **시스템 설치**: BIOS/UEFI 진입 후 부팅 순서를 USB로 변경하여 설치를 진행합니다.

---

## 2. PVE Post Install 스크립트 실행 및 최적화
유료 구독 경고 팝업을 제거하고, 무료 사용자용 공식 저장소(Repository) 활성화 및 시스템 패키지 업그레이드를 자동화 스크립트로 처리합니다.

1. Proxmox 웹 GUI 셸(Shell) 또는 SSH 터미널에 `root` 계정으로 접속합니다.
2. [Proxmox VE Community Scripts](https://community-scripts.org/)의 최적화 스크립트를 실행합니다.

```bash
bash -c "$(curl -fsSL [https://raw.githubusercontent.com/community-scripts/ProxmoxVE/main/tools/pve/post-pve-install.sh](https://raw.githubusercontent.com/community-scripts/ProxmoxVE/main/tools/pve/post-pve-install.sh))"

1. 'pve-enterprise' 및 'ceph enterprise' 리포지토리 비활성화
2. 'pve-no-subscription' 및 'ceph(no-subscription)' 리포지토리 추가
3. 'pve-test' 리포지토리 추가는 하지 말 것 ('no' 선택)
4. 'subscription nag' (로그인 시 구독 메시지 창) 비활성화
5. 'high availabily' 비활성화
6. 'corosync' 비활성화

--

## 3. MOTD 비활성화
SSH 로그인 시 출력되는 불필요한 리눅스 기본 환영 메시지를 비워 터미널 접속 환경을 깔끔하게 정리합니다.

다음 명령어를 셸(Shell)에 입력합니다.

```bash
cat /dev/null > /etc/motd

--

## 윈도우 듀얼부팅 사용시 윈도우 EFI 복구
윈도우 듀얼부팅 사용시 윈도우 EFI를 복구합니다.

1. 복구 환경 진입 및 명령 프롬프트 열기
  1. Windows 복구 USB로 부팅합니다.
  2. 언어 및 키보드 설정 화면이 나오면 다음을 누릅니다.
  3. 왼쪽 아래의 컴퓨터 복구(R)를 클릭합니다.
  4. 문제 해결 -> 고급 옵션 -> 명령 프롬프트(Command Prompt)를 선택합니다.

2. EFI 파티션 확인 및 드라이브 문자 할당
먼저 Windows가 설치된 드라이브와 복구할 EFI 파티션이 어디에 있는지 찾아야 합니다.
명령 프롬프트창이 열리면 다음 명령어를 순서대로 입력합니다.

```bash
diskpart
list disk

```bash
sel disk 0  (해당 디스크 번호 입력)
list vol

  1. EFI 파티션이 이미 존재하는 경우
```bash
sel vol 2  (EFI 볼륨 번호 입력)
assign letter=V  (EFI 파티션에 임의로 V 드라이브 문자 할당)
exit


