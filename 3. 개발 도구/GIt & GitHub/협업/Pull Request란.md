# 사용 이유
여러분이 새로운 회사에 합류했다고 가정해 봅시다. 이미 엄청난 양의 코드가 존재하는 프로젝트에 첫발을 내딛게 되었습니다. 아무래도 처음이다 보니, 작성한 코드가 완벽하게 동작할지 확신하기 어려운데요. 과연 내가 작성한 코드에 버그는 없을까요? 운이 좋다면 한 번에 잘 작동할 수도 있겠지만, 대부분의 경우에는 실수가 있을 겁니다. 그렇다고 바쁜 선배 개발자에게 매번 직접 확인을 부탁하는 것도 어려울 텐데요. 이때 바로 Pull Request 기능을 사용하면 됩니다.

Pull Request란, 다른 GitHub 사용자들에게 자신의 작업한 내용을 검토하고 머지해 달라고 요청하는 GitHub의 기능입니다. 줄여서 PR이라고도 하는데요. PR을 활용하면 다른 개발자들에게 여유가 될 때, 여러분의 코드를 검토해 달라고 피드백을 요청할 수 있습니다. 특정 시간에 맞추지 않아도 되고, 서로의 의견을 자유롭게 교환할 수 있으며, 더 효율적이고 품질 높은 코드를 작성할 수 있습니다.

# 개인/팀 프로젝트에서 Pull Request를 사용한다면?

## 개인 프로젝트

개인 프로젝트에서도 PR을 활용하면 자신이 작성한 코드를 체계적으로 검토하고 문서화하는데 큰 도움이 됩니다. PR을 통해 코드의 변경 사항을 재검토하면서 오류를 발견하는 일은 흔합니다. PR은 꼭 문서로 코드를 설명하지 않더라도 PR 자체가 의미를 가지는 코드의 단위입니다. PR 자체가 코드를 문서화하는 것이죠. 즉 변경 점의 최소 단위인 commit 단위의 기록이, feature 단위의 기록이 PR로 남기 때문에 일종의 기록, 혹은 자동화된 documentation의 기능을 수행하는 것입니다.

또한 PR을 만드는 것은 복구 가능한 시점을 만드는 것이기도 해서, 초심자분들에게 큰 도움을 줄 수 있습니다. PR을 사용하지 않고 머지하게 될 경우 복구가 어려운 반면, PR을 사용하면 대부분의 기록이 remote에 남게 되고, 실수한 경우 쉽게 되돌릴 수 있습니다.

모든 변경 사항을 PR로 관리하려면 처음에는 시간이 많이 소요될 수 있고, 특히 처음에는 익숙하지 않아 실수를 할 수도 있습니다. 그러나 PR을 통한 작업 관리에 익숙해진다면, 이로 인한 시간 소모는 점차 줄어들게 될 것이며, 향후에 팀 프로젝트를 진행할 때 큰 도움이 될 것입니다.

## 팀 프로젝트에서 사용할 때

팀 프로젝트에서는 PR의 장점이 더욱 두드러집니다. 모든 팀원이 코드 리뷰를 통해 다른 사람이 작성한 코드를 확인하면서 지식을 공유하고 코드의 품질을 개선할 수 있습니다.

더불어 PR은 프로젝트의 투명성을 높여 주고, 작성자의 의도를 쉽게 파악할 수 있게 해 줍니다. 코드의 작성 배경, 목적 그리고 주요 내용을 문서화해 두면, 다른 팀원들의 관련 작업이 쉬워지는 것이죠. 혜택을 받는 대상에는 다른 팀원뿐만 아니라 작성자 본인도 포함됩니다. 왜 이런 코드를 작성했는지 잊어버린 미래의 본인 말이죠.

PR을 통해 문서화하는 일은 수고가 들어가는 일이지만, 그만한 가치가 있는 일입니다. PR을 통해 프로젝트의 참여하는 개개인이 절약하는 시간을 모두 합쳐 본다면 상당히 큰 부분일 것이기 때문이죠.

# Pull Request를 직접 만들기
1. 작업할 프로젝트 생성하기
    
    - 먼저 예습을 진행할 프로젝트가 필요하겠죠? GitHub로 들어가 repository를 하나 만들어 보도록 하겠습니다. GitHub 우측 상단의 + 버튼을 통해 새로운 repository를 생성할 수 있습니다. 다양한 옵션들이 있지만 오늘의 예제에서는 중요하지 않기 때문에 `README.md` 파일만 체크를 하고 생성해 보도록 합시다. 이 파일을 체크하면 `README.md`가 자동으로 생성됩니다.
        
        ![z2dvg042i-image.png](https://bakey-api.codeit.kr/api/files/resource?root=static&seqId=6646&version=&directory=z2dvg042i-image.png&name=z2dvg042i-image.png)
        
2. 프로젝트 clone하기
    
    `git clone https://github.com/<username>/<repository-name>.git`
    
    - 아래처럼 GitHub repository에서 녹색 버튼을 누르면 clone할 수 있는 주소가 나옵니다.
        
        ![p22rsp7c0-image.png](https://bakey-api.codeit.kr/api/files/resource?root=static&seqId=6646&version=&directory=p22rsp7c0-image.png&name=p22rsp7c0-image.png)
        
    
    `cd my-test-repository`
    
    편의상 앞으로는 repository로의 이동은 생략하겠습니다.
    
3. 새로운 브랜치 생성하기
    
    - 자 이제 `checkout` 명령어를 사용해 새로운 브랜치를 만들어보죠
        
        `git checkout -b my-first-branch`
        
4. 코드 변경하기
    
    - 간단하게 `README.md`를 수정해 봅니다.
        
        `md`는 마크다운(markdown)을 의미하는 확장자(extension)입니다. 일반적인 텍스트 편집기 (메모장, VS Code 등)로 켜서 아래와 같이 수정 후 저장할 수 있습니다.
        
        `# my-test-repository  이건 테스트입니다.`
        
    - 변경 사항이 발생했으니 커밋을 해야겠죠?
        
        `git add -A git commit -m "this is my first branch commit"`
        
5. 변경 사항 푸시하기
    
    - 변경 사항을 푸시해 봅시다!
        
        `git push origin`
        
    - 그럼 다음과 같은 출력 결과가 보일 것입니다.
        
        `Enumerating objects: 5, done. Counting objects: 100% (5/5), done. Writing objects: 100% (3/3), 305 bytes | 305.00 KiB/s, done. Total 3 (delta 0), reused 0 (delta 0), pack-reused 0 remote:  remote: Create a pull request for 'my-first-branch' on GitHub by visiting: remote:      https://github.com/choiking10/my-test-repository/pull/new/my-first-branch remote:  To github.com:choiking10/my-test-repository.git  * [new branch]      my-first-branch -> my-first-branch`
        
    - 해당 출력 결과에는 `https://github.com/...` 로 시작하는 주소가 포함되어 있습니다. 이 주소를 클릭해 볼까요?
        
6. PR 생성하기
    - 여러분들의 첫 번째 PR 화면에 당도했습니다.
        
        ![uaen77u3l-image.png](https://bakey-api.codeit.kr/api/files/resource?root=static&seqId=6646&version=&directory=uaen77u3l-image.png&name=uaen77u3l-image.png)
        
    - 여기서 Create pull request를 클릭한다면 PR이 생성됩니다. 조금 심심하니 제목과 내용에 원하는 문구들을 추가하고 Create pull request를 눌러주세요.
        
        ![d4t5rhs9e-image.png](https://bakey-api.codeit.kr/api/files/resource?root=static&seqId=6646&version=&directory=d4t5rhs9e-image.png&name=d4t5rhs9e-image.png)

# Pull Request의 상태
PR은 크게 3가지 상태를 가질 수 있습니다.
## **Open**

Open 상태의 PR은 아직 검토가 완료되지 않았거나, 추가적인 작업이 필요한 상태를 말합니다. 이 상태에서는 더 많은 커밋을 추가하거나, 토론을 진행하며 수정을 요청할 수 있습니다. 해당 상태의 PR은 아래의 녹색 아이콘으로 표시됩니다.

![c5occ3d97-image.png](https://bakey-api.codeit.kr/api/files/resource?root=static&seqId=6646&version=&directory=c5occ3d97-image.png&name=c5occ3d97-image.png)

## **Merged**

Merged 상태의 PR은 소스 코드의 기본 브랜치로 병합된 상태를 말합니다. Pull Request 화면에서 Merge 버튼을 누르게 되면 해당 상태로 변경되게 됩니다. 이는 해당 PR의 변경 사항이 승인되었고, 코드에 통합되었다는 것을 의미합니다. PR이 병합된 후에는 Closed 상태로 표시되며, 이후 추가적인 변경이나 커밋을 추가할 수 없습니다. Merged 상태의 PR은 아래와 같은 아이콘으로 표시됩니다.

![](https://bakey-api.codeit.kr/api/files/resource?root=static&seqId=6646&version=&directory=bjpj4g2lh-image.png&name=bjpj4g2lh-image.png)

## **Closed**

Closed 상태의 PR은 거부되었거나, 더 이상 유효하지 않은 PR을 말합니다. 이 PR은 병합되지 않았지만 더 이상 필요하지 않거나 다른 이유로 인해 종료된 상태입니다. UI에서 Closed 버튼을 눌렀을 경우 PR은 Closed 상태가 됩니다. Closed 상태로 전환된 후에도 브랜치가 존재한다면 다시 Open 상태로 되돌릴 수 있습니다. Merged 상태도 Closed 상태로 취급하기 때문에 Closed 탭에는 Merged와 Closed 상태의 모든 PR을 확인할 수 있습니다. 해당 상태의 PR은 아래와 같은 아이콘으로 표시됩니다.

![](https://bakey-api.codeit.kr/api/files/resource?root=static&seqId=6646&version=&directory=wuxrpwzjx-image.png&name=wuxrpwzjx-image.png)

Closed와 Merged는 결국 Closed 상태이기 때문에 Closed에서 모두 확인할 수 있습니다.

![vqdfb4usc-image.png](https://bakey-api.codeit.kr/api/files/resource?root=static&seqId=6646&version=&directory=vqdfb4usc-image.png&name=vqdfb4usc-image.png)
# 정리
`git pull` → 브랜치 생성 (`git checkout -b`) → 변경 사항 커밋 (`git add` & `git commit`) → `git push` → PR 생성