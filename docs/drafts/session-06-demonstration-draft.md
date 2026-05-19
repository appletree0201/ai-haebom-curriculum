---
title: 6회차 시연자료 초안
type: draft
session: 06
topic: Windows Git 설치와 기본 기록 남기기
audience: complete-beginners
duration: 1h
share: notion
---

# 6회차 시연자료

## 시연 목적
- Git이 설치되지 않은 Windows PC를 기준으로 시작한다.
- Git 설치, 설치 확인, VS Code 터미널 실행 흐름을 보여 준다.
- `git-practice.md` 파일로 Git 기본 명령어를 실습한다.
- `init`, `status`, `add`, `commit`의 의미를 초보자 기준으로 설명한다.
- 스테이징과 커밋의 차이를 보여 준다.

## 오늘 보여줄 핵심
오늘의 핵심은 아래 흐름입니다.

```text
Git 설치
-> 설치 확인
-> 실습 파일 만들기
-> 폴더 기록 시작
-> 변경 상태 확인
-> 스테이징
-> 커밋
```

## 오늘 제외할 내용
시작 전에 제외 범위를 분명히 말합니다.

```text
오늘은 GitHub, branch, merge, conflict, push, pull을 다루지 않습니다.
오늘은 내 PC에서 내 파일의 변경 기록을 남기는 것까지만 합니다.
```

## 1. Git 설치 페이지 보여주기
브라우저에서 Git 공식 설치 페이지를 엽니다.

```text
https://git-scm.com/install/windows.html
```

Windows용 설치 파일을 다운로드하는 위치를 보여 줍니다.

설치 중에는 특별한 이유가 없으면 기본값으로 `Next`를 눌러 진행한다고 안내합니다.

회사 PC에서는 보안 정책 때문에 설치가 막힐 수 있다고 설명합니다.

이 경우 담당자나 관리자 확인이 필요합니다.

## 2. VS Code 다시 열기
Git 설치 후에는 VS Code를 완전히 닫았다가 다시 엽니다.

이유는 설치된 Git을 VS Code 터미널이 새로 인식해야 하기 때문입니다.

## 3. VS Code 터미널 열기
VS Code 상단 메뉴에서 터미널을 엽니다.

```text
Terminal > New Terminal
```

터미널이 화면 아래쪽에 열리는 것을 보여 줍니다.

## 4. 설치 확인하기
터미널에서 아래 명령어를 입력합니다.

```text
git --version
```

정상 예시입니다.

```text
git version 2.xx.x
```

버전 숫자는 사람마다 달라도 됩니다.

`git version`이 나오면 설치 확인은 끝입니다.

## 5. 실습 폴더 열기
VS Code에서 Git 연습용 폴더를 엽니다.

```text
File > Open Folder
```

예시 폴더 이름은 아래처럼 안내합니다.

```text
git-practice
```

실제 업무 폴더가 아니라 연습 폴더를 쓰는 이유도 설명합니다.

```text
처음 Git을 배울 때는 업무 파일이 들어 있는 폴더보다 연습 폴더가 안전합니다.
```

## 6. git-practice.md 만들기
왼쪽 `Explorer`에서 새 파일을 만듭니다.

파일 이름입니다.

```text
git-practice.md
```

파일 내용입니다.

```md
# Git 연습

오늘 Git 설치와 기본 명령어를 배웠습니다.
```

저장합니다.

```text
Ctrl + S
```

## 7. git init 실행
터미널에서 아래 명령어를 입력합니다.

```text
git init
```

설명 멘트입니다.

```text
git init은 이 폴더를 Git으로 기록하기 시작하겠다는 뜻입니다.
한 폴더에서 처음 한 번만 실행하면 됩니다.
```

## 8. git status 실행
현재 상태를 확인합니다.

```text
git status
```

설명 멘트입니다.

```text
git status는 지금 뭐가 바뀌었는지 확인하는 명령어입니다.
초보자는 status를 자주 입력해도 됩니다.
```

`git-practice.md`가 아직 기록되지 않은 파일로 보이는 것을 확인합니다.

## 9. git add 실행
이번 기록에 넣을 파일을 고릅니다.

```text
git add git-practice.md
```

설명 멘트입니다.

```text
git add는 이번 저장 기록에 넣을 파일을 고르는 단계입니다.
이 대기 공간을 스테이징이라고 부릅니다.
```

## 10. 스테이징 상태 확인
다시 상태를 확인합니다.

```text
git status
```

방금 전과 표시가 달라진 것을 보여 줍니다.

설명 멘트입니다.

```text
방금 전에는 기록할 파일을 고르기 전이었고,
지금은 커밋할 준비가 된 상태입니다.
```

## 11. git commit 실행
준비한 파일을 기록으로 남깁니다.

```text
git commit -m "첫 Git 연습 기록"
```

설명 멘트입니다.

```text
git commit은 스테이징에 올려 둔 파일을 하나의 저장 기록으로 남기는 단계입니다.
-m 뒤의 문장은 이 기록의 제목입니다.
```

## 12. 깨끗한 상태 확인
마지막으로 다시 확인합니다.

```text
git status
```

변경사항이 남아 있지 않으면 정상입니다.

설명 멘트입니다.

```text
이 상태는 현재 작업 폴더의 변경사항이 모두 기록되었다는 뜻입니다.
```

## 시연 순서
1. 오늘 제외할 내용을 먼저 말한다.
2. Git 공식 설치 페이지를 보여 준다.
3. Windows용 Git 설치 흐름을 설명한다.
4. VS Code를 다시 연다.
5. VS Code 터미널을 연다.
6. `git --version`으로 설치를 확인한다.
7. `git-practice` 실습 폴더를 연다.
8. `git-practice.md` 파일을 만든다.
9. `git init`을 실행한다.
10. `git status`를 실행한다.
11. `git add git-practice.md`를 실행한다.
12. 다시 `git status`를 실행한다.
13. `git commit -m "첫 Git 연습 기록"`을 실행한다.
14. 마지막으로 `git status`를 실행한다.

## 참가자에게 던질 질문
- Git은 무엇을 기록하는 도구인가?
- `git status`는 언제 쓰면 좋은가?
- `git add`와 `git commit`은 무엇이 다른가?
- 스테이징은 어떤 대기 공간인가?
- 오늘 다루지 않은 GitHub와 Git은 무엇이 다를 것 같은가?

## 자주 막힐 때 안내
### git 명령어를 찾을 수 없는 경우
Git 설치 후 VS Code를 다시 열었는지 확인합니다.

그래도 안 되면 PC를 재부팅하거나 설치 상태를 확인합니다.

### commit에서 이름과 이메일을 요구하는 경우
아래 명령어가 필요할 수 있다고 안내합니다.

```text
git config --global user.name "내 이름"
git config --global user.email "내 이메일"
```

회사 기준이 있으면 회사 기준을 따릅니다.

## 마무리
6회차 시연의 핵심은 Git을 협업 도구로 어렵게 시작하지 않는 것입니다.

내 PC에서 내 파일의 변경 기록을 남기는 것부터 시작합니다.
