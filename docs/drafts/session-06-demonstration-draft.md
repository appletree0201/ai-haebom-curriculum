---
title: 6회차 시연내용 초안
type: draft
session: 06
topic: Windows Git 설치와 기본 기록
audience: complete-beginners
duration: 1h
share: notion
---

# 6회차 시연내용

## 시연 목적
6회차 시연은 Git을 설치하지 않은 상태에서 시작해, 작은 파일 하나를 변경 기록으로 남기는 흐름을 보여줍니다.

목적은 Git 전체를 배우는 것이 아니라 `init`, `status`, `add`, `commit`의 흐름을 눈으로 확인하는 것입니다.

## 준비 상태
- Windows PC를 기준으로 진행합니다.
- Git 설치 여부를 확인할 수 있습니다.
- VS Code와 터미널을 열 수 있습니다.
- 실습 폴더가 준비되어 있습니다.

## 진행 흐름
1. Git 설치 여부를 확인합니다.
2. 설치되어 있지 않다면 설치 흐름을 보여줍니다.
3. VS Code에서 실습 폴더를 엽니다.
4. 터미널을 엽니다.
5. `git init`을 실행합니다.
6. `git-practice.md` 파일을 만듭니다.
7. `git status`로 상태를 확인합니다.
8. `git add`로 기록할 파일을 선택합니다.
9. `git commit`으로 기록을 남깁니다.

## 화면에서 보여줄 것
### 1. 설치 확인
```text
git --version
```

확인할 점은 Git 버전이 보이는지입니다.

### 2. 저장소 시작
```text
git init
```

확인할 점은 현재 폴더가 Git 기록을 남길 준비가 되었는지입니다.

### 3. 상태 확인
```text
git status
```

확인할 점은 새 파일이나 변경된 파일이 보이는지입니다.

### 4. 기록할 파일 선택
```text
git add git-practice.md
```

확인할 점은 이번 기록에 포함할 파일을 선택한다는 점입니다.

### 5. 기록 남기기
```text
git commit -m "첫 Git 연습 기록"
```

확인할 점은 커밋이 하나 만들어졌는지입니다.

## 결과 확인 기준
- `git --version`으로 설치 여부를 확인했는지 봅니다.
- `git status` 결과가 단계마다 달라지는지 봅니다.
- `git add` 후 상태가 바뀌는지 봅니다.
- `git commit` 후 작업할 내용이 없다는 메시지에 가까워지는지 봅니다.

## 함께 확인할 포인트
- `status`는 자주 확인하는 명령입니다.
- `add`는 저장이 아니라 기록할 대상을 고르는 일입니다.
- `commit`은 기록을 남기는 일입니다.
- 오늘은 GitHub에 올리지 않습니다.
- 오류 메시지가 나오면 그대로 읽고 기록합니다.

## 마무리 질문
- `git status`는 언제 쓰나요?
- `git add`와 `git commit`은 무엇이 다른가요?
- 기록을 남기면 나중에 무엇이 좋아질까요?
