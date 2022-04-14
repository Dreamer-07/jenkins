# Jenkins 持续集成

## 持续集成及Jenkins介绍

### 软件开发生命周期

软件开发生命周期又叫做 SDLC, 它是集合了计划，开发，测试和部署过程的集合，如下图所示

![image-20220326210627634](README.assets/image-20220326210627634.png)

- 需求分析：根据项目需求，执行一个可行性计划的分析，这阶段主要是对项目信息的收集，分析项目的预算，以及收益情况，明确项目创建的目标
- 设计：主要是设计系统的架构和整个系统最后出来需要什么功能
- 实现：开发者根据任务和在设计阶段确定的目标进行代码开发
- 测试：包括功能测试，代码测试，压力测试等
- 进化(维护)：对产品不断的进行改进和维护，根据用户的使用情况和反馈，可能需要对某功能进行修改，bug修复，功能增加等

### 软件开发瀑布模型

![image-20220326211444790](README.assets/image-20220326211444790.png)

| 优势                                 | 劣势                                                         |
| ------------------------------------ | ------------------------------------------------------------ |
| 简单易用和理解                       | 每个阶段都是固定的，会产出大量的文档，增加工作量             |
| 当完成一个阶段之后，只用关注后续阶段 | 由于开发模型是线性的，用户只有等到最后才能看到产品的最终效果，增加了成本和风险 |
| 为项目提供了按阶段划分的检查节点     | 最核心的劣势在于 **无法快速适应用户提出需求的变化**          |

### 软件敏捷开发

> 什么是敏捷开发？

敏捷开发(Agile Develop) 的核心是**迭代开发**与**增量开发**

- 迭代开发：对于大型软件项目，传统的开发方式是采用一个大周期进行开发，整个过程就是一次大开发

  迭代开发的方式则不一样，它将开发过程拆分成多个小周期，即将一次 **大开发** 拆分成 **多次小开发**，每次小开发都是一样的流程

- 增量开发：软件的每个版本，都会新增一个用户可以感知的完整功能，也就是说，按照**新增功能来划分迭代版本**

> 敏捷开发如何迭代？

虽然敏捷开发分成多个迭代，但是也要求，每次迭代都是一个**完整的软件开发周期**

![image-20220326212811912](README.assets/image-20220326212811912.png)

> 敏捷开发的好处

- 早期交付：降低成本
- 降低风险：可以即使了解市场需求，降低产品不适用的风险

### 持续集成

> 敏捷开发的好搭档

持续集成(CI)指的是，频繁的(一天多次)将代码集成到主干

持续集成的目的就是：**让产品可以快速迭代，同时还能保证高质量**；核心措施就是代码集成到主干后，必须通过自动化测试，只要有一个测试用例失败，就不能集成

> 持续集成的流程

- 提交：流程的第一步，开发者想代码仓库提交代码
- 测试(第一轮)：代码仓库对 commit 操作配置了钩子(hook), 只要代码提交或者合并到主干，就会进行自动化测试
- 构建：通过第一轮测试后，代码就可以合并到主干，就算可以交付了。而构建指的是，**将源码转为可以运行的实际代码**(例如安装依赖，配置各种资源等等)
- 测试(第二轮)：构建之后，如果第一轮已经覆盖了所有测试内容，第二轮就可以省略，当前，这时构建步骤就需要移动到第一轮测试前面
- 部署：通过第二轮测试，当前代码就是一个可以直接部署的版本，将这个版本的所有文件打包存档，发送生产服务器
- 回滚(支持自动回滚)：一旦当前版本发生问题，就要回滚到上一个版本的构建结果

> 持续集成的组成要素

- 一个**自动构建过程**，从构建代码，编译构建，运行测试，结果记录，测试统计都是自动完成的，无需人工干预
- 一个**代码存储库**，即需要版本控制软件来保证代码的可维护性，同时作为构建过程的素材库，一般使用 SVN / GIT
- 一个**持续集成服务器**，而 **Jenkins** 就是一个配置简单和使用方便持续集成服务器

> 持续集成的好处

1. 降低风险，由于持续集成会自动的不断的进行构建，编译和测试；可以很早期的发现问题，减小修复的代价
2. 对系统健康持续检查，减少发布风险带来的问题
3. 减少重复性的工作
4. **持续部署**，提供可部署单元包
5. 持续交付可供使用的版本

### Jenkins 介绍

![image-20220327161127039](README.assets/image-20220327161127039.png)

官网：https://www.jenkins.io/zh/

简介：是一款流行的开源的**持续集成(CI)工具**,广泛用于项目开发，具有自动化构建，测试和部署等功能

特征：

![image-20220327161350155](README.assets/image-20220327161350155.png)

## Jenkins 安装和持续集成环境配置

### 持续集成流程说明

![image-20220327161731040](README.assets/image-20220327161731040.png)

1. 开发人员进行 commit 将代码提交到 git 仓库
2. Jenkins 作为持续集成工具，使用 git 工具到 git 仓库拉取代码到集成服务器，再配合 JDK, Maven 等软件完成代码编译，代码测试，审查，打包等工作；(在这个过程中每一步出错，都重新执行一次整个流程)
3. Jenkins 将打包的 war / jar 包分发到测试服务器/生产服务器，供测试人员/用户访问

### Gitlab 代码托管仓库服务器

#### 安装

1. 安装相关依赖

   ```bash
   yum -y install policycoreutils openssh-server openssh-clients postfix
   ```

2. 启动 ssh 服务 & 并设置开机自启

   ```bash
   systemctl enable sshd && sudo systemctl start sshd
   ```

3. 设置 postfix 开机自启(用来支持 gitlab 发信功能)

   ```bash
   systemctl enable postfix && systemctl start postfix
   ```

4. 开放 ssh 以及 http 服务，然后重新加载防火墙列表(也可以直接关闭防火墙)

   ```bash
   firewall-cmd --add-service=ssh --permanent
   firewall-cmd --add-service=http --permanent
   firewall-cmd --reload
   ```

5. 下载 [gitlab](https://mirrors.tuna.tsinghua.edu.cn/gitlab-ce/yum/el6/gitlab-ce-12.4.2-ce.0.el6.x86_64.rpm) 进行安装

   ```bash
   rpm -i gitlab-ce-12.4.2-ce.0.el6.x86_64.rpm
   ```

6. 修改 gitlab 配置

   ```bash
   vi /etc/gitlab/gitlab.rb
   ```

   主要是修改 gitlab 访问地址和端口

   访问地址是默认有的，要去修改; nginx那个可以直接写，默认是注释的

   ```ruby
   external_url 'http://192.168.66.100:82'
   nginx['listen_port'] = 82
   ```

7. 重新加载配置以及重启 gitlab(需要等一会)

   ```bash
   gitlab-ctl reconfigure
   gitlab-ctl restart
   ```

8. 开放对应的端口[如果关闭防火墙了那无所谓]

   ```bash
   firewall-cmd --zone=public --add-port=82/tcp --permanent
   firewall-cmd --reload
   ```

9. 启动，访问

   ![image-20220403202521950](README.assets/image-20220403202521950.png)

   ![image-20220403202812100](README.assets/image-20220403202812100.png)

#### 添加组，创建用户，创建项目

1. 创建组

   使用管理员 root 创建组，一个组里面可以有多个项目，可以将开发者添加到组里面并进行相关权限设置

   ![image-20220403203635701](README.assets/image-20220403203635701.png)

   ![image-20220403203906383](README.assets/image-20220403203906383.png)

2. 创建用户

   ![image-20220403204108904](README.assets/image-20220403204108904.png)

   需要注意的点：

   - 分配身份权限

     ![image-20220403204206882](README.assets/image-20220403204206882.png)

   - 密码：需要创建用户后才能设置(可以在右上角退出登录试试)

     ![image-20220403204244066](README.assets/image-20220403204244066.png)

     ![image-20220403204250794](README.assets/image-20220403204250794.png)

3. 将用户添加到用户组中

   ![image-20220403204515347](README.assets/image-20220403204515347.png)

   ![image-20220403204542802](README.assets/image-20220403204542802.png)

   

   Guest：可以创建 issue，发布评论，不能读写版本库

   Reporter：可以克隆代码，不能提交

   Developer：可以克隆代码，开发，提交，push

   Maintainer：可以创建项目，项目 tag，保护分支，添加项目成员，边际成员

   Owner：可以设置项目访问权限，删除项目，迁移项目，管理组成员

4. 创建项目

   ![image-20220403204920185](README.assets/image-20220403204920185.png)

#### 将代码提交到 gitlab 仓库

就和正常上传到 git 一样，地址可以在仓库的左上角 [Clone] 上搞到，不过第一次上传的时候 need 输入账号密码，输入自己 gitlab 的账户即可(建议用 root 不然可能遇到权限问题emmm)

![image-20220403210112135](README.assets/image-20220403210112135.png)

![image-20220403210603326](README.assets/image-20220403210603326.png)

### Jenkins 安装

1. 安装 JDK 环境

   ```bash
   yum install java-1.8.0-openjdk* -y
   ```

2. 进行 Jenkins 安装

   ```bash
   sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
   sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
   yum install jenkins
   ```

   如果第一条命令出现错误，就换成

   ```bash
   wget --no-check-certificate -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
   ```

3. 启动 Jenkins

   ```bash
   systemctl start jenkins
   ```

4. 修改 Jenkins 配置(改完记得重启)

   ```bash
   vi /etc/sysconfig/jenkins
   ```

   修改内容

   ```
   JENKINS_USER="root"
   JENKINS_PORT="8888"
   ```

   [坑]如果修改之后发现 Jenkins 的端口依然是 8080，可以使用以下方法

   ```bash
   vim /usr/lib/systemd/system/jenkins.service
   ```

   > Environment="JENKINS_PORT=8888"

   ```bash
   # 重新加载配置文件
   systemctl daemon-reload
   ```

5. 打开游览器访问(记得开放对应的端口)

6. 获取并输入 admin 密码

   ```bash
   cat /var/lib/jenkins/secrets/initialAdminPassword
   ```

7. 跳过插件安装(因为 Jenkins 插件需要连接官网下载，速度太慢，所以这里先暂时跳过)

   ![image-20220410161932819](README.assets/image-20220410161932819.png)

   ![image-20220410161947839](README.assets/image-20220410161947839.png)

8. [可选]创建一个管理员账户

   ![image-20220410162016408](README.assets/image-20220410162016408.png)

### Jenkins 插件安装

#### 修改 Jenkins 插件下载地址

1. 打开 Jenkins；jenkins -> Manage jenkins -> Maven Plugins，点击 Available

2. 等待列表加载完成，这一步是为了将 Jenkins 官方的插件列表下载到本地

3. 修改地址文件，替换为国内插件地址

   ```bash
   cd /var/lib/jenkins/updates
   
   sed -i 's/http:\/\/updates.jenkinsci.org\/download/https:\/\/mirrors.tuna.tsinghua.edu.cn\/jenkins/g' default.json && sed -i 's/http:\/\/www.google.com/https:\/\/www.baidu.com/g' default.json
   ```

4. 在 Plugin Manager 中点击 Advance，划到最下面 Update Site 将其中的 URL 修改成国内的下载地址

   > https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json

   点击 Submit

5. 在 Jenkins 访问地址后面加上 `/restart`，即可重启 Jenkins

   ![image-20220410194900098](README.assets/image-20220410194900098.png)

#### 安装中文插件

1. Manager Jenkins -> Manage Plugins -> Available 等待列表下载完成

2. 输入 `Chinese`

   ![image-20220410195048624](README.assets/image-20220410195048624.png)

3. 等待安装后勾选重启框即可

   ![image-20220410195126079](README.assets/image-20220410195126079.png)

4. 刷新页面

### Jenkins 用户权限管理

> 利用 Role-based Authorization Strategy 管理 Jenkins 用户

1. 系统管理 -> 插件管理 -> 可选插件，等待列表加载完成

2. 搜索 [Role-based Authorization Strategy]

   ![image-20220410201406595](README.assets/image-20220410201406595.png)

3. 等待安装，这个可以不用重启

4. 系统管理 -> 全局安全配置 -> 授权策略

   ![image-20220410201524704](README.assets/image-20220410201524704.png)

5. [创建角色] 系统管理 -> Manage and Assign Roles -> 管理角色

   ![image-20220410201903998](README.assets/image-20220410201903998.png)

   下面有个 **Save** 别忘了保存

6. [创建用户] 系统管理 -> 管理用户

   ![image-20220410202037373](README.assets/image-20220410202037373.png)

7. [给用户分配角色] 系统管理 -> Manage and Assign Roles -> Assign Roles(记得下面有个 **Save**)

   ![image-20220410202335581](README.assets/image-20220410202335581.png)

8. [创建项目用来测试] 新建任务

   ![image-20220410202411558](README.assets/image-20220410202411558.png)

   ![image-20220410202446457](README.assets/image-20220410202446457.png)

9. 分别使用两个账户登录

   ![image-20220410202655137](README.assets/image-20220410202655137.png)

   ![image-20220410202813121](README.assets/image-20220410202813121.png)

### Jenkins 凭证管理

作用：凭据可以用来存储需要密文保护的数据库密码、Gitlab密码信息、Docker私有仓库密码等，以便 Jenkins可以和这些第三方的应用进行交互

#### 安装 Credentials Binding 插件

![image-20220410213845546](README.assets/image-20220410213845546.png)

安装成功后可以在 系统管理 中看到相关选项

![image-20220410214216678](README.assets/image-20220410214216678.png)

#### 安装 git 插件 & git 工具

作用：为了让Jenkins支持从Gitlab拉取源码，需要安装Git插件以及在CentOS7上安装Git工具。

> git 插件安装

![image-20220410214550355](README.assets/image-20220410214550355.png)

在安装完成后可以[新建任务]并在其中的**源码管理**中可以看到 git

![image-20220410214817477](README.assets/image-20220410214817477.png)

> 在 Centos 中安装 git

```bash
yum install git -y #安装
git --version      #安装后查看版本
```

#### 使用用户密码类型拉取 gitlab 仓库

1. [凭据配置]

   ![image-20220410220228029](README.assets/image-20220410220228029.png)

   ![image-20220410220358217](README.assets/image-20220410220358217.png)

2. 添加凭据

   ![image-20220410220509014](README.assets/image-20220410220509014.png)

   ![image-20220410220610504](README.assets/image-20220410220610504.png)

3. 选择凭据类型并填写相关数据

   ![image-20220410221256917](README.assets/image-20220410221256917.png)

   ![image-20220410221320922](README.assets/image-20220410221320922.png)

4. [测试凭证是否可以使用] 打开任务 -> 配置 -> 源码管理(记得点 Save)

   ![image-20220410221740560](README.assets/image-20220410221740560.png)

5. 回到任务界面，点击[立即构建]，等待一会，点击[工作区]即可看到拉取的代码

   ![image-20220410222210282](README.assets/image-20220410222210282.png)

#### 使用 SSH 密钥拉取 gitlab 仓库

> SSH 免密登录示意图

![image-20220410222738304](README.assets/image-20220410222738304.png)

1. 使用 root 用户生成公钥和私钥

   ```bash
   ssh-keygen -t rsa
   ```

2. 可以在 `/root/.ssh` 下查看公钥私钥文件

   ![image-20220410222910520](README.assets/image-20220410222910520.png)

3. 复制公钥放到 gitlab 上

   ![image-20220410223116124](README.assets/image-20220410223116124.png)

4. 在 Jenkins 中添加密钥凭证

   ![image-20220410223337125](README.assets/image-20220410223337125.png)

5. 使用密钥凭证拉取 gitlab 仓库上的数据

   ![image-20220410223446619](README.assets/image-20220410223446619.png)

6. [立即构建]

   ![image-20220410223504243](README.assets/image-20220410223504243.png)

### Maven 安装和配置

> 在 Jenkins 服务器上，我们需要安装 Maven 来编译和打包项目

#### 安装 Maven

1. 先将 Maven 软件上传到服务器

2. 解压 Maven 文件夹

   ```bash
   tar -xzf apache-maven-3.6.2-bin.tar.gz #解压
   mkdir -p /opt/maven 				   #创建目录
   mv apache-maven-3.6.2/* /opt/maven     #移动文件
   ```

3. 配置环境变量

   ```bash
   vi /etc/profile
   ```

   > export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk 
   >
   > export MAVEN_HOME=/opt/maven 
   >
   > export PATH=$PATH:$JAVA_HOME/bin:$MAVEN_HOME/bin

   ```bash
   source /etc/profile #配置生效
   mvn -v 				#查找Maven版本
   ```

#### 修改 Maven 的 setting.xml

1. 创建本地仓库目录

   ```bash
   mkdir /opt/resp
   ```

2. 修改 maven 配置文件

   ```bash
   vi /opt/maven/conf/settings.xml
   ```

   ```xml
   <!-- 这一段可以直接加 -->
   <localRepository>/opt/resp</localRepository>
   <!-- 这一段在 mirrors 中添加 -->
   <mirror>
       <id>aliyunmaven</id>
       <mirrorOf>*</mirrorOf>
       <name>阿里云公共仓库</name>
       <url>https://maven.aliyun.com/repository/public</url>
   </mirror>
   ```

#### 在 Jenkins 上配置 JDK & Maven

系统管理 -> 全局工具配置

![image-20220411114401111](README.assets/image-20220411114401111.png)

![image-20220411114445870](README.assets/image-20220411114445870.png)

记得点击 **保存**

#### 添加 Jenkins 全局配置

系统管理 -> 系统配置 -> 全局属性

![image-20220411114806459](README.assets/image-20220411114806459.png)

#### 测试 Maven 是否配置成功

打开一个任务，点击[配置]，找到[构建] -> [增加构建步骤] -> [执行 shell]

```shell
mvn clean package
```

点击保存后，点击[立即构建]

tips：如果出现错误可以参考

- https://blog.csdn.net/qq_34794527/article/details/117363051
- https://blog.csdn.net/luomo0203/article/details/110414085

### Tomcat 安装和配置

1. 安装 JDK

   ```bash
   yum install java-1.8.0-openjdk* -y
   ```

2. 将对应的 tomcat 上传的服务器上并解压

3. 创建一个目录用来存放 tomcat

   ```bash
   mkdir -p /opt/tomcat
   ```

4. 移动 tomcat 到目的文件夹

   ```bash
   mv apache-tomcat-8.5.78/* /opt/tomcat/
   ```

5. 默认情况下 tomcat 没有角色维护管理，所以需要额外配置

   ```bash
   vi /opt/tomcat/conf/tomcat-users.xml
   ```

   ```xml
   <!-- 配置角色 -->
   <role rolename="tomcat"/>
   <role rolename="role1"/>
   <role rolename="manager-script"/>
   <role rolename="manager-gui"/>
   <role rolename="manager-status"/>
   <role rolename="admin-gui"/>
   <role rolename="admin-script"/>
   <!-- 配置用户并分配角色 -->
   <user username="tomcat" password="tomcat" roles="manager-gui,manager-script,tomcat,admin-gui,admin-script"/>
   ```

   ```bash
   vi /opt/tomcat/webapps/manager/META-INF/context.xml
   ```

   ```xml
   <!-- 将这部分内容注释dio即可 -->
   <!--
   <Valve className="org.apache.catalina.valves.RemoteAddrValve"
   allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" />
   -->
   ```

6. 运行 tomcat

   ```bash
   /opt/tomcat/bin/startup.sh
   ```

7. 访问对应的 8080 端口

   ![image-20220411215821084](README.assets/image-20220411215821084.png)
   输入对应的用户密码(tomcat)即可访问管理界面

## Jenkins 构建 Maven 项目

### Jenkins 构建的项目类型介绍

jenkins 中自动构建项目的类型有很多，常用的有以下三种

- 自由风格软件项目(FreeStyle Project)
- Maven 项目(Maven Project)
- 流水线项目(Pipeline Project)

每种类型的构建其实都可以完成一样的构建过程与结果，只是操作方式，灵活度方面有所区别，在实际开发中可以根据自己的需求和习惯来选择

### 自由风格构建

> 创建一个自由风格项目来完成项目的集成过程：
>
> 拉取代码 -> 编译 -> 打包 -> 部署

1. 创建 Jenkins 任务

   ![image-20220413105459895](README.assets/image-20220413105459895.png)

2. 配置源码管理

   ![image-20220413105542503](README.assets/image-20220413105542503.png)

3. 添加构建步骤

   ```shell
   echo "开始编译和打包"
   mvn clean package
   echo "编译和打包结束
   ```

4. 部署到 **tomcat**

   1. 安装 ` Deploy to container` 插件

   2. 添加 Tomcat 用户凭证

      ![image-20220413105809393](README.assets/image-20220413105809393.png)

   3. 在任务配置中添加构建后操作

      ![image-20220413110022679](README.assets/image-20220413110022679.png)

      ![image-20220413110119470](README.assets/image-20220413110119470.png)

   4. 点击立即构建即可

   5. 构建成功后访问 http://192.168.137.82:8080/web-demo-1.0-SNAPSHOT/

      ![image-20220413113032792](README.assets/image-20220413113032792.png)

### Maven 风格构建

1. 安装 `Maven Integration` 插件

   ![image-20220413113134547](README.assets/image-20220413113134547.png)

2. 创建 Maven 风格项目

   ![image-20220413113623099](README.assets/image-20220413113623099.png)

3. 拉取 gitlab 和部署 tomcat 的过程是一样的

4. 不同的在于 mvn 构建过程

   ![image-20220413113826339](README.assets/image-20220413113826339.png)

5. 访问页面

   ![image-20220413114334189](README.assets/image-20220413114334189.png)



### Pipeline 流水线项目构建

#### Pipeline 简介

- Pipeline：指**流水线**，可以理解成一套运行在 Jenkins 上的工作流框架，将原来独立运行于单个/多个节点的任务连接起来，实现单个任务难以完成的复杂流程编排和可视化的工作

  > 例如：我们要实现单个任务可能要经过 pull, build 等等阶段，但这些阶段都是相互独立，之间并没有什么关联性，不方便控制和管理，但使用 Pipeline，就可以使这些过程关联起来，在 Jenkins 上甚至能提供一些可视化阶段帮助我们观察和管理

- 使用 Pipeline 的好处：

  1. Pipeline 以代码的形式实现，通常被检入源代码控制，使团队能够编辑，审查和迭代其传送流程
  2. 无论是计划内的还是计划外的服务器重启，Pipeline都是可恢复的。 
  3. Pipeline 可接收交互式输入，以确定是否继续执行 Pipeline
  4. Pipeline支持现实世界中复杂的持续交付要求，它支持 fork/join、循环执行，并行执行任务的功能。
  5. Pipeline 插件支持其DSL的自定义扩展 ，以及与其他插件集成的多个选项

- 如何创建 Jenkins Pipeline

  - Pipeline 脚本由 **Groovy** 语言实现
  - 有两种创建方式
    1. 直接在 Jenkins Web UI 界面中输入脚本
    2. 在源代码的目录下创建一个 `Jenkinsfile`(推荐)
  - 支持两种语法
    - 声明式
    - 脚本式

#### 安装 Pipeline 插件

![image-20220413181336718](README.assets/image-20220413181336718.png)

创建成功后在[创建任务]界面上就可以看到相关选项了

![image-20220413181757140](README.assets/image-20220413181757140.png)

#### Pipeline 语法快速入门

![image-20220413181947142](README.assets/image-20220413181947142.png)

> 声明式

```groovy
pipeline {
    agent any

    stages {
        stage('pull code') {
            steps {
                echo 'pull code'
            }
        }
        stage('build project') {
            steps {
                echo 'build project'
            }
        }
        stage('publish project') {
            steps {
                echo 'publish project'
            }
        }
    }
}
```

**stages**：代码整个流水线的所有执行流程，通常 stages 只有一个，里面包含多个 stage

**stage**：代表流水线中的某个阶段，可能出现 n 个，例如拉取代码，项目构建，部署项目等

**steps**：一个阶段内需要执行的逻辑，可能是 shell脚本/git命令/ssh远程发布 等任意内容

> 脚本式

```groovy
node {
    def mvnHome
    stage('pull code') { // for display purposes
        echo 'pull code'
    }
    stage('build project') {
        echo 'build project'
    }
    stage('publish project') {
        echo 'publish project'
    }
}
```

**node**：节点，一个 node 就是一个 jenkins 节点(Master/Slave)，是执行 Step 的具体运行环境

**stage**：流水线阶段，一个 pipeline 可以分为多个 stage，每个 stage 表示一个操作(pull/bulid/deploy等等)

**step**：步骤，最基本的运行单元，可以是打印一句话，也可以是构建一个 Docker 镜像， 由各类 Jenkins 插件提供

#### Pipeline Gitlab Code Pull

> 可以通过[流水线语法]生成对应的 pipeline steps 代码

![image-20220413185231819](README.assets/image-20220413185231819.png)

```groovy
pipeline {
    agent any

    stages {
        stage('pull code') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[credentialsId: 'd2344a4e-e819-41d4-bda4-1f8bffdd0ef9', url: 'http://192.168.137.80:88/root/web-demo.git']]])
            }
        }
    }
}
```

#### Pipeline Build & Publish

> Maven 构建脚本

```groovy
stage('maven build') {
    steps {
        sh 'mvn clean package'
    }
}
```

> deploy to tomcat

![image-20220413225615465](README.assets/image-20220413225615465.png)

```groovy
stage('project publish') {
    steps {
        deploy adapters: [tomcat8(credentialsId: 'a7922d1b-95a7-4f5f-a8e5-d05368679ff5', path: '', url: 'http://192.168.137.82:8080/')], contextPath: null, war: 'target/*.war'
    }
}
```

#### Pipeline Script Form SCM

> 通过 Jenkins 的 UI 界面去编写 Pipeline 代码，并不方便我们进行脚本维护，所以建议是将 Pipeline 脚本文件放在项目中(一起进行版本控制)

1. 在项目的根目录下创建 `Jenkinsfile` 文件(名字最好叫这个，也可以改),并将刚刚的脚本内容CV上去

   ```groovy
   pipeline {
       agent any
   
       stages {
           stage('pull code') {
               steps {
                   checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[credentialsId: 'd2344a4e-e819-41d4-bda4-1f8bffdd0ef9', url: 'http://192.168.137.80:88/root/web-demo.git']]])
               }
           }
           stage('maven build') {
               steps {
                   sh 'mvn clean package'
               }
           }
           stage('project deploy') {
               steps {
                   deploy adapters: [tomcat8(credentialsId: 'a7922d1b-95a7-4f5f-a8e5-d05368679ff5', path: '', url: 'http://192.168.137.82:8080/')], contextPath: null, war: 'target/*.war'
               }
           }
       }
   }
   ```

   ![image-20220414104145302](README.assets/image-20220414104145302.png)

2. 将代码上传到 Git 仓库

3. 修改 Jenkins Pipeline 任务的配置

   ![image-20220414104403855](README.assets/image-20220414104403855.png)

   注意：下面有一个可以修改配置文件名的输入框，如果文件名不是 `Jenkinsfile` 记得改

   ![image-20220414104449378](README.assets/image-20220414104449378.png)

4. 点击立即构建

### 常用的构建触发器

Jenkins 内置的4中构建步骤：

- 触发远程构建
- 其他工作构建后触发(Build after other projects are build)
- 定时构建(Build Periodically)
- 轮询SCM(Poll SCM)

#### 触发远程构建

![image-20220414105900199](README.assets/image-20220414105900199.png)

访问：`JENKINS_URL`/job/web-demo-pipeline3/build?token=`你刚刚设置的身份验证令牌`

刷新任务界面

![image-20220414110100609](README.assets/image-20220414110100609.png)

#### 其他工作构建后触发

先随便创建一个工作 `pre_job`

修改原工程配置

![image-20220414110327124](README.assets/image-20220414110327124.png)

保存配置后，构建 `pre_job`，刷新当前任务界面

![image-20220414110418434](README.assets/image-20220414110418434.png)

#### 定时构建

![image-20220414111130516](README.assets/image-20220414111130516.png)

定时字符串从左往右分别为：分 时 日 月 周 (有点类似于 cron，但没有秒的概念)

等待相应时间即可

一些定时表达式的例子： 

> 每30分钟构建一次：H代表形参 H/30 * * * * 10:02 10:32 
>
> 每2个小时构建一次: H H/2 * * * 
>
> 每天的8点，12点，22点，一天构建3次： (多个时间点中间用逗号隔开) 0 8,12,22 * * * 
>
> 每天中午12点定时构建一次 H 12 * * * 
>
> 每天下午18点定时构建一次 H 18 * * * 
>
> 在每个小时的前半个小时内的每10分钟 H(0-29)/10 * * * * 
>
> 每两小时一次，每个工作日上午9点到下午5点(也许是上午10:38，下午12:38，下午2:38，下午 4:38) H H(9-16)/2 * * 1-5

#### 轮询 SCM

指的是定时扫描 Git 仓库代码，如果代码有变更**才会**触发项目构建

![image-20220414112008530](README.assets/image-20220414112008530.png)

构建规则和**定时构建**相同，但需要注意的是：这次构建触发器，Jenkins会定时扫描本地整个项目的代码，增大系统的开销，不建议使用。

### Git hook 自动触发构建

#### 安装 Git hook 插件

![image-20220414112759516](README.assets/image-20220414112759516.png)

#### Jenkins 设置自动化构建

1. 修改 Jenkins 系统配置，允许接收外部请求(一般公司里应该是要勾的然后通过 Gitlab connections 进行配置，但这里是为了方便学习就不那么麻烦了)

   ![image-20220414113509886](README.assets/image-20220414113509886.png)

2. 修改 Jenkins 任务配置

   ![image-20220414113724885](README.assets/image-20220414113724885.png)

#### Gitlab 设置 webhook

1. 修改 Gitlab 系统配置

   ![image-20220414113856387](README.assets/image-20220414113856387.png)

2. 修改项目配置(记得保存)

   ![image-20220414114045825](README.assets/image-20220414114045825.png)

3. 更新代码，重新 push，观察 tomcat 服务器

   ![image-20220414114304757](README.assets/image-20220414114304757.png)

   ![image-20220414114317184](README.assets/image-20220414114317184.png)



### Jenkins 参数化构建

> 应用场景：有时候我们在进行项目构建的过程中，我们需要根据用户的输入动态传入一些参数，从而影响整个构建结果，这时我们可以使用参数化构建。
>
> 这里演示根据用户不同输入拉取部署不同分支的代码

1. 创建分支，并推送到 gitlab 上(这里写错了，先进行第 3 步才对)

   通过 idea 创建一个新分支，然后修改代码，并推送到 gitlab 即可

2. 在 Jenkins 中添加字符串类型参数

   ![image-20220414115927531](README.assets/image-20220414115927531.png)

3. 修改 Jenkinsfile

   ![image-20220414120059743](README.assets/image-20220414120059743.png)

4. 返回 Jenkins 任务详情点击 [Build With Paramaters]

   ![image-20220414120459597](README.assets/image-20220414120459597.png)

5. 刷新 Tomcat

### 配置邮件服务器发送构建结果

**lepu****nxhu****taaw****ebje**

1. 安装 Email Extension Template 插件

   ![image-20220414141659517](README.assets/image-20220414141659517.png)

2. 开启邮箱的 SMTP 配置并获取授权码

   ![image-20220414142004140](README.assets/image-20220414142004140.png)

3. 更改 Jenkins 配置[系统管理 -> 系统配置]

   - 修改 **Jenkins Location**

     ![image-20220414142308046](README.assets/image-20220414142308046.png)

   - 修改插件配置 **Extended E-mail Notification**

     ![image-20220414143009878](README.assets/image-20220414143009878.png)

     ![image-20220414143029768](README.assets/image-20220414143029768.png)

   - 修改 **邮件通知**

     ![image-20220414142709335](README.assets/image-20220414142709335.png)

4. 准备邮件内容

   在项目根目录下创建 `email.html` 作为邮件发送模板

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <meta charset="UTF-8">
       <title>${ENV, var="JOB_NAME"}-第${BUILD_NUMBER}次构建日志</title>
   </head>
   
   <body leftmargin="8" marginwidth="0" topmargin="8" marginheight="4"
         offset="0">
   <table width="95%" cellpadding="0" cellspacing="0"  style="font-size: 11pt; font-family: Tahoma, Arial, Helvetica, sans-serif">
       <tr>
           <td>以下为${PROJECT_NAME }项目构建信息</td>
       </tr>
       <tr>
           <td><br />
               <b><font color="#0B610B">构建信息</font></b>
               <hr size="2" width="100%" align="center" /></td>
       </tr>
       <tr>
           <td>
               <ul>
                   <li>项目名称：${PROJECT_NAME}</li>
                   <li>构建编号：第${BUILD_NUMBER}次构建</li>
                   <li>触发原因：${CAUSE}</li>
                   <li>构建状态：${BUILD_STATUS}</li>
                   <li>构建日志：<a href="${BUILD_URL}console">${BUILD_URL}console</a></li>
                   <li>构建Url：<a href="${BUILD_URL}">${BUILD_URL}</a></li>
                   <li>工作目录：<a href="${PROJECT_URL}ws">${PROJECT_URL}ws</a></li>
                   <li>项目Url：<a href="${PROJECT_URL}">${PROJECT_URL}</a></li>
                   <li>SonarQube结果：http://sonar****** </li>
                   <li>代码覆盖率结果：http://**/job/${PROJECT_NAME}/${BUILD_NUMBER}/jacoco/  </li>
               </ul>
           </td>
       </tr>
       <tr>
           <td><b><font color="#0B610B">历史变更记录:</font></b>
               <hr size="2" width="100%" align="center" /></td>
       </tr>
       <tr>
           <td>
               ${CHANGES_SINCE_LAST_SUCCESS,reverse=true, format="Changes for Build #%n:<br />%c<br />",showPaths=true,changesFormat="<pre>[%a]<br />%m</pre>",pathFormat="&nbsp;&nbsp;&nbsp;&nbsp;%p"}
           </td>
       </tr>
   </table>
   </body>
   </html>
   ```

   PS：邮件相关全局参数参考列表:系统设置->Extended E-mail Notification->Content Token Reference，点击旁边的?号

   ![image-20220414145639984](README.assets/image-20220414145639984.png)

5. 修改 Jenkinsfile 文件

   ```groovy
   pipeline {
       agent any
   
       stages {
           stage('pull code') {
               steps {
                   checkout([$class: 'GitSCM', branches: [[name: '*/${branch}']], extensions: [], userRemoteConfigs: [[credentialsId: 'd2344a4e-e819-41d4-bda4-1f8bffdd0ef9', url: 'http://192.168.137.80:88/root/web-demo.git']]])
               }
           }
           stage('maven build') {
               steps {
                   sh 'mvn clean package'
               }
           }
           stage('project deploy') {
               steps {
                   deploy adapters: [tomcat8(credentialsId: 'a7922d1b-95a7-4f5f-a8e5-d05368679ff5', path: '', url: 'http://192.168.137.82:8080/')], contextPath: null, war: 'target/*.war'
               }
           }
       }
       // 在构建完成后进行相关操作
       post {
           // 无论是否构建成功都进行相关操作
           always {
               emailext(
                   subject: '构建通知：${PROJECT_NAME} - Build # ${BUILD_NUMBER} - ${BUILD_STATUS}!',
                   body: '${FILE,path="email.html"}',
                   to: '2391105059@qq.com'
               )
           }
       }
   }
   ```

   tips: 关于声明式脚本的代码结构可以在下图中的位置配置

   ![image-20220414144037337](README.assets/image-20220414144037337.png)

   ![image-20220414144103099](README.assets/image-20220414144103099.png)

6. 将代码 push gitlab 后在 Jenkins 进行构建部署查看邮件

   ![image-20220414144948409](README.assets/image-20220414144948409.png) 

   

## Jenkins + Docker + SpringCloud 微服务持续集成







## 基于 Kubernetes/K8S 构建 Jenkins 持续集成平台 



