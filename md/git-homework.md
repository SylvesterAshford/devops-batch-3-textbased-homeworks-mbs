# Git မှာ Merge, Branch, Time Travel နဲ့ Rebase

Git ကို စသုံးတုန်းက add, commit, push သုံးခုပဲ သိတယ်။ တစ်ယောက်တည်း လုပ်နေတုန်းက ဒါနဲ့တင် လုံလောက်တယ်ထင်တာ။ Team နဲ့ project စလုပ်ရတော့မှ မသိတာတွေ အများကြီး ပေါ်လာတယ်။ Conflict တက်တာတို့၊ commit မှားလုပ်မိလို့ ပြန်ဖြုတ်ချင်တာတို့၊ merge နဲ့ rebase ဘယ်ဟာသုံးမလဲ ဆိုတာတို့ပေါ့။ အဲဒီတုန်းက ရှာဖွေလေ့လာခဲ့ရတာတွေကို ပြန်စုရေးထားတာပါ။

## Merge strategy အမျိုးအစားတွေ ဘာကွာလဲ

Merge လုပ်တယ်ဆိုတာ branch တစ်ခုက ပြင်ထားတာတွေကို နောက် branch တစ်ခုထဲ ပေါင်းထည့်တာပဲ။ ဒါပေမယ့် ပေါင်းပုံ ပေါင်းနည်း မတူရင် နောက်ဆုံး code က အတူတူဖြစ်နေပေမယ့် history ပုံစံက တော်တော်ကွာသွားတတ်တယ်။ အဲဒါကို အစက သတိမထားမိခဲ့ဘူး။

ပထမဆုံး fast-forward ကနေ စပြောမယ်။ `main` ကနေ `feature` ခွဲထွက်ပြီးကတည်းက `main` ပေါ်မှာ ဘယ်သူမှ commit အသစ်မထည့်ရသေးဘူးဆိုရင် ဒီလိုပုံစံ ဖြစ်နေမယ်။

```
A---B             (main)
     \
      C---D       (feature)
```

`main` ပေါ်ကနေ `git merge feature` လုပ်လိုက်ရင် Git က ဘာမှ ပေါင်းစရာမလိုဘူး။ `main` ကို D ဆီ ရှေ့တိုးရွှေ့ပေးလိုက်ရုံပဲ။

```
A---B---C---D     (main, feature)
```

History က တစ်ကြောင်းတည်း သန့်သန့်လေး ဖြစ်သွားတယ်။ Feature branch ရှိခဲ့တယ်ဆိုတဲ့ အထောက်အထားကတော့ ပျောက်သွားတယ်။ Merge commit အမှတ်မထင် မဝင်လာစေချင်ရင် `git merge --ff-only feature` ကိုသုံးလို့ရတယ်။ Fast-forward မဖြစ်နိုင်ရင် ရပ်ပေးတယ်။

`main` ဘက်မှာလည်း commit အသစ်ဝင်လာပြီဆိုရင်တော့ ရွှေ့ရုံနဲ့ မရတော့ဘူး။

```
A---B---E         (main)
     \
      C---D       (feature)
```

ဒီအခါ Git က three-way merge လုပ်တယ်။ B (နှစ်ခု ခွဲထွက်ခဲ့တဲ့နေရာ)၊ E နဲ့ D သုံးခုကို ယှဉ်ကြည့်ပြီး parent နှစ်ခုပါတဲ့ merge commit M ကို အသစ်ဆောက်ပေးတယ်။

```
A---B---E---M     (main)
     \     /
      C---D       (feature)
```

M ရှိနေတော့ feature ကို ဘယ်တုန်းက ပေါင်းခဲ့လဲ နောက်မှ ပြန်ကြည့်လို့ရတယ်။ တစ်ခုခုပျက်သွားရင် M ကို revert လုပ်လိုက်ရုံနဲ့ feature တစ်ခုလုံး ပြန်ထွက်သွားတယ်။ Fast-forward ဖြစ်နိုင်တဲ့အခါမှာတောင် M ကို ချန်ထားချင်ရင် `--no-ff` ထည့်ပေးရတယ်။ Branch တွေ များလာရင်တော့ `git log --graph` က ကြည့်ရခက်လာတာ အမှန်ပဲ။ Git 2.34 ကစပြီး ဒီ three-way merge ကို `recursive` အစား `ort` ဆိုတဲ့ engine နဲ့ လုပ်တယ်။ ပိုမြန်ပြီး ဖိုင်နာမည်ပြောင်းထားတာတွေကိုလည်း ပိုကောင်းကောင်း ကိုင်နိုင်တယ်။

Feature ရေးနေရင်း "wip", "fix typo" လို commit အသေးလေးတွေ စုလာတာ ကိုယ်လည်း ခဏခဏ ဖြစ်တယ်။ အဲဒါတွေ `main` ထဲ မရောက်စေချင်ရင် squash merge သုံးတယ်။ `git merge --squash feature` လုပ်ပြီး `git commit` တစ်ခါ ပြန်လုပ်ပေးရတယ်။

```
A---B---E---S     (main)
     \
      C---D       (feature)
```

S ထဲမှာ C နဲ့ D ပြင်ထားတာ အကုန်ပါတယ်။ ဒါပေမယ့် S ရဲ့ parent က E တစ်ခုတည်းပဲ၊ `feature` နဲ့ ချိတ်မထားဘူး။ ဒါကြောင့် နောက်မှ `git branch -d feature` နဲ့ ဖျက်ရင် Git က မပေါင်းရသေးဘူးလို့ ထင်ပြီး warning ပေးတယ်။ `-D` နဲ့ ဖျက်ရတယ်။ ဒါကို ပထမဆုံးကြုံတုန်းက ဘာဖြစ်နေမှန်း နားမလည်ခဲ့ဘူး။

နောက်တစ်နည်းက rebase လုပ်ပြီးမှ merge တာ။ GitHub က "Rebase and merge" ခလုတ်က ဒီလိုလုပ်တာ။ Feature ရဲ့ commit တွေကို `main` ရဲ့ နောက်ဆုံး commit အပေါ်မှာ တစ်ခုချင်း ပြန်တင်ပြီးမှ fast-forward လုပ်တယ်။

```
A---B---E---C'---D'   (main, feature)
```

History လည်း တစ်ကြောင်းတည်း၊ commit တွေလည်း မပျောက်ဘူး။ C နဲ့ D က C' နဲ့ D' ဖြစ်သွားတာကိုတော့ သတိထားရမယ်။ Hash တွေ ပြောင်းသွားတာ။ အောက်က rebase အပိုင်းမှာ ဒါဘာလို့ အရေးကြီးလဲ ပြောထားတယ်။

နောက်ထပ် သိပ်မသုံးဖြစ်တဲ့ strategy တချို့လည်း ရှိသေးတယ်။ `git merge a b c` လို branch သုံးလေးခု တစ်ခါတည်းပေါင်းရင် octopus လို့ခေါ်တယ်၊ conflict မရှိမှ အလုပ်ဖြစ်တယ်။ `-s ours` က ပေါင်းပြီးပြီလို့ မှတ်ပေမယ့် တစ်ဖက် branch ရဲ့ code ကို လုံးဝမယူဘူး။ `-s subtree` ကတော့ project တစ်ခုကို folder တစ်ခုအနေနဲ့ ထည့်ချင်တဲ့အခါ သုံးတယ်။ `-s ours` နဲ့ `-X ours` ကို ကိုယ်လည်း အစက ရောခဲ့ဖူးတယ်။ `-X ours` က conflict တက်တဲ့နေရာမှာပဲ ကိုယ့်ဘက်ကိုယူပြီး ကျန်တာကို ပုံမှန်အတိုင်း ပေါင်းပေးတာ။ `-X theirs` ဆိုရင် conflict နေရာမှာ တစ်ဖက်ကို ယူတယ်။

## Git branching cheatsheet

Branch ဆိုတာ commit တစ်ခုကို ညွှန်ထားတဲ့ pointer လေးပဲ။ ဖိုင်တွေ copy ကူးထားတာ မဟုတ်ဘူး။ ဒါသိသွားမှ branch ခွဲရမှာ မနှမြောတော့ဘူး။ ကိုယ်အများဆုံးသုံးတာတွေကို စုထားတာ။

```bash
git branch -vv
git branch --merged
git switch -c feature-login
git switch -c hotfix a1b2c3d
git switch -
git branch -m new-name
git branch -d feature-login
git branch -D feature-login
git fetch --prune
git push -u origin feature-login
git push origin --delete feature-login
git log main..feature
git diff main...feature
git log --oneline --graph --all
```

`git branch -vv` က branch တစ်ခုချင်းစီ remote ထက် commit ဘယ်နှခု ရှေ့ရောက်နေလဲ နောက်ကျနေလဲ ပြပေးတယ်။ `--merged` ကတော့ ပေါင်းပြီးသား branch တွေ ပြပေးလို့ မဖျက်ခင် စစ်ဖို့ကောင်းတယ်။

Branch ဆောက်တာ ပြောင်းတာကို အရင်က `git checkout -b` နဲ့ လုပ်ကြတယ်။ အခုလည်း ရပါသေးတယ်။ Git 2.23 ကစပြီး `checkout` ကို `switch` နဲ့ `restore` ဆိုပြီး ခွဲပေးလိုက်တော့ branch အတွက်ဆို `switch` ကို သုံးတာ ပိုရှင်းတယ်။ `git switch -c` က ဆောက်ပြီး တန်းပြောင်းတယ်၊ နောက်မှာ commit hash ထည့်ရင် အဲဒီ commit ကနေ စဆောက်တယ်။ `git switch -` က ခုနက ရှိခဲ့တဲ့ branch ကို ပြန်သွားတယ်၊ branch နှစ်ခုကြား ကူးနေရရင် အရမ်းအဆင်ပြေတယ်။

ဖျက်တဲ့အခါ `-d` က ပေါင်းပြီးမှ ဖျက်ပေးလို့ စိတ်ချရတယ်။ `-D` က ဘာမှမစစ်ဘဲ ဖျက်တာ။

Remote နဲ့ပတ်သက်ရင် `fetch` နဲ့ `pull` မတူတာ သိထားဖို့လိုတယ်။ `fetch` က remote မှာ ဘာပြောင်းလဲ ယူလာရုံပဲ၊ ကိုယ့် branch ကို မထိဘူး။ `pull` က fetch ပြီးတာနဲ့ ကိုယ့် branch ထဲ တန်းပေါင်းတယ်။ `--prune` ထည့်ရင် remote မှာ ဖျက်ပြီးသား branch တွေကို ကိုယ့်စက်ထဲကပါ ရှင်းပေးတယ်။ Branch အသစ်ကို ပထမဆုံး push တဲ့အခါ `-u` ထည့်ထားရင် နောက်ပိုင်း `git push` ချည်းပဲ ရိုက်ရုံနဲ့ ရပြီ။

Branch နှစ်ခု ယှဉ်ကြည့်ချင်ရင် `git log main..feature` က feature မှာရှိပြီး main မှာ မရှိသေးတဲ့ commit တွေ ပြတယ်။ Code ကွာတာ ကြည့်ချင်ရင်တော့ `git diff main...feature` လို့ အစက်သုံးစက်နဲ့ သုံးတာ ပိုကောင်းတယ်။ Main ဘက်မှာ နောက်မှဝင်လာတာတွေ မပါဘဲ feature ဘက်က ပြင်ထားတာပဲ ပြလို့။

## Time travel cheatsheet

Git ရဲ့ အကောင်းဆုံးအချက်က အရင်ကို ပြန်သွားလို့ရတာပဲ။ ဒါပေမယ့် ပြန်သွားတယ်ဆိုတာ ကြည့်ရုံသွားတာလား၊ ဖိုင်တစ်ခုပဲ ပြန်ယူချင်တာလား၊ commit ကို တကယ်ဖြုတ်ချင်တာလား ဆိုတာပေါ် မူတည်ပြီး command တွေ မတူဘူး။

```bash
git log --oneline --graph --all
git log -p file.txt
git log -S "functionName"
git show a1b2c3d
git blame file.txt

git switch --detach a1b2c3d
git restore file.txt
git restore --staged file.txt
git restore --source=HEAD~2 file.txt

git commit --amend
git reset --soft HEAD~1
git reset HEAD~1
git reset --hard HEAD~1
git revert a1b2c3d
git revert -m 1 <merge-commit>
git reflog
git reset --hard HEAD@{1}
```

History ကြည့်ဖို့ `git log --oneline --graph --all` ကို အမြဲသုံးဖြစ်တယ်။ ဖိုင်တစ်ခုရဲ့ ပြောင်းလဲမှုတွေ ကြည့်ချင်ရင် `-p`၊ line တစ်ကြောင်းကို ဘယ်သူပြင်ခဲ့လဲ သိချင်ရင် `git blame`။ `git log -S` ကို နောက်မှ သိတာ၊ စာသားတစ်ခုကို ဘယ် commit က ထည့်ခဲ့လဲ ဖြုတ်ခဲ့လဲ ရှာပေးတယ်။ Function တစ်ခု ဘယ်အချိန်က ပျောက်သွားလဲ ရှာရတဲ့အခါ အသုံးဝင်တယ်။

Commit ကို ညွှန်းတဲ့အခါ hash ကို အမြဲ copy ကူးစရာမလိုဘူး။ `HEAD~1` က တစ်ခုအရင်၊ `HEAD~3` က သုံးခုအရင်။ Merge commit ရဲ့ ဒုတိယ parent ကို `HEAD^2` လို့ ခေါ်တယ်။ `main@{yesterday}` ဆိုပြီး အချိန်နဲ့တောင် ညွှန်းလို့ရတယ်။

အရင် commit တစ်ခုကို ကြည့်ရုံပဲဆိုရင် `git switch --detach` နဲ့ သွားလို့ရတယ်။ Git က "detached HEAD" လို့ ပြောလိမ့်မယ်။ ပထမဆုံးမြင်တုန်းက လန့်သွားသေးတယ်။ တကယ်တော့ HEAD က branch ကို မညွှန်ဘဲ commit ကို တိုက်ရိုက်ညွှန်နေတာပဲ။ `git switch -` နဲ့ ပြန်လာလို့ရတယ်။ အဲဒီမှာ ပြင်ပြီး commit လုပ်မိရင်တော့ မပျောက်အောင် `git switch -c` နဲ့ branch တစ်ခု အရင်ဆောက်ထား။

ဖိုင်တစ်ခုတည်းကို ပြန်ယူချင်ရင် `git restore`။ ဒီအတိုင်းဆိုရင် မ commit ရသေးတဲ့ ပြင်ဆင်မှုကို ဖျက်တယ်၊ `--staged` ဆိုရင် stage ထဲက ပြန်ထုတ်တယ်၊ `--source` ထည့်ရင် အဲဒီ commit တုန်းက version ကို ပြန်ယူပေးတယ်။

Commit ဖြုတ်တဲ့နေရာမှာတော့ `reset` နဲ့ `revert` ကွာတာကို သေချာနားလည်ထားမှ ဖြစ်မယ်။ `reset` က branch ကို နောက်ကို ပြန်ဆွဲချတာ။ `--soft` ဆိုရင် ပြင်ထားတာတွေ stage ထဲမှာ ကျန်တယ်၊ commit တွေ ပြန်ပေါင်းချင်တဲ့အခါ သုံးတယ်။ ဘာမှမထည့်ရင် (`--mixed`) ဖိုင်ထဲမှာတော့ ကျန်ပေမယ့် stage ကနေ ထွက်သွားတယ်။ `--hard` က အကုန်ဖျက်တာ၊ မ commit ရသေးတဲ့ အလုပ်တွေ ပြန်မရနိုင်တော့ဘူး။ မသုံးခင် `git status` တစ်ချက်ကြည့်တာ ကောင်းတယ်။

`revert` ကတော့ history ကို မထိဘူး။ အရင် commit က ပြင်ခဲ့တာကို ပြောင်းပြန်လုပ်ပေးတဲ့ commit အသစ်တစ်ခု ထပ်ထည့်ပေးတာ။ Merge commit ကို revert လုပ်ရင် `-m 1` ထည့်ပေးရတယ်။ ကိုယ်လိုက်နာတဲ့ စည်းကမ်းက ရိုးရိုးလေးပဲ။ Push မလုပ်ရသေးရင် `reset`၊ push လုပ်ပြီးသွားရင် `revert`။ Push ပြီးသားကို reset လုပ်ရင် team ထဲက တခြားသူတွေ history နဲ့ ကွဲသွားပြီး ရှုပ်ကုန်မယ်။ နောက်ဆုံး commit ရဲ့ message မှားတာ၊ ဖိုင်တစ်ခု ထည့်ဖို့မေ့တာလောက်ပဲဆိုရင် `git commit --amend` နဲ့ ပြင်လို့ရတယ်၊ ဒါလည်း push မလုပ်ခင်မှ သုံးရမယ်။

နောက်ဆုံး၊ `reset --hard` မှားရိုက်မိတာ၊ branch မှားဖျက်မိတာမျိုး ဖြစ်ရင် `git reflog` ကို ကြည့်ပါ။ HEAD ရောက်ခဲ့ဖူးတဲ့ နေရာတိုင်းကို ကိုယ့်စက်ထဲမှာ မှတ်ထားတယ်။

```
a1b2c3d HEAD@{0}: reset: moving to HEAD~3
e4f5g6h HEAD@{1}: commit: Add payment feature
```

ဒီလိုမြင်ရရင် `git reset --hard HEAD@{1}` နဲ့ မမှားခင်အခြေအနေ ပြန်ရောက်တယ်။ ဖျက်မိတဲ့ branch ဆိုရင် `git branch recovered e4f5g6h` နဲ့ ပြန်ဆောက်လို့ရတယ်။ Reflog က ရက် ၉၀ လောက် သိမ်းထားပေမယ့် ကိုယ့်စက်ထဲမှာပဲ ရှိတာ၊ clone အသစ်မှာ မပါဘူး။

## Rebase vs merge cheatsheet

ဒါက team ထဲမှာ ငြင်းကြတာ အများဆုံး ခေါင်းစဉ်ပဲ။ နှစ်ခုလုံးက တစ်ဖက် branch က ပြောင်းလဲမှုတွေကို ယူလာတာချင်း တူတယ်။ ကွာတာက history ကို ဘယ်လိုချန်ခဲ့လဲ ဆိုတာ။

`feature` ခွဲထွက်ပြီးနောက် `main` မှာ E နဲ့ F ဝင်လာတယ်ဆိုပါစို့။

```
A---B---E---F     (main)
     \
      C---D       (feature)
```

`feature` ပေါ်ကနေ `git merge main` လုပ်ရင် C နဲ့ D ဒီအတိုင်းရှိနေပြီး D နဲ့ F ကို ချိတ်တဲ့ merge commit M ထပ်ပေါ်လာတယ်။ `main` ကတော့ F မှာပဲ ရှိနေသေးတယ်။

```
A---B---E---F         (main)
     \       \
      C---D---M       (feature)
```

`git rebase main` လုပ်ရင်တော့ C နဲ့ D ကို F ရဲ့ နောက်မှာ ပြန်တင်ပေးတယ်။ C' နဲ့ D' က ပြင်ထားတာချင်း အတူတူပေမယ့် hash အသစ်နဲ့ commit အသစ်တွေ။ C နဲ့ D အဟောင်းကို ဘယ် branch ကမှ မညွှန်တော့ဘူး။

```
A---B---E---F             (main)
             \
              C'---D'     (feature)
```

```bash
git merge main
git rebase main
git pull --rebase
git config --global pull.rebase true
git rebase -i HEAD~4
git rebase --continue
git rebase --skip
git rebase --abort
git merge --abort
git push --force-with-lease
```

Merge က ဖြစ်ခဲ့တာကို ဖြစ်ခဲ့တဲ့အတိုင်း မှတ်ထားတယ်။ History ကို ပြန်မရေးလို့ စိတ်ချရတယ်၊ မကြိုက်ရင် M ကို revert လုပ်ရုံပဲ။ Branch များတဲ့ project မှာတော့ log ကြည့်ရတာ ရှုပ်တယ်။ Rebase က history ကို တစ်ကြောင်းတည်း သန့်သန့်ပေးတယ်၊ ဒါပေမယ့် အဲဒါရဖို့ history ကို ပြန်ရေးလိုက်တာ။ Conflict တက်ရင်လည်း merge က တစ်ခါတည်းဖြေရပေမယ့် rebase က commit တစ်ခုချင်းစီမှာ ထပ်ခါထပ်ခါ ဖြေရနိုင်တယ်။

ကိုယ်ကတော့ ဒီလိုခွဲသုံးတယ်။ ကိုယ်တစ်ယောက်တည်း သုံးနေတဲ့ feature branch ကို main နဲ့ update ဖြစ်အောင်လုပ်ရင် rebase၊ main လို အားလုံးသုံးနေတဲ့ branch ထဲ ပေါင်းရင် merge။ တခြားသူတွေ pull ယူထားပြီးသား branch ကိုတော့ rebase မလုပ်နဲ့။ ကိုယ့်စက်မှာ C' D' ဖြစ်သွားပေမယ့် သူတို့စက်မှာ C D အဟောင်းတွေ ကျန်နေတော့ commit တွေ ထပ်ပြီး ရှုပ်ကုန်ရော။ ကိုယ့် feature branch ကို rebase ပြီး push ပြန်လုပ်ရရင် `--force` မသုံးဘဲ `--force-with-lease` ကိုသုံး။ ကိုယ်နောက်ဆုံး fetch လုပ်ပြီးနောက် တခြားသူ push ထားရင် overwrite မလုပ်ဘဲ ရပ်ပေးတယ်။

PR မတင်ခင် `git rebase -i` နဲ့ commit တွေ သပ်ရပ်အောင် လုပ်တာလည်း အသုံးဝင်တယ်။ Editor ပွင့်လာရင် ဒီလိုမြင်ရမယ်။

```
pick   a1b2c3d Add login form
fixup  e4f5g6h fix typo
reword 7h8i9j0 Add validation
drop   k1l2m3n debug print
```

ရှေ့က စကားလုံးကို ပြောင်းပေးရုံပဲ။ `pick` က ဒီအတိုင်းထား၊ `reword` က message ပြင်၊ `squash` နဲ့ `fixup` က အပေါ်က commit နဲ့ပေါင်း (`fixup` က ဒီ commit ရဲ့ message ကို ပစ်လိုက်တယ်)၊ `drop` က ဖျက်။

နေ့စဉ်လုပ်ဖြစ်တဲ့ အစီအစဉ်ကတော့ ဒီလိုပဲ။ Feature branch ခွဲ၊ `git fetch` ပြီး `git rebase origin/main` နဲ့ update လုပ်၊ PR မတင်ခင် `rebase -i` နဲ့ ရှင်း၊ `--force-with-lease` နဲ့ push၊ ပြီးမှ PR ကနေ main ထဲ merge ဒါမှမဟုတ် squash နဲ့ ပေါင်း။ ဒီလိုဆို feature branch မှာ history သန့်ပြီး main ရဲ့ history ကိုလည်း မထိဘူး။

ဒီထဲက အချက်တွေကို Pro Git စာအုပ် (git-scm.com/book) နဲ့ git-scm.com/docs က git-merge, git-rebase, git-reset, git-reflog စာမျက်နှာတွေကို ကိုးကားပြီး ရေးထားတာပါ။
