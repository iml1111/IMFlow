# IMFlow
본 페이지는 **Github를 이용한 프로젝트 진행 및 어플리케이션 코드 형상 관리에 대한 전반적인 협업 방침**에 대해서 다룹니다.

이 글을 읽기 전에 유의해야 할 점은 나름 꽤 많이 고민을 했습니다만 **절대 정답은 아니며, 많은 걸 간과하고 있을 수 있다는 점을 명심**해야 합니다.

**그런 의문점이 보인다면 주저하지 말고 댓글로 부탁드립니다!**

이 글을 읽기 전에 아래에 기본적인 사전 지식이 필요하므로 참고 부탁드립니다.

- Git, Github에 대한 기본적인 개념, 사용법
- Git Flow를 비롯한 브랜치 설계 개념의 겉핥기 지식



# 개요

보통 개발을 진행할 떄에흔히 퍼져있는 협업 관례(Git flow라던지) 등을 가져와 프로젝트를 진행하게 되었는데, 막연하게 풀어가다 보면 여러가지 아쉬운 점들이 존재했습니다.

- Github에서 제공되는 여러가지 유틸 기능의 활용도가 그다지 높지 않음.
- 프로젝트 내에서 여러 repo가 나눠질 경우, 각기 다른 Label, PR, Merge, Commit 메시지를 사용하고 있어 일관성이 떨어지게 됨.
- 기존에 널리 알려진 Git flow가 Github의 권장하는 방식과 매치가 잘되지 않아 여러 문제점이 나타남. (이 부분은 밑에서)

## 방법론의 범주

어디까지 협업에 제약을 걸어야 할까 고민이 들었습니다. 제약을 걸면 걸수록 밖에서 지켜보기에는 깔끔해보이지만 안에서 그걸 지키면서 움직이는 팀원들은 죽어나갈테니..

개발자답게 최대한 실리를 가져가게 짜도록 했지만, 그럼에도 번거로운 것들이 곳곳에 존재할 것입니다. 하지만 그게 여러 사람과 협업할 때는 오히려 더 효율적이라 생각하면서 정리해보았습니다. IMFlow 방법론으로 통제되는 범주는 다음과 같습니다.

- **Branch**
- **Pull Request (이하 PR로 생략)**
- **Issue**
- **Commit**

(그 외의 Github에서 제공되는 기능인 **Action, Project, Wiki**는 제외합니다. 강제하지 않는 것일 뿐, 당연히 써보면 좋은 기능들입니다)

어플리케이션이 개발됨에 따라 진행되는 프로세스에서 위 4가지에 대한 생성/수정/삭제에 대한 규칙을 제약합니다. 제약되는 룰에는 반드시 지켜야 하는 것도 있고, 알아서 선택할 수 있거나 확장시킬 수 있습니다.



## Git flow의 문제점

IMFlow 또한 Git-flow의 큰 틀을 벗어나지는 않지만, Git-flow으로 진행하면서 몇가지 문제점이 착안되었고 이를 개선하기 위해 약간 변형시킨 구조라고 보시면 됩니다. Git-flow에는 다음과 같은 문제점들이 있었습니다.

[Git flow의 개념, 그리고 문제점](https://github.com/iml1111/IMFlow/blob/develop/git-flow.md)



# IMFlow에 대하여

Git-flow를 보고 IMFlow 전략을 세우면서 개선하고자 하는 점은 다음과 같습니다.

- 영구 브랜치인 `develop`과 `main`은 가능한 한 커밋 히스토리가 동기화되어 있는게 좋다.

  - 그래야 관리가 쉽다.
  - 물론 여기서의 동기화란, `develop → main`을 의미한다. `main → develop`은 지켜지지 않아도 무관하다.

- develop 브랜치에 들어왔다는 것은 테스트가 완료되고 언제든 배포해도 된다는 걸 의미

  한다.

  - 따라서, 통상적인 배포 절차는 `develop -> main` Merge 과정을 통해 수행한다.

- **가능한한 2중 머지, 지속적인 머지 과정을 프로세스에서 제거하자.**

- **Git-flow에서 정해주지 않은 애매한 것들을 확실하게 정의하자.**

  

## Branch

IMFlow의 전반적인 Branch 구조는 다음과 같습니다.

![image](https://user-images.githubusercontent.com/29897277/182814471-8883b7c4-2dbd-4028-bcc1-96520c6363c5.png)

> **브랜치 상속, 머지 제약**

모든 브랜치의 생성 및 병합(Merge) 과정에 대하여 위의 그림에서 이루어지는 것 만을 허용합니다. **일반 머지 루트**란, 개발 프로세스에서 통상적으로 사용되는 머지 방식을 뜻합니다.

**예외 머지 루트**란, 특정 이슈가 발생했을 때 문제를 해소하기 위한 예외적인 머지 방식입니다.

> **각각의 사례에 대하여 어떤 Merge Commit을 해야 하나?**

`develop`, `main` 브랜치(영구 브랜치)를 제외한 모든 브랜치는 **임시 브랜치**로, 이는 **언제든지 삭제될 수 있음**을 의미합니다.

**영구 브랜치(develop → main)** 간의 Merge는 **Merge Pull request(일반 머지**)로 제약합니다.

**임시 브랜치 → 영구 브랜치**로의 Merge는 **Squash Merge(스쿼시)**로 제약합니다.



### main

**Stable 한 코드의 Archive**이며 main 브랜치로 배포하면 **언제든지 stable 한 상태의 코드가 배포**될 수 있습니다.

통상적으로 `main` 브랜치는 `develop` 브랜치의 모든 커밋을 Merge를 통해 가지고 있습니다. 

**대부분의 업데이트(PR)는 `develop`을 통해서** 진행합니다. **단, 예외적인 상황에 대하여 `hotfix`, `release` 브랜치에 대한 PR로 업데이트**가 이루어지기도 하는데, 이는 아래에서 다룹니다.

> ✅ **반드시 `develop → main`의 형태로 브랜치간의 동기화가 가능해야 한다.**



### develop

<img src="https://user-images.githubusercontent.com/29897277/182815055-deb23954-ae4d-4e41-9e7a-a32876e0f462.png" width="300">

Deploy-ready 상태의 코드가 있는 브랜치입니다.

 develop 브랜치에 들어왔다는 것은 **테스트가 완료되고 언제든 배포해도 된다는 의미**입니다.

기존 github는 Repo 생성시 main 브랜치가 default branch가 되지만, IMFlow에서는 **develop 브랜치를 default branch로 지정**한다.

develop 브랜치에 대한 **대부분의 업데이트(PR)은 Feature branches를 통해** 진행된다. **단, 여기도 예외적인 상황에 대하여 `hotfix`, `release` 브랜치에서의 PR로 업데이트**될 수 있다.



### Feature Branches

<img src="https://user-images.githubusercontent.com/29897277/182815297-06ac15d9-d407-41bd-a9ca-d4b54bb9246e.png" width="400">

피처 브랜치는 실질적인 개발 업무를 분담하기 위한 브랜치들의 집합입니다. Git-Flow는 막연히 복수의 피처 브랜치들이 develop 브랜치에 붙어 있는 형태로 주어져 있지만, 여기서 IMFlow는 각 역할에 대하여 명확하게 구분할 수 있도록 다음과 같이 브랜치 이름 규칙을 강제합니다.

```jsx
<feature-type>-<issue-id>
# feature-type: 해당 브랜치의 역할
# issue-id: 해당 브랜치와 연관된 이슈 아이디

Ex) feat-1, fix-3, chore-32,13, ...
```

> **feature-type**

피처 타입은 해당 브랜치에서 수행되는 태스크로써, 우선적으로 아래와 같이 정의하였습니다.

- **feat**: 기능 개발, 수정 및 삭제
- **fix**: 기능의 문제 수정
- **refac**: 리팩토링
- **doc**: 문서화
- **chore**: 의존성 패키지, assets 세팅 등 여러 기타 처리

하나의 브랜치가 복수의 태스크 및 역할을 담당할 수 있습니다. 이 경우, **가장 주가 되는 태스크를 개발자가 판단하여 하나의 피처 타입에 기입**하도록 합니다.

**모든 피처 브랜치는 최소 하나 이상의 이슈를 브랜치 명에 포함**하고 합니다. 즉, 모든 태스크는 브랜치 생성 이전에 이슈가 우선적으로 공유가 되어야 합니다.

모든 피처 브랜치의 **Merge는 Squash Merge을 한 후, 해당 브랜치는 반드시 삭제**해야 합니다. 그 외에는 기존의 Git-flow와 일치합니다.

> **하나의 feature 브랜치에 여러 명이 협업한다면?**

<img src="https://user-images.githubusercontent.com/29897277/182815468-79d03d0f-6c7f-4380-bc45-922417ba1e98.png" width="400">

부모의 feature 브랜치의 이름을 계승하고 추가적으로 덧붙이는 형태로 브랜치를 생성합니다.

이때 생성되는 하위 feature 브랜치 또한 임시 브랜치이기 때문에 **Squash Commit**은 동일합니다.



### hotfix

<img src="https://user-images.githubusercontent.com/29897277/182815647-245e6255-fa08-49df-b117-eb41ebc8c38d.png" width="400">

hotfix 브랜치는 현재 배포되어 있는 main 브랜치에서 예상치 못한 문제가 발생한 경우, 긴급하게 수정하여 배포할 수 있도록 하는 shortcut 브랜치이다. base 브랜치는 main이며, 브랜치 이름 규칙은 아래와 같습니다.

```jsx
hotfix-<issue-id>
# issue-id: 해당 브랜치와 연관된 이슈 아이디

Ex) hotfix-12
```

수정사항 반영이 완료된 경우, `main` , `develop` 브랜치에 각각 Squash Merge를 수행합니다. **가급적이면 2중 머지를 억제하고 싶지만, 이것만큼은...** 해당 과정 후 발생되는 develop 및 main 브랜치간의 불일치는 `develop -> main` Merge를 통해 재동기화해야 합니다.



### Stage Branches (release-*)

스테이지 브랜치는  `main` 브랜치를 제외하고 개발된 어플리케이션을 배포하기 위한 브랜치들의 집합입니다.

스테이지 브랜치는 Git-flow에서 상대적으로 꽤 많은 부분이 변화하였습니다.

가장 먼저, Stable 버전(main)으로 배포시 반드시 release 브랜치를 거쳐야하는 부분을 제거하였습니다. **기존 release 브랜치에서 수행하던 bugfix 및 수정 작업은  `fix -> develop` 방식으로 대체**합니다.

> **즉, 스테이지 브랜치는 일반적인 배포 과정에서 필수가 아닙니다.**

release 브랜치 이름을 작성하는 규칙은 다음과 같으며, [Symentic Versioning](https://semver.org/lang/ko/)을 따릅니다.

```jsx
release-<major>.<minor>.<patch>|-<pre-release>|+<build>

Ex)
release-1.0.3-alpha
release-3.0.0
```

스테이지 브랜치는 특정한 상황에서만 사용되는 브랜치 집합으로 아래의 2가지로 제한합니다.

> **1.** **최신 버전외에도** **다양한 하위 버전의 호환성을 유지해야 할 때**

**간단하게 말하자면 release 브랜치들을 통해 복수의 main 브랜치를 가집니다.**

![image](https://user-images.githubusercontent.com/29897277/182815736-5c59a6ad-f571-4316-9fd5-115e58feabbc.png)

어플리케이션의 특성상 Stable latest 버전 외에도, 하위 버전의 호환성을 유지하고 있어야 하는 경우가 있을 수 있습니다. 이때 **계속해서 업데이트되는 develop에서 해당 코드를 격리시키기 위해** **별도의 release 브랜치**를 사용하여 격리시킵니다. **이를 기점으로 해당 `release` 브랜치는 `main` 브랜치와 완전히 독립적으로 관리된다.**

이때 각각의 `release` 브랜치는 `hotfix`를 통해, 하위 버전의 호환성 개선만을 수행하며 새로운 기능은 업데이트되지 않도록 합닏.  (**해당 이슈가 현재의 `develop`에도 적용된다는 가정하에 2중 머지도 허용**)

해당 버전의 호환성 유지가 중단된다면, 해당 브랜치를 삭제합니다.

> **2.** **Stable 버전(main) 배포 전, 특정 분기에 대하여 릴리즈 & QA를 진행할 때**

**실 서비스 배포 이전의 스테이징 & QA 과정을 수행하는 브랜치**입니다.

**해당 브랜치는 반드시 사용하지 않아도 됩니다. (Be Optional !!!)**

![image](https://user-images.githubusercontent.com/29897277/182815812-76b9d7dc-3fb5-4f0b-9b52-ec57dec5ee3a.png)

릴리즈 브랜치는 반드시 분기를 시키지 않아도 됩니다. 통상적인 **스테이징 & QA 과정은 각각의 `feature` 브랜치 및 `develop` 브랜치에서도 자유롭게 수행할 수 있기 때문**입니다.

그럼에도 별도의 release 브랜치를 사용해야 하는 경우는 **현재 검증을 해야 하는 부분이 `develop` 브랜치 내에서 수시로 영향을 받고 급변하고 있는 특수한 경**우입니다.

이 경우 해당 코드를 일시적으로 `develop` 에서 격리시키기 위해 `release` 브랜치를 생성합니다. 그 후, 전반적인 QA를 통해 수정 작업을 거친 뒤 다시 `develop`으로 병합시켜 QA 프로세스를 완료합니다.

이 경우, `develop` 브랜치 입장에서는 다른 통상적인 `feature` 브랜치와 비슷한 절차를 가진다고 볼 수 있습니다.

단, 여기서 **예외적으로 해당 release 브랜치의 변경사항을 곧바로 main 브랜치로 병합시켜야 하는 경우가 있습니다**. 바로 `develop`이 빠르게 업데이트 되어 기존 `release` 와의 차이가 벌어져 병합이 불가능하거나/의미가 없는 경우입니다.

**이러한 특수한 경우에 한하여, `develop -> release -> main`의 방식을 허용**합니다. 단, `hotfix`와 마찬가지로 **추후에 `develop`과 `main`의 동기화를 별도로 수행**해주어야 합니다.

> **✅ `main` 브랜치의 업데이트는 가능한 한 `develop` 브랜치에게 일임합니다.**



## Pull Request

PR는 크게 2종류 취급한다.

- **다른 브랜치의 코드를 병합시키기 위한 PR(for Merge)**
- **코드리뷰를 위한 PR(for review)**

로 나뉘어 진다.



### PR for Merge

모든 Merge는 반드시 PR 및 코드리뷰(가능하다면)를 통해 이루어져야 합니다.

PR이 가능한 경우를 정리하자면 다음과 같습니다.

**일반 머지 루트**

- `develop → main` (Merge)
- `feature → develop` (Squash)
- `release → develop` (Squash)
- `hotfix → release` (Sqaush)

**예외 머지 루트**

- `release → main` (Sqaush)
- `hotfix → develop` (Sqaush)
- `hotfix → main` (Sqaush)

**PR의 이름**과 PR로 인해 **생성된 모든 Merge Commit의 이름**을 동일하게 맞추도록 합시다.

PR 명을 정하는 규칙은 다음과 같습니다.

```jsx
# 제목
[<branch-name>] PR에 대한 간략 한글설명 (#<PR-id>)

# 최소 생성시 코멘트 최하단에 추가 
- close #<issue-id>

Ex)
[feat-3] 댓글 기능 수정
- close #3

[refac-23] 메시지큐 로직 최적화
- close #23
```

`close #<issue-id>`를 이용하여 해당 PR과 동시에 이슈를 종료할 수 있습니다. 제목, 내용 어디에도 써도 되지만 이미 브랜치 이름에서 중복되기에 내부 내용에 기입해주도록 합시다.

브랜치 이름을 PR 제목에 넣음으로써 해당 **PR의 주요 변경점과 관련된 Issue-id를 우선적으로 확인**할 수 있습니다. 단, 해당 브랜치가 복수의 피처를 담당하게 될 경우, Label을 통하여 보다 상세한 명세를 기입합니다.



### Label for PR

- **Feat** - 기능 개발, 수정 및 삭제를 했다!
- **BugFix** - 버그 및 기능의 문제를 해결했다!
- **Refac** - 리팩토링을 했다!
- **Doc** - 문서화를 했다!
- **Chore** - 기타 잡일을 했다!
- **Review** - 해당 PR은 리뷰를 위해 개설했다! **(PR for Review)**

이외에 **목적이** **겹치지 않는다는 가정하에 각 파트에 맞게 유동적으로 Label을 확장**시키도록 합시다!



## Issue

모든 피처 브랜치 및 hotfix는 발급된 `issue-id`를 사용하여 생성합니다. 즉, **브랜치를 생성하여 태스크를 할당하기 이전에, 해당 태스크에 대한 명세가 사전에 이슈로 기입**되어 있어야 합니다.

이슈 생성에 대하여 Assignees, Project와 같은 부분은 자유롭게 기입하되, Label의 경우 반드시 아래와 같은 형태로 사용하도록 해야 합니다.



### Label for Issue

- **BugFix** - 버그가 발생했다, 기능이 뭔가 이상하다!
- **Feat** - 기능 개발, 수정 및 삭제가 필요하다!
- **Refac** - 리팩토링이 필요해보인다!
- **Doc** - 문서화를 해야 할 것 같다!
- **Chore** - 그외 기타 잡일이 필요하다!
- **Pause** - 이 이슈는 일시적으로 보류한다!

이외에 **목적이** **겹치지 않는다는 가정하에 각 파트에 맞게 유동적으로 Label을 확장**시키도록 합니다!



## Commit

여기서의 커밋이란, PR로 생성된 모든 Merge Commit을 제외한 것을 말합니다.

- 어느 정도의 작업량을 Commit 단위로 끊어야 하나
- 얼마나 자세히 명세해야 하나
- 커밋 이름의 규칙은 어떻게 되나

는 각자가 알아서 하되, **가능하면 커밋 히스토리만으로 해당 작업의 흐름을 쉽게 파악할 수 있는 수준을 지향**하도록 합시다.



## References

[🐙 Github에서 협업하는 방법](https://velog.io/@cos/Github에서-협업하는-방법)

[[GitHub\] GitHub로 협업하는 방법[1] - Feature Branch Workflow - Heee's Development Blog](https://gmlwjd9405.github.io/2017/10/27/how-to-collaborate-on-GitHub-1.html)

[Git flow, GitHub flow, GitLab flow](https://ujuc.github.io/2015/12/16/git-flow-github-flow-gitlab-flow/)

[Git 브랜칭 전략 : Git-flow와 Github-flow](https://hellowoori.tistory.com/56)

[deploy 브랜치 전략 활용 방법](https://medium.com/daangn/deploy-브랜치-전략-활용-방법-545f278ca878)
