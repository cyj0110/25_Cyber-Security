# CTF

25-4 CTF 개념 정리 - 포렌식

## 포렌식

### 1번 문제

1) volatility_2.6_win64_standalone.exe -f mem.raw imageinfo - 분석에 적합한 프로필 확인
2) volatility_2.6_win64_standalone.exe -f mem.raw --profile=Win7SP1x64 filescan >> filescan.txt - 메모리상에 존재하는 파일 목록 확인 후 filescan.txt로 저장
3) filescan.txt에서 사용자가 보고 있던 사진 확인
4) volatility_2.6_win64_standalone.exe -f mem.raw --profile=Win7SP1x64 dumpfiles -Q 메모리주소값 -D ./ - 특정 메모리주소값(오프셋)에 존재하는 파일을 추출 후, 복원(경로 지정)

***

### 2번 문제

**부팅과정**

1) BIOS가 MBR을 로드
2) MBR 부트 코드는 부팅 가능한 파티션을 찾아, 해당 파티션의 첫 섹터에 위치한 VBR을 실행
3) MBR 코드는 운영체제 커널 파일을 찾아 로드

운영 체제가 로드될 때 읽는 드라이브의 첫 번째 섹터 : MBR(Master Boot Record): 512바이트

MBR - Partition Table Entry(0x01BE - 0x01FD) (시작했을 때의 위치)

파티션 테이블은 총 64 바이트로 이루어져 있음.

16바이트씩 총 4개의 파티션을 생성 가능.

```
- 1번째 바이트: 부팅 가능 여부(부트플래그) 0x80(부팅가능) / 0x00(부팅불가)
- 2~4번째 바이트: CHS 주소지정방식의 파티션 시작 위치
(CHS는 8바이트 이하 디스크에서만 유효, 현대 시스템에서는 LBA 방식을 사용, 무시 가능)
- 5번째 바이트: 파티션의 유형 (NTFS의 경우 07, FAT의 경우 0C)
- 6~8번째 바이트: CHS 주소지정 방식의 파티션의 끝 위치
- 9~12번째 바이트: LBA 주소 지정방식의 시작 위치: LBA 시작주소 - 해당 섹터로 이동해 확인 0x80(128번째 섹터)
- 13~16번째 바이트: 저장장치의 섹터 수(한 섹터는 512 byte)
```

MBR 시그니처 = 55 AA (2 byte)

**MBR Table**

C 드라이브에 대한 정보

00 02 03 00 07 61 1B 06 **80 00 00 00** **00 90 01 00** = BOLD표시는 의미 있는 정보

0x80 = 128 (C 드라이브의 시작 주소 섹터)

0x19000 = 102,400 (C 드라이브의 전체 중 섹터)

= 102,528

백업본은 마지막 섹터의 바로 앞 섹터에 위치 = 102,527

***

### 3번 문제

FTK Imager에서 삭제된 파일은 아이콘에 X표시 되어 있음.

마우스 우클릭 -> Export Files -> 경로 지정 -> 확인

***

### 4번 문제

이미지 스테가노 그래피 : 숨겨진 파일 추출

(HxD 이용) (Hint: 모든 파일은 고유의 파일 시그니처를 갖고 있음.)

1) HxD 이용해서 파일 시그니처 기반으로 숨겨진 파일 찾아보는 방법

시작 시그니처:	FF D8	JPEG 시작 (SOI)

끝 시그니처:	FF D9	JPEG 끝 (EOI)

**JPEG 시그니처를 통해**

- 파일 무결성 검사: 손상된 JPEG인지 확인할 수 있음
- 포렌식 분석: 숨겨진 JPEG 파일 찾을 때 시그니처로 검색
- 악성 파일 탐지: 확장자는 .jpg지만 내부 시그니처가 다르면 위장 가능성 있음 (예: .jpg.exe)

**Kali Linux에서 분리**
```
1) 터미널에서 binwalk forest.jpg
2) binwalk -D 'jpeg image:jpeg' forest.jpg
3) ls로 확인하면 forest.jpg.extracted 폴더에 추출된 사진이 있음.
```

***

### 5번 문제

Log File 분석 : NTFS 로그트래커 Tool 사용

1) NTFS 로그 트래커 활용, $Logfile Path에 $Logfile 로드
2) $MFT File Path에 $MFT 로드
3) Parse (DB 이름은 임의로 입력(test 등), 경로도 임의로 지정)
4) 로그 분석
5) 답안 cadt{YYYY-MM-DD HH:MM:SS_변경 전 이름_YYYY-MM-DD HH:MM:SS}

***

<br>
