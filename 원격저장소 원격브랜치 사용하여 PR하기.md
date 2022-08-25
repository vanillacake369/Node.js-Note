# 원격저장소 원격브랜치 사용하여 PR하기(feat.Jira)

Created: 2022년 8월 11일 오후 3:28
Last Edited Time: 2022년 8월 24일 오전 11:44
State: Done
Tag: node.js

# Jira 용어 정리

---

- ****Step 1 - Create a project****
- ****Step 2 - Pick a template****
    
    우리 팀은 스크럼 방식의 템플릿을 택했다.
    

![https://wac-cdn.atlassian.com/dam/jcr:7391be03-5f54-47d0-b712-5a442fd80e2c/simple-scrum-icon.svg?cdnVersion=474](https://wac-cdn.atlassian.com/dam/jcr:7391be03-5f54-47d0-b712-5a442fd80e2c/simple-scrum-icon.svg?cdnVersion=474)

- **Step 4 - Create an issue**
    
    Issues are the building blocks of your Jira Software project. An issue can represent a [story, epic, bug, feature to be built](https://www.atlassian.com/agile/project-management/epics-stories-themes), or any other task in your project.
    
    Both Scrum teams and Kanban teams can add large pieces of work on their **Roadmap**. The Roadmap is a place to visualize, plan, and manage work. [Learn more about managing work on the roadmap](https://confluence.atlassian.com/jirasoftwarecloud/create-manage-and-visualize-your-epics-on-the-roadmap-1004952901.html).
    
- ****Step 5 - Invite your team****
- ****Step 6 - Move work forward****

# Jira 이슈 코드 사용하여 브랜치 생성하기

---

깃 레포의 브랜치를 사용하여 Jira로 프로젝트 관리하는 절차는 대충 아래와 같다.

> 브랜치 (지라 브랜치 만들기 코드) 새로만듬 > 수정하고 추가하는 작업 후 > git add . > 깃 커밋 (지라 code 명으로) > 풀 리퀘스트 > 김은민 깃 머지 > git pull 로 main 브랜치로 돌아오기
> 
1. 우선 첫 번째로 jira와 github을  서로 연동한다. 연동하는 방법은 구글링을 해보자.
2. 이제 Jira의 Issue Key기반으로 브랜치를 생성하자. 아래 이슈에서 보이는 `개발 > 브랜치 만들기`를 누르면 해당 Issue Key 기반으로 브랜치 생성 Git명령어를 보여준다.

![Untitled](%E1%84%8B%E1%85%AF%E1%86%AB%E1%84%80%E1%85%A7%E1%86%A8%E1%84%8C%E1%85%A5%E1%84%8C%E1%85%A1%E1%86%BC%E1%84%89%E1%85%A9%20%E1%84%8B%E1%85%AF%E1%86%AB%E1%84%80%E1%85%A7%E1%86%A8%E1%84%87%E1%85%B3%E1%84%85%E1%85%A2%E1%86%AB%E1%84%8E%E1%85%B5%20%E1%84%89%E1%85%A1%E1%84%8B%E1%85%AD%E1%86%BC%E1%84%92%E1%85%A1%E1%84%8B%E1%85%A7%20PR%E1%84%92%E1%85%A1%E1%84%80%E1%85%B5(feat%20%202553853303aa4258ab6f153a5c92cfbb/Untitled.png)

                                                                               ⬇️

![Untitled](%E1%84%8B%E1%85%AF%E1%86%AB%E1%84%80%E1%85%A7%E1%86%A8%E1%84%8C%E1%85%A5%E1%84%8C%E1%85%A1%E1%86%BC%E1%84%89%E1%85%A9%20%E1%84%8B%E1%85%AF%E1%86%AB%E1%84%80%E1%85%A7%E1%86%A8%E1%84%87%E1%85%B3%E1%84%85%E1%85%A2%E1%86%AB%E1%84%8E%E1%85%B5%20%E1%84%89%E1%85%A1%E1%84%8B%E1%85%AD%E1%86%BC%E1%84%92%E1%85%A1%E1%84%8B%E1%85%A7%20PR%E1%84%92%E1%85%A1%E1%84%80%E1%85%B5(feat%20%202553853303aa4258ab6f153a5c92cfbb/Untitled%201.png)

이를 사용하여 기존 로컬 저장소에서 branch를 생성하여 작업을 해준다. *다만 Jira 같은 경우 Fork를 해서 작업을 하게 되면 인식을 하지 못 하는 경우가 존재하므로, **기존 작업물 또한 Fork가 아닌 Branch & Pull을 통해 땡겨오도록 한다.*** 

1. 작업을 추가/수정한 뒤, add & commit을 해주도록 한다. 다만 commit 시, 커밋 메세지를 Jira 이슈 화면에서 `개발>커밋만들기` 작성하도록 한다. 이렇게 하면 커밋 관리를 효율적으로 할 수 있다나..
2. 커밋 이후 원격저장소로 설정해두었던 upstream에 따라 push를 진행한다. push를 진행하면 로컬 브랜치를 자동으로 원격 저장소를 생성해준다.
3. 이제 마지막으로 PR을 날려 원격 저장소의 팀장이 Merge를 해준다.

# Reference

---

1. [https://www.atlassian.com/software/jira/guides/getting-started/basics#step-6-move-work-forward](https://www.atlassian.com/software/jira/guides/getting-started/basics#step-6-move-work-forward)
2. [https://www.lesstif.com/jira/jira-cloud-github-125305615.html#:~:text=github 에 연결한 후에,새로운 브랜치가 생성됩니다](https://www.lesstif.com/jira/jira-cloud-github-125305615.html#:~:text=github%20%EC%97%90%20%EC%97%B0%EA%B2%B0%ED%95%9C%20%ED%9B%84%EC%97%90,%EC%83%88%EB%A1%9C%EC%9A%B4%20%EB%B8%8C%EB%9E%9C%EC%B9%98%EA%B0%80%20%EC%83%9D%EC%84%B1%EB%90%A9%EB%8B%88%EB%8B%A4).