# [Git] 오류 해결 error: unable to delete '브랜치명': remote ref does not exist

Created: 2022년 8월 22일 오후 4:34
Last Edited Time: 2022년 8월 23일 오후 3:22
State: Done
Tag: git
URL: https://velog.io/@wheezy_han/Git-%EC%98%A4%EB%A5%98-%ED%95%B4%EA%B2%B0-error-unable-to-delete-%EB%B8%8C%EB%9E%9C%EC%B9%98%EB%AA%85-remote-ref-does-not-exist

![%5BGit%5D%20%E1%84%8B%E1%85%A9%E1%84%85%E1%85%B2%20%E1%84%92%E1%85%A2%E1%84%80%E1%85%A7%E1%86%AF%20error%20unable%20to%20delete%20'%E1%84%87%E1%85%B3%E1%84%85%E1%85%A2%E1%86%AB%E1%84%8E%E1%85%B5%E1%84%86%E1%85%A7%207790c531b74a4f68aff8e3994fb01897/images2Fwheezy_han2Fpost2F2f9b8026-ed4d-4eb6-b42b-887cd069862a2Fimage.png](%5BGit%5D%20%E1%84%8B%E1%85%A9%E1%84%85%E1%85%B2%20%E1%84%92%E1%85%A2%E1%84%80%E1%85%A7%E1%86%AF%20error%20unable%20to%20delete%20'%E1%84%87%E1%85%B3%E1%84%85%E1%85%A2%E1%86%AB%E1%84%8E%E1%85%B5%E1%84%86%E1%85%A7%207790c531b74a4f68aff8e3994fb01897/images2Fwheezy_han2Fpost2F2f9b8026-ed4d-4eb6-b42b-887cd069862a2Fimage.png)

## Intro

gitlab으로 협업을 하다 보니까 git branch가 많이 쌓이게 되어 정리를 하였다.
 로컬 브랜치는 알아서 개인적으로 하면 되지만 remote는 별도로 관리하지 않으면 방치가 될 수 있다.

git remote branch를 정리하기 위해 아래와 같이 입력했다

```
git push origin --delete feature/authority

```

근데 오류가 났다😥😑

```
error: unable to delete '브랜치명': remote ref does not exist

```

![%5BGit%5D%20%E1%84%8B%E1%85%A9%E1%84%85%E1%85%B2%20%E1%84%92%E1%85%A2%E1%84%80%E1%85%A7%E1%86%AF%20error%20unable%20to%20delete%20'%E1%84%87%E1%85%B3%E1%84%85%E1%85%A2%E1%86%AB%E1%84%8E%E1%85%B5%E1%84%86%E1%85%A7%207790c531b74a4f68aff8e3994fb01897/images2Fwheezy_han2Fpost2F12f64680-a309-43fb-9247-1415faf496ac2Fimage.png](%5BGit%5D%20%E1%84%8B%E1%85%A9%E1%84%85%E1%85%B2%20%E1%84%92%E1%85%A2%E1%84%80%E1%85%A7%E1%86%AF%20error%20unable%20to%20delete%20'%E1%84%87%E1%85%B3%E1%84%85%E1%85%A2%E1%86%AB%E1%84%8E%E1%85%B5%E1%84%86%E1%85%A7%207790c531b74a4f68aff8e3994fb01897/images2Fwheezy_han2Fpost2F12f64680-a309-43fb-9247-1415faf496ac2Fimage.png)

해석해보면 원격서버에 삭제하려는 브랜치가 존재하지 않아 삭제할 수 없다는 오류이다.

분명 `git branch -a` 이나 `git branch -r`을 했을 때 remote branch가 있는 것을 확인 하였다.

## Solution

📍 이유는 `로컬에 기록되어있는 remote 서버 branch 정보`와 `실제 remote 서버 branch 정보`가 **일치하지 않아서**이다...!

로컬 pc의 remote 서버 branch 정보가 실제와 **동기화 된지 오래되었다면** 이 문제가 발생한다고 한다ㅜㅜ

이런 경우에는 🔑`fetch 명령어`🔑를 통해서 실제 remote 서버의 branch 정보를 가져와 로컬 pc에 갱신을 해주어야 한다.

```
git fetch -p origin
```

![%5BGit%5D%20%E1%84%8B%E1%85%A9%E1%84%85%E1%85%B2%20%E1%84%92%E1%85%A2%E1%84%80%E1%85%A7%E1%86%AF%20error%20unable%20to%20delete%20'%E1%84%87%E1%85%B3%E1%84%85%E1%85%A2%E1%86%AB%E1%84%8E%E1%85%B5%E1%84%86%E1%85%A7%207790c531b74a4f68aff8e3994fb01897/images2Fwheezy_han2Fpost2Fa3915571-6cea-40bf-8a02-9bca2673a9622Fimage.png](%5BGit%5D%20%E1%84%8B%E1%85%A9%E1%84%85%E1%85%B2%20%E1%84%92%E1%85%A2%E1%84%80%E1%85%A7%E1%86%AF%20error%20unable%20to%20delete%20'%E1%84%87%E1%85%B3%E1%84%85%E1%85%A2%E1%86%AB%E1%84%8E%E1%85%B5%E1%84%86%E1%85%A7%207790c531b74a4f68aff8e3994fb01897/images2Fwheezy_han2Fpost2Fa3915571-6cea-40bf-8a02-9bca2673a9622Fimage.png)

위의 명령어를 통해 동기화를 해준 후 다시 `git branch -r`을 해주면 삭제 실패했던 branch들이 사라진것을 볼 수 있을 것이다.

**주기적으로 branch를 관리**해주는 습관을 들이는것이 좋은 것 같다😋

### 참고

[https://ooz.co.kr/454](https://ooz.co.kr/454)