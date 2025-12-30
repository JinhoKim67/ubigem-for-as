# UbiGEM 빌드 & 배포 (고객사 AS 대응을 위한 테스트 및 분석용 소스)

---

## 1.1 UbiGEM 빌드 절차

> 사전 조건: `BIN\ConfuseEx` 폴더가 존재해야 합니다.

1. 탐색기에서 `UbiSam.Sources.Revision.sln` 실행
2. **`UbiSam.KeyLock` 빌드**
3. **`UbiCom.Net.Automata.SECS2` 빌드**
4. **`UbiSam.KeyLock` 빌드**<br>
   : UbiCom.Net.Automata.SECS2 빌드 후, 난독화 과정을 거치므로
     UbiGEM.Net 빌드 전 반드시 UbiSam.KeyLock을 한번 더 빌드해야됨   
5. **`UbiGEM.Net` 빌드**
   - 빌드 완료 후 `BIN\Confused` 폴더가 생성됨
6. `BIN\Confused` 폴더 내 **4개 파일**을  
   `Setup\BIN Data\UbiGEM\BIN` 폴더로 복사
7. `Setup\UbiGEM` 폴더에서 `UbiGEM.sln` 실행
8. **`UbiGEM` 빌드**
9. `Setup\Setup Files\` 폴더 내 생성된 **`UbiGEM.msi`** 를 압축(zip)하여 배포용 패키지 생성

---

## 1.2 개발자 및 테스트용 Host Simulator & EQP Sample 소스 빌드 절차

1. 탐색기에서 `UbiSam.Sources.Revision.sln` 실행
2. 공통 사항: CPU 아키텍쳐에 따른 빌드 모드 (32비트 vs. 64비트)
   : 각 모듈별로 32비트용(x86), 64비트용(x64)으로 구분되어 있으므로 모듈 빌드 모드 감안하여 빌드
   : 빌드 에라 시 CPU 모드에 따른 에라가 대부분임 (디버깅 & 테스트용이면 CPU를 하나로 통일하여 빌드 필요)
3. ** `UbiCom.Net` 및 `UbiGEM.Net`을 Debug 모드 빌드**   
   : Confuser.CI\LI.exe 실행 에라 발생 시, 
     ..\UbiCom.Net\UbiCom.Net.csproj 화일과 ..\UbiGEM.Net\UbiGEM.Net.csproj 화일 모두에서 <br>
     <PostBuildEvent>"ConfuserEx\Confuser.CLI.exe -n -o "..\BIN" "..\BIN\ConfuserEx\UbiGEM.crproj"</PostBuildEvent>를 <br>
     <PostBuildEvent>"$(SolutionDir)BIN\ConfuserEx\Confuser.CLI.exe" -n -o "$(SolutionDir)BIN" "$(SolutionDir)BIN\ConfuserEx\UbiGEM.crproj"</PostBuildEvent> <br>
     로 수정하고 다시 빌드함.
3. **`UbiGEM.Net.Simulator.Host` 빌드**
   : .NET 버전 상이로 인한 빌드(컴파일) 에라 발생 시,
     ubigem-for-as\UbiGEM.Net.Simulator.Host\UbiGEM.Net.Simulator.Host.csproj 내용 중
     <TargetFrameworkVersion>v4.7.2</TargetFrameworkVersion>
     --> <TargetFrameworkVersion>v4.8</TargetFrameworkVersion>로 변경
4. **`UbiSam.GEM.Sample.CPP` 빌드**
   1) .rc 화일 없음 에라 시, "..\Ubisam.GEM.Sample.CPP\Ubisam.GEM.Sample.CPP\res" 폴더의 .rc2 화일을
      "..\Ubisam.GEM.Sample.CPP\Ubisam.GEM.Sample.CPP" 폴더에 UbisamGEMSampleCPP.rc로 복사하여 사용
5. **`UbiCOM` 빌드**
   : 솔루션 메뉴의 맨 하위에 있는 'UbiCOM'은 모든 소스 빌드 후, 프로그램 설치 파일(.msi)를 만드는 내용으로 <br>
     현재 내용은 무시하고, 아래 3. UbiCOM 빌드 & 배포 과정을 통해 .msi를 만들어서 배포함

---

## 1.3 Host Simulator & EQP Sample 테스트 절차

1. 탐색기에서 `UbiSam.Sources.Revision.sln` 실행
2. **`UbiGEM.Net.Simulator.Host`, `UbiSam.GEM.Sample.CPP`, `UbiSam.GEM.Sample.CSharp` 빌드** <br>
   : 각 프로젝트 우클릭, 정리 -> 다시 빌드 실행 (빌드 순서 관계 없음)
3. **`UbiSam.GEM.Sample.CPP` 또는 `UbiSam.GEM.Sample.CSharp`실행**  <br>
   : 'File' 메뉴 하위에 'Open UGC' 버튼을 클릭해서 Sample.ugc 화일을 읽는다. <br>
   : 'Driver' 메뉴 하위의 'Initialize' 버튼을 클릭한 후, 'Start' 버튼을 클릭한다
4. **`UbiGEM.Net.Simulator.Host` 실행** <br>
   : 'Connection' 메뉴 하위에 'Configuration' 버튼을 클릭해서 'Configuration' Popup 창이 뜨면 <br>
   : 맨위 UGC File 선택 버튼을 클릭 후, 'Sample.ugc' 화일을 선택한 후 Popup 창을 닫는다 <br>
   : 'Connection' 메뉴 하위에 'Open Connection' 버튼을 클릭한다. <br>
   : EQP 실행 화면과 Host Simulator화면에서 각각 여러가지 메시지를 보내보고 Log 화면에 표시되는 것을 확인한다.

---

## 2. UbiGEM 배포 방식 (현재 운영 방식)

현재는 위의 **1) ~ 8) 빌드 과정을 수행하지 않고**,  
아래 ZIP 파일을 그대로 고객에게 제공하고 있습니다.

- Repository: `com.ubisam.projects.ubigem.dist`
- 배포 파일: **`UbiGEM_20211221.zip`**

---

## 3. UbiCOM 빌드 & 배포

1. **UbiCOM은 제공된 소스 기준으로 빌드 및 배포 가능**
2. `Setup\UbiCOM` 폴더에서 `UbiCOM.sln` 실행 후 빌드

---

## 4. UbiGEM.PAC

- **UbiGEM.PAC 소스 및 빌드 절차는 구미 쪽 추가 확인 필요**

---

## 5. KeyLock (키락) 생성

- **장석주 부장님을 통해 KeyLock 1.0 생성**
- UbiGEM / UbiGEM.PAC 판매 시, 키락 생성 및 발송 절차는  
  장석주 부장님을 통해 진행
