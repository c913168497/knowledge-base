## 分支策略

- 按分支属性分类
   - 主干分支：master 
   - 特性分支：feature-[序号]、fix-[序号]、hotfix-[序号]
   - 开发分支：dev-[序号]-[开发者]
   - 发布分支：release-[版本号]
   - 部署分支：release（正式）、test（测试）、join（联调）、dev（开发）。
- 按分支存在时间分类
   - 常设分支
      - master
   - 临时分支
      - 除master外的所有分支皆为临时分支
      - 远程临时分支和本地临时分支
      - 远程临时分支：特性分支、发布分支、部署分支
      - 本地临时分支：开发分支           
- 开发分支合并到特性分支时，必须发起 Pull / Merge Requset ，并指定一个人 code review。 <font color="#ff0000" >注意：</font>只有通过审核的代码才能发布到正式环境!! 
- 远程临时分支由发起者追踪和维护， reviewer 负责删除。
- 所有的开发和迭代都应都应临时分支上。
- 合并到 master 分支的代码必须打上 Tag（统一由Jenkins打）

## 分支解释
- master （主干分支）： 始终只接受已发布到线上的分支的回归 ，没有发布现网的分支不能回归master  
 
- 特性分支：从master切出，每次新功能开发、bug修复、优化等单独保存代码的分支  
 
- 开发分支：从特性分支切出，开发分支回归特性分支时必须提交合并请求进行代码审核  
 
- 发布分支：从master切出，确保每次发版是最新现网版本、方便发布版本时组合各特性分支、方便回归master而存在的一个分支  
 
- 部署分支：从发布分支切出，用户对应各个部署环境的分支，规则：以4套【环境】命名，如：dev，join，test，release    
 
- 注意事项 
   - 所有临时分支都是从master直接或间接切出，所有临时分支都将回归到master或者丢弃
   - 发布分支必须从master主干checkout，然后合并各特性分支代码到该分支  
   - 只有部署分支不需要回归。  
 
- 临时分支
   - 本地临时分支主要用于迭代、bug修复
   - 远程临时分支主要用于发起 Pull / Merge Requset，合并代码后删除。
   - 如多人开发迭代，可共同商量创建远程临时开发分支便于代码交换，临时维护。
   - 禁止非管理员删除远程临时分支

## Pull / Merge Request
代码合并到特性分支：
<br/>
1.将需要合并到本地开发分支 push 到 gitlab。
2.进入工程 -> merge request -> create new merge request 。
3.选择源分支、目标分支，确定。
4.review 负责人进入 merge request，确认没有问题之后选择 Auto Merge（或者手动在本地合并之后再 push 到 gitlab），并关闭这个 merge request，完成。
5.如果发现问题那么在有问题的行下注释，并提醒 request 的发起人及时修改。
6.删除本地临时分支，本地特性分支更新到最新状态。

## Code Review
- 提交 Pull / Merge Request 时， Commit 和 Message 要足够清晰详细。 切记，如果一次提交的内容包含很多 Commit，请不要使用自动生成的描述。 请用简短且足够说明问题的语言（理想是控制在3句话之内）来描述：
<br/>
 !!!#eeeeee 你改动了什么，解决了什么问题，需要代码审查的人留意那些影响比较大的改动。特别需要留意，如果对基础、公共的组件进行了改动，一定要另起一行特别说明。!!! 
<br/>
- 审核人员邀请原则：项目参与人员 & 团队同事 & 团队 Leader。（对项目足够了解，对项目足够了解，对项目足够了解，重要的事情说三遍）；
- 评论中至少出现一个 lgtm 且保证代码评审通过之后 Pull / Merge Request 才可以被合并；（注：lgtm 即 looks good to me 的缩写）


## git命令使用规则
1.禁止使用pull命令从feature或master分支拉最新代码，必须使用fetch，rebase命令；
2.合并开发分支到临时组合分支，必须使用merge命令，不允许使用rebase命令；
3.不允许在不同的分支上同时开发相同的功能

## 开发迭代工作流示例
0.创建功能开发分支

- 从gitlab上切出特性开发分支（此步骤在协调开发时需要，如个人开发则直接从1开始即可）
- 并设置特性分支保护。

1.创建本地临时开发分支(dev-[特性序列号]-[开发者])

``` 
git fetch --all
git checkout feature-001
git checkout -b dev-001-Adam
``` 
2.发布分支-联调(join)

``` 
# 把代码合并到联调环境部署分支（join）进行部署
git checkout dev
git merge dev-001-Adam
git push origin dev
``` 
3.进入Jenkins找到相应job部署到开发联调环境

  -  如中间解决bug，重复2和3
  - 始终在本地临时开发分支上修改代码 dev-001-Adam

4.测试

``` 
# 把代码合并到开发环境部署分支（test）进行部署
git checkout test
git merge dev-001-Adam
git push origin test
``` 
5.进入Jenkins找到相应job部署到测试环境

  - 如中间解决bug，重复4和5
  - 始终在本地临时开发分支上修改代码 dev-3.5.3.2

6.创建合并请求

``` 
git checkout dev-001-Adam
git log
# 修改过多的、乱七八糟提交记录
# 注意 git reset 一定要加--soft，不然代码将被还原无法恢复，等于工作白干
git reset --soft <commit-id(在git log 中查看你是从master分支哪条提交记录切的分支，就回到那个提交记录)>
# 推送本地临时开发分支到远程仓库，方便创建合并请求
git push origin dev-001-Adam
``` 

- 进入gitlab，操作查看 Pull / Merge Request !!! 

7.code reviewer 合并请求,删除远程临时开发分支

<br/>
<br/>
<br/>
<br/>
<br/>
<br/>