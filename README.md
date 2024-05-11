# learning-notes
关于我的一些学习笔记

使用Git Bash推送本地代码到GitHub的步骤大致如下：

1. **确保你已经安装了Git**：首先，你需要在你的计算机上安装Git。如果还没有安装，可以从[Git官方网站](https://git-scm.com/downloads)下载并安装。

2. **配置Git**：打开Git Bash，配置你的用户名称和邮箱，这些信息将用于GitHub上的提交记录。

   ```shell
   git config --global user.name "你的用户名"
   git config --global user.email "你的邮箱"
   ```

3. **在GitHub上创建仓库**：登录GitHub账户，点击顶部的"+"号，选择"New repository"，填写仓库名称和描述，选择是否公开或私有，然后点击"Create repository"。

4. **在本地项目目录初始化Git仓库**：在包含你想要推送代码的文件夹内，右键选择"Git Bash Here"打开Git Bash终端。

   ```shell
   git init
   ```

5. **添加文件到暂存区**：如果你想添加所有文件，可以使用以下命令：

   ```shell
   git add .
   ```

   如果只想添加特定文件，可以指定文件名，如：

   ```shell
   git add filename.txt
   ```

6. **提交更改**：提交暂存区的文件到本地仓库，并附上提交信息。

   ```shell
   git commit -m "提交信息，比如：'Initial commit'"
   ```

7. **关联远程GitHub仓库**：将本地仓库与GitHub上的仓库关联起来。复制GitHub仓库页面上的HTTPS或SSH链接，然后在Git Bash中输入：

   ```shell
   git remote add origin https://github.com/your-username/your-repository.git
   ```

8. **推送代码到GitHub**：如果这是你第一次推送这个仓库，需要使用 `-u` 参数来设置默认的上游分支（通常是`main`或`master`）。

   ```shell
   git push -u origin main
   ```

   或者如果是`master`分支：

   ```shell
   git push -u origin master
   ```

   对于后续的推送，只需使用：

   ```shell
   git push
   ```
