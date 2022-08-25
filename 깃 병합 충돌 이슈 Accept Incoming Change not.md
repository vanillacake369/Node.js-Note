# 깃 병합 충돌 이슈 : Accept Incoming Change not appearing in VS Code

Created: 2022년 8월 19일 오전 9:51
Last Edited Time: 2022년 8월 23일 오후 3:22
State: Done
Tag: git

Unlike here where the command palette is clearly visible at the top window.

![%E1%84%80%E1%85%B5%E1%86%BA%20%E1%84%87%E1%85%A7%E1%86%BC%E1%84%92%E1%85%A1%E1%86%B8%20%E1%84%8E%E1%85%AE%E1%86%BC%E1%84%83%E1%85%A9%E1%86%AF%20%E1%84%8B%E1%85%B5%E1%84%89%E1%85%B2%20Accept%20Incoming%20Change%20not%20%20bd940351a334489ebc41d99e297ee07f/NSs53.png](%E1%84%80%E1%85%B5%E1%86%BA%20%E1%84%87%E1%85%A7%E1%86%BC%E1%84%92%E1%85%A1%E1%86%B8%20%E1%84%8E%E1%85%AE%E1%86%BC%E1%84%83%E1%85%A9%E1%86%AF%20%E1%84%8B%E1%85%B5%E1%84%89%E1%85%B2%20Accept%20Incoming%20Change%20not%20%20bd940351a334489ebc41d99e297ee07f/NSs53.png)

You may not also be able to see "Accept Change(s)" because recently VS Code introduced **Git merge editor**, which is now enabled by default.

It allows you to view and resolve merge conflicts by clicking on a conflicting file in the Source Control view instead. You can turn it off to go back to "Accept Change(s)", though I think it's better.

Add this line to VS Code user settings.json to turn merge editor off: **"git.mergeEditor": false**

I'm attaching screenshots for anyone want to compare merge.editor off & on:[Git merge editor - off](https://i.stack.imgur.com/oFVvY.jpg)[Git merge editor - on](https://i.stack.imgur.com/GTjbR.jpg)

## NOTE:

If you like the new merge editor, you can bring it up from the terminal. Add the following to your .gitconfig, and then run - git mergetool

```
[merge]
    tool = vscode
[mergetool "vscode"]
    cmd = code --wait --merge $REMOTE $LOCAL $BASE $MERGED
[mergetool]
    keepBackup = false

```

![%E1%84%80%E1%85%B5%E1%86%BA%20%E1%84%87%E1%85%A7%E1%86%BC%E1%84%92%E1%85%A1%E1%86%B8%20%E1%84%8E%E1%85%AE%E1%86%BC%E1%84%83%E1%85%A9%E1%86%AF%20%E1%84%8B%E1%85%B5%E1%84%89%E1%85%B2%20Accept%20Incoming%20Change%20not%20%20bd940351a334489ebc41d99e297ee07f/5QGkf.gif](%E1%84%80%E1%85%B5%E1%86%BA%20%E1%84%87%E1%85%A7%E1%86%BC%E1%84%92%E1%85%A1%E1%86%B8%20%E1%84%8E%E1%85%AE%E1%86%BC%E1%84%83%E1%85%A9%E1%86%AF%20%E1%84%8B%E1%85%B5%E1%84%89%E1%85%B2%20Accept%20Incoming%20Change%20not%20%20bd940351a334489ebc41d99e297ee07f/5QGkf.gif)

![%E1%84%80%E1%85%B5%E1%86%BA%20%E1%84%87%E1%85%A7%E1%86%BC%E1%84%92%E1%85%A1%E1%86%B8%20%E1%84%8E%E1%85%AE%E1%86%BC%E1%84%83%E1%85%A9%E1%86%AF%20%E1%84%8B%E1%85%B5%E1%84%89%E1%85%B2%20Accept%20Incoming%20Change%20not%20%20bd940351a334489ebc41d99e297ee07f/trAHo.gif](%E1%84%80%E1%85%B5%E1%86%BA%20%E1%84%87%E1%85%A7%E1%86%BC%E1%84%92%E1%85%A1%E1%86%B8%20%E1%84%8E%E1%85%AE%E1%86%BC%E1%84%83%E1%85%A9%E1%86%AF%20%E1%84%8B%E1%85%B5%E1%84%89%E1%85%B2%20Accept%20Incoming%20Change%20not%20%20bd940351a334489ebc41d99e297ee07f/trAHo.gif)