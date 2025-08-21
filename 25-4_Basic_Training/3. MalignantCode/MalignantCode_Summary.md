# CTF

25-4 CTF 개념 정리 - 악성코드

## 악성코드

### 1번 문제

**PE 파일 구조**

PE(Portable Executable) 파일 : 윈도우 운영체제의 실행파일 형식

```
실행 계열: EXE, SCR
드라이버 계열: SYS, VXD
라이브러리 계열 : DLL, OCX, CPL, DRV
오브젝트 계열 : OBJ
```

DOS 헤더 시그니처: 0x4D 0x5A (ASCII: "MZ")

PE 파일 기본 구조는 크게 PE 헤더와 바디(섹션)로 구성된다.

<img width="451" height="392" alt="화면 캡처 2025-08-06 102640" src="https://github.com/user-attachments/assets/4e5b60b1-7ffd-4f87-86e7-c6f196972274" />

**ImageBase(기준점)**
- PE 파일이 메모리에 로딩될 때의 기본 주소 값
- 가상 메모리에서의 PE 파일이 로딩되는 주소, 일반적으로 EXE 파일의 값은 0x00400000
- DLL의 경우 0x01000000
- 실행 시 코드와 데이터가 이 주소부터 시작된다고 가정하고 작성됨

**Address of EntryPoint**
- 실행이 시작되는 진입점 함수의 RVA (Relative Virtual Address)
- 운영체제가 실행 시 이 주소를 찾아 코드 실행을 시작함

**Start of TextSection**
-.text 섹션(즉, 실행 코드)의 시작 위치
- 보통 바이너리에서 기계어 코드가 위치하는 첫 주소

**x32dbg(디버거)**

(문자열 참조 기능)

**자주 쓰는 조합**
- f2 : breakpoint
- Ctrl + f2 : 다시 시작
- f9 : entrypoint 진입

**그 외 기능**
- f7 : 하나의 OP code 실행 (함수 내부로 진입 // 안으로 단계 진행)
- f8 : 하나의 OP code 실행 (함수만 실행 // 건너서 단계 진행 (스텝 오버))
- Ctrl + G : 원하는 주소로 이동 (해당 주소까지 실행되지는 않음)
- Ctrl + E : 데이터 편집

문제에서, 

ImageBase(기준점): 0x 00400000

AddressOfEntryPoint: 0x 0040126F (실제 주소), 0x 0000126F (상대 주소)

StartOfTextSection:  0x 00401000 (실제 주소), 0x 00001000 (상대 주소)

가상 주소는 얼마만큼 떨어져있는지를 표시.

***

### 2번 문제

```asm
add eax, 1
mov dword ptr ss:[ebp-8], eax
cmp dword ptr ss:[ebp-8], 4
jge 002.401081
```
=> 1씩 증가하며 4번의 반복 과정

```asm
add ecx, 1
mov dword ptr ss:[ebp-4], ecx
cmp dword ptr ss:[ebp-4], 8
jge 002.40107F
```
=> 1씩 증가하며 8번의 반복 과정

```asm
movsx ecx, byte ptr ds:[eax+edx*4+41E000]
```
=> 41E000 주소와 데이터를 반복 과정에서 사용되는 레지스터와 함께 연산해 한글자씩 가져옴

```asm
cmp edx, dword ptr ss:[ebp-14]
je 002.40107D
```
=> 가져온 글자와 사용자의 입력을 한 글자씩 비교

분석된 정보를 바탕으로 Python Script 작성
```python
j = 0
i = 0

a = "분석된 String"

for j in range(4):
     for i in range(8):
          print(a[i*4+j], end='')
```

***

### 3번 문제

dropper.doc : 문서형 악성코드

악성코드가 흔히, vbaproject.bin 바이너리 파일에 저장 (공격자는 여기에 악성 VBA 스크립트(= 매크로)를 집어넣어, 사용자가 문서를 열 때 자동으로 악성 행위를 수행하도록 만들 수 있음.)

***

### 4번 문제

패킹: 실행 파일 크기를 줄이고, 실행 파일 내부를 분석하기 어렵게 만듦.

UPX는 그 중 가장 널리 사용되는 오픈 소스 패킹 툴

1. 파일 크기 감소: 프로그램을 컴팩트하게 만들어 배포나 네트워크 전송을 효율적으로 함.
2. 코드 보호/보안: 리버스 엔지니어링, 디버깅, 디스어셈블링을 어렵게 하여 소스 분석 방지(특히 악성코드에서 많이 사용)
3. 데이터 보호: 주요 기능, 라이선스 인증, 민감 정보 노출 최소화
4. 실행 파일 내부 구조 변경: 원본 코드, 데이터, 리소스를 숨기거나 섹션 이름 변경 등.

<어셈블리 명령어>

pushad: EAX~EDI 등 주요 레지스터 값을 스택에 저장(백업)

popad: 스택의 값을 원래 레지스터로 복구

popad 이후 JMP가 원본 코드(OEP)로 이동 -> **리버스 엔지니어링에서 중요한 단서**

***

### 5번 문제

Crackme 바이너리 분석 (x32dbg 사용)

1. F9 엔트리 포인트 진입 후 문자열 찾아보기
2. 입력 받는 부분부터 비교하는 값 리버싱 해보기
3. 첫번째 레지스터 변수: 문자열의 인덱스
4. 두번째 레지스터 변수: 비교하는 값
5. FFFFFFC3 = 3D(16진수)에 -를 한 값 = -61(10진수)

```asm
1. mov edxm 1 : edx 레지스터에 1을 복사
  - edx = 1
2. imul eax, edx, 0 :
  - eax = 1 * 0 = 0
3. movsx ecx, byte ptr ss:[ebp+eax-28]

ebp 레지스터 주소 : 0019FF28
ebp - 28 = 0019FF00
ebp+eax-28 = 0019FF00 + eax = 0019FF00 (∵eax = 0)
```
```
1. mov edx, 1 : edx 레지스터에 1을 복사
  - edx = 1

2. imul eax, edx, E : edx * E(14)를 한 값을 eax에 복사한다.
  - eax = 14(10진수)

[ebp-28+eax]
어떤 문자열 A의 A[14]

3. sub ecx, 3 : ecx 레지스터에서 3을 뺀 값을 ecx에 저장

4. cmp ecx, 30 : ecx를 30과 비교
ecx - 3 = 30
ecx = 33 = 문자열 3
```

***

<br>
