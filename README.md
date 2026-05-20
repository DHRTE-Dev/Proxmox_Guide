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
