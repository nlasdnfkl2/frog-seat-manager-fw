# Frog Seat Manager Firmware (`frog-seat-manager-fw`)

**Frog Seat Manager v1.0**은 **ESP32-C3** 마이크로컨트롤러 기반의 좌석 예약·입실/퇴실·인증 관리 펌웨어입니다. 로터리 노브(Knob)와 버튼(BT/PUSH) 입력, 화면(QR/좌석 현황) UI, 백그라운드 워커(`sheet-worker`)를 통해 Google Sheets(Apps Script) 및 Google Forms와 연동하여 좌석 상태를 실시간으로 관리합니다.

---

## 주요 기능

- **좌석 현황 및 예약/퇴실 관리 (`RESERVE`, `CHECKOUT`)**
  - 총 10개 좌석(`A1`–`A5`, `B1`–`B5`) 상태 조회 및 예약/퇴실 처리
  - 초기 좌석 목록 동기화(`?action=list`) 및 주기적 목록 동기화(`periodic LIST`) 지원
- **QR 코드 생성 및 사진 인증 연동**
  - 좌석 및 학번(`studentId`) 기반 QR 코드 생성 및 화면 표시 (`QRCodeGFX` 활용)
  - Google Forms 프리필 URL을 통한 인증/연계 플로우 지원
  - 서버 연동 사진 인증 상태 확인(`?action=checkPhoto`) 및 상태 반영(`PENDING`, `ACTIVE`, `EMPTY`, `verified`, `authTime`, `exitTime`)
- **자동 만료 및 정리 (`Cleanup`)**
  - 부팅 직후 기준 **10분 초과 미인증 좌석** 자동 퇴실 처리 (`authTime` 기준)
  - 부팅 직후 기준 **24시간 초과 퇴실 예정 좌석** 자동 퇴실 처리 (`exitTime` 기준)
  - 네트워크/응답 실패 시 자동 퇴실 재시도 및 로컬 상태 정리
- **네트워크 및 시간 동기화**
  - 캠퍼스 Wi-Fi(`KNU WIFI 6`, `eduroam` 등) 초기화 및 재연결 가드(`reconnect guard`) 지원
  - `kr.pool.ntp.org`, `pool.ntp.org` 기반 NTP 시간 동기화(`configTime` / `getLocalTime`)
- **안정성 및 원격 업데이트(OTA)**
  - `sheet-worker` 태스크를 통한 비동기 시트 요청 큐 처리 및 리다이렉트(302 등) 대응·재시도 로직
  - Free Heap 메모리 모니터링(`LOW HEAP warning` / `critical` 자동 재시작)
  - 원격 펌웨어 업데이트(`OTA`) 진행률 및 오류 로깅

---

## 하드웨어 및 빌드 환경

- **MCU / 타겟 보드:** ESP32-C3 (`ESP32C3_DEV`, 160MHz CPU, 4MB Flash, QIO, 80MHz Flash Freq)
- **프레임워크:** Arduino-ESP32 (ESP-IDF v5.5.5 기반)
- **주요 라이브러리:** `QRCodeGFX`, `WiFi` / `HTTPClient` / `WiFiClientSecure` (mbedTLS)
- **FQBN 예시:**
  ```text
  esp32:esp32:esp32c3:UploadSpeed=115200,CDCOnBoot=cdc,CPUFreq=160,FlashFreq=80,FlashMode=qio,FlashSize=4M,PartitionScheme=min_spiffs,DebugLevel=none,EraseFlash=none,JTAGAdapter=default,ZigbeeMode=default
