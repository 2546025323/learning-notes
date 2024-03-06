# git指令

```git
# 查看当前git版本
git -version

# 创建初始化本地仓库
git init

# 克隆远程仓库
# url：远程仓库的http地址。new_name(可选)：克隆后的新的文件夹名称
git clone (url) (new_name)

# 对本地单个仓库进行配置
git config user.name huangxuming
git config user.email 2546025323@qq.com

# 对本地所有仓库进行配置
git config --global user.name "huangxuming"
git config --global user.email "2546025323@qq.com"

# 查看暂存区状态
git status

# 从工作区到暂存区
# filename：文件名
git add filename

# 从暂存区到工作区
# filename：文件名
git rm --cached filename

# 从暂存区到存储区
# message：提交信息
git commit -m (message)

# 查看日志文件
git log
git log --oneline
git log 标签名
 
 # 误删除
 git restore filename
 git reset --hard 版本号
 git revert 版本号 # 删除前的版本号
 
 # 创建分支
 git branch 分支名
 
 # 转到某分支下
 git checkout 分支名
 
 # 查看当前分支数目
 git branch -v
 
 # 创建并转到该分支下
 git checkout -b 分支名
 
 # 删除分支
 git branch -d 分支名
 
 # 合并分支(在主分支下完成操作)
 git merge 分支名
 
 # 查看所有标签
 git tag
 
 # 添加标签
 git tag 标签名 版本号
 
 # 将本地仓库发送到远程仓库
 # origin：config文件中的远程仓库地址git@...
 git push origin
 
 # 生成安全公钥ssh
 ssh -keygen -t rsa -c git@...
 
 # 将远程仓库更新到本地仓库
 git pull origin
```

