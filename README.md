# 🧭 초보자를 위한 Git & Branch 완벽 이해 가이드

이 문서는 Git을 처음 접하는 분도 **실제 작업 흐름과 에러 상황**을 자연스럽게 이해할 수 있도록, 이 저장소(`oz-ai-08`)에 남아 있는 실제 커밋 기록을 바탕으로 정리한 친절한 안내서입니다.

---

## 1. 30초 만에 잡는 핵심 비유

Git 명령어를 외우기 전에, 머릿속에 아래 4가지 그림만 먼저 그려보세요.

| 용어 | 쉬운 비유 | 실제 의미 |
| :--- | :--- | :--- |
| **저장소 (Repository)** | 게임 세이브 폴더 | 프로젝트의 모든 파일과 변경 기록이 저장되는 공간 |
| **커밋 (Commit)** | 세이브 포인트 / 사진 찍기 | 현재 코드 상태를 하나의 기록으로 저장하는 것 |
| **브랜치 (Branch)** | 평행우주 / 복사된 연습장 | 원본(`main`)을 건드리지 않고 안전하게 새 기능을 실험하는 독립 공간 |
| **병합 (Merge)** | 평행우주 합치기 | 브랜치에서 완성한 작업을 본래 세계(`main`)로 합치는 것 |
| **원격 저장소 (`origin`)** | 클라우드 백업함 (GitHub) | 내 컴퓨터(로컬)의 기록을 인터넷에 올려두고 공유하는 곳 |

---

## 2. 이 저장소의 실제 히스토리 따라가기

현재 이 저장소에는 Git의 가장 중요한 과정이 모두 담겨 있습니다. 아래에서 위로(과거 → 현재) 순서대로 살펴보겠습니다.

```text
*   ffa38e1 (HEAD -> main, origin/main) Fix conflict   ← 4. 충돌 해결 후 최종 병합!
|\
| * c9653bd (right) hello, right                       ← 3-2. right 브랜치에서 hello.py 수정
* | a62cbc3 (left) hello, left                         ← 3-1. left(main)에서 hello.py 수정
|/
*   4561b24 Merge branch 'feat/ui'                     ← 2-3. 두 갈래를 합침 (MERGE_MSG 등장)
|\
| * 69c041b Update ui.py                               ← 2-2. feat/ui 브랜치에서 ui.py 수정
* | c189a4b Create ai.py                               ← 2-1. main에서 ai.py 생성
|/
* 1324512 Create ui.py                                 ← 1-2. ui.py 추가 커밋
* 4ba2406 Create files                                 ← 1-1. 최초 커밋 (기초 파일 생성)
```

---

### 📍 1단계: 시작과 첫 커밋 (`Create files` → `Create ui.py`)

Git은 작업한 파일을 바로 저장하지 않고 **장바구니(`add`)에 담은 뒤 결제(`commit`)**하는 2단계를 거칩니다.

```bash
git init                  # 현재 폴더를 Git 저장소로 초기화
git add .                 # 변경된 모든 파일을 장바구니(Staging Area)에 담기
git commit -m "Create files"   # 세이브 포인트(커밋) 생성
```

> **💡 왜 `add`와 `commit`을 나눌까요?**
> 파일 10개를 고쳤더라도, 그중 3개만 골라서 깔끔하게 하나의 커밋으로 묶어 저장할 수 있기 때문입니다.

---

### 📍 2단계: 브랜치 분기와 두 가지 병합 (`Merge branch 'feat/ui'`)

`1324512 (Create ui.py)` 지점에서 작업이 두 갈래로 나뉘었습니다.
- `main` 줄기에서는 `ai.py`를 새로 만들었고 (`c189a4b`)
- `feat/ui` 브랜치에서는 `ui.py`를 수정했습니다 (`69c041b`)

#### 자주 쓰는 브랜치 명령어
```bash
git branch feat/ui        # feat/ui 브랜치(이름표) 만들기
git switch feat/ui        # feat/ui 브랜치로 이동하기
# (팁) 위 두 줄을 한 번에 하려면: git switch -c feat/ui

git switch main           # 병합을 위해 다시 main 브랜치로 돌아오기
git merge feat/ui         # main에 feat/ui 작업 합치기
```

#### 🤔 왜 `git merge`를 할 때 가끔 `MERGE_MSG` 창이 열릴까요?
에러가 아니라 **지극히 정상적인 과정**입니다! 상황에 따라 병합 방식이 두 가지로 나뉘기 때문입니다.

1. **Fast-forward 병합 (그냥 통과)**: `main`에는 아무 새 커밋이 없고 다른 브랜치만 혼자 앞서갔을 때, Git은 새 커밋을 만들지 않고 `main` 위치만 앞으로 쓱 옮깁니다.
2. **3-way 병합 (`MERGE_MSG` 창 등장)**: 이번 예시처럼 `main`도 `Create ai.py`를 했고, `feat/ui`도 `Update ui.py`를 해서 **양쪽 다 각자 일한 경우**, 두 줄기를 하나로 묶는 **새로운 '병합 커밋'**이 필요합니다.
   - 이때 Git이 *"Merge branch 'feat/ui'라는 제목으로 병합 커밋을 남길 건데 괜찮지?"* 하고 에디터(`MERGE_MSG`)를 열어 확인을 요청합니다.
   - **해결법**: 메시지를 바꿀 필요가 없다면 **열린 `MERGE_MSG` 탭을 그냥 닫기(`X` 또는 `Ctrl+W`)만 하면** 즉시 병합이 완료됩니다!

---

### 📍 3단계: 충돌(Conflict)과 해결 (`left` + `right` → `Fix conflict`)

병합된 `4561b24` 이후, `left`와 `right`가 **같은 파일(`hello.py`)의 같은 부분**을 서로 다르게 수정했습니다.
이 상태에서 병합을 시도하면 Git은 *"누구 코드를 써야 할지 내가 임의로 결정할 수 없어!"* 하며 **충돌(Conflict)**을 알리고 멈춥니다.

#### 충돌이 났을 때 파일 안의 모습
```python
<<<<<<< HEAD
# main(left)에서 수정한 내용
=======
# right 브랜치에서 수정한 내용
>>>>>>> right
```

#### 충돌 해결 3단계
1. **직접 선택하기**: 파일에서 `<<<<<<<`, `=======`, `>>>>>>>` 표시를 지우고, 남기고 싶은 최종 코드로 정리합니다.
2. **해결 완료 알리기**: `git add hello.py`
3. **병합 마무리하기**: `git commit -m "Fix conflict"` (또는 `git commit` 입력 후 창 닫기)

---

## 3. 초보자가 가장 자주 만나는 에러 & 해결법

실습하다 아래 메시지를 만나더라도 당황하지 마세요! Git이 내 코드를 지켜주려고 띄우는 친절한 안내입니다.

### ❌ 상황 1: `error: The following untracked working tree files would be overwritten by checkout`
- **왜 뜰까요?**
  현재 브랜치에 아직 커밋하지 않은 새 파일(예: `ui.py`)이 있는데, 이동하려는 브랜치에도 똑같은 이름의 파일이 이미 존재할 때 발생합니다. 그대로 이동하면 내 작업이 덮어씌워져 날아가므로 Git이 막아주는 것입니다.
- **해결 방법 (내 작업을 임시 보관하고 이동하기)**
  ```bash
  git stash -u            # 작성 중인 파일(새 파일 포함)을 임시 서랍에 보관
  git switch feat/ui      # 원하는 브랜치로 안전하게 이동
  git stash pop           # 서랍에서 아까 작업하던 내용 다시 꺼내기
  ```

### ❌ 상황 2: `error: remote origin already exists.`
- **왜 뜰까요?**
  `git remote add origin <새주소>`를 입력했는데, 이미 `origin`이라는 별명으로 다른 GitHub 주소가 등록되어 있기 때문입니다.
- **해결 방법**
  ```bash
  git remote -v                             # 현재 연결된 주소 확인하기
  git remote set-url origin <새로운_주소>    # 기존 origin의 주소만 새 주소로 교체하기
  ```

### ❌ 상황 3: 방금 한 커밋을 되돌리고 싶을 때
- **수정한 코드는 남기고 커밋만 취소하고 싶다면** (가장 추천):
  ```bash
  git reset HEAD~1
  ```
- **수정한 코드까지 아예 이전 커밋 상태로 깨끗이 버리고 싶다면** (주의!):
  ```bash
  git reset --hard HEAD~1
  ```

---

## 4. 꼭 기억해야 할 한 가지 습관

> **⚠️ 터미널을 여러 개 띄워도 현재 브랜치(`HEAD`)는 폴더 전체에 딱 하나만 존재합니다.**
>
> VS Code에서 터미널 창을 3개로 분할해 한쪽은 `(main)`, 한쪽은 `(feat/ui)`처럼 띄워두더라도, **어느 한 창에서 `git switch`를 실행하면 폴더 전체의 브랜치가 바뀝니다.**
> 다른 창 화면에 옛날 브랜치 이름이 글자로 남아 있어도 실제로는 방금 바꾼 브랜치 상태이므로, 가급적 **터미널 1개**에서 `git status`나 `git branch`로 현재 위치를 확인하며 작업하는 것이 가장 안전합니다.

---

## 5. 한눈에 보는 상황별 치트시트

| 지금 하고 싶은 일 | 입력할 명령어 |
| :--- | :--- |
| 현재 상태(브랜치, 수정된 파일) 확인 | `git status` |
| 브랜치 목록 및 현재 위치(`*`) 확인 | `git branch` |
| 커밋 그래프 한눈에 보기 | `git log --all --graph --oneline` |
| 새 브랜치 만들고 바로 이동 | `git switch -c <브랜치명>` |
| 작업 파일 담기 & 커밋하기 | `git add .` → `git commit -m "메시지"` |
| 다른 브랜치 내용 현재 브랜치로 합치기 | `git merge <합칠_브랜치명>` |
| 합치기 끝난 브랜치 깔끔하게 삭제 | `git branch -d <브랜치명>` |
| 연결된 GitHub(원격 저장소)에 업로드 | `git push origin main` |
