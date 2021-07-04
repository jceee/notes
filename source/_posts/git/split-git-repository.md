title: 拆分Git仓库并保存提交记录  
date: 2021-06-26 22:48:38  
tags: git   
categories: git

---

>分分合合是常态，大项目有时会有拆分的需求，当想拆分git项目，又想保留提交记录时，可以按下面流程操作

## 基本设定

父项目为 `https://github.com/xxx/demo.git`

父仓库目录：/demo

当前子仓库目录：/demo/target_child

目标是把target_child拆到新项目：`https://github.com/xxx/target_child.git`



##  step1 clone 一份原仓库文件到本地，目录为目标文件夹





```

​cd ~/git/code/
​
​git clone https://github.com/xxx/demo.git target_child
​

```



## step2 删掉原仓库的remote


```

cd target_child

git remote rm origin


```



## step3 过滤无用的历史记录

过滤所有历史提交，只保留对指定子目录有影响的提交. 命令执行完毕后，就可以看到本地的新仓库已经是原仓库子目录中的内容了，且保留了关于该子目录所有的提交历史。

```


git filter-branch --tag-name-filter cat --prune-empty --subdirectory-filter ./target_child -- --all


```



| 参数|作用 |
|---|---|
|--tag-name-filter |该参数控制我们要如何处理旧的 tag，cat 即表示原样输出|
|--prune-empty |删除空的（对子目录没有影响的）提交|
|--subdirectory-filter |指定子目录路径, 如`./target_child`|
|-- --all |该参数必须跟在`--`后面，表示对所有分支进行操作。如果你只想保存当前分支，也可以不添加此参数。|



## step 4 清理.git的object

`.git` 目录里还是保存有不少无用的object，我们需要将其清除掉以减小新仓库的体积.

PS: `git gc`操作耗时比较久,请耐心等待.



```


git reset --hard

git for-each-ref --format="%(refname)" refs/original/ |xargs -n 1 git update-ref -d

git reflog expire --expire=now --all

git gc --aggressive --prune=now


```

## step 5 将新的本地仓库推送到远端

先创建新的仓库：`https://github.com/xxx/target_child.git`

```


//添加远端分支

git remote add origin https://github.com/xxx/target_child.git

//强制推送到远端

git push --fore origin master


```

PS:

若master分支为保护状态，强制推送时会遇到如下错误：

```

remote: GitLab: You are not allowed to force push code to a protected branch on this project.

```

须解除master分支的保护状态，推完之后再改回来