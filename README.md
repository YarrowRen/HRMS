# HRMS

基于SSM的简单人事管理系统

## 概述
随着互联网行业的发展，众多企业通过在线管理网站的方式对企业员工及开发项目进行管理，传统的企业管理应用暴露出了安全性差，难于维护扩展，不便于进行大型数据处理等问题，进一步增强了企业采用管理网站进行公司管理的趋势。

该项目前端采用了Bootstrap开源包进行网页前端界面的设计与美化，后端采用SSM（Spring,SpringMVC,MyBatis）框架进行业务逻辑的处理。
后端业务逻辑的实现采用了传统的MVC模式，将实现分为三部分，Mapper层（DAO层或持久层）负责基本的数据库查询语言的调用，实现对数据库的增删改查基本操作，Service层（服务层）基于Mapper层提供的服务，对数据进行处理（对象的合理映射，条件处理），并对可能在持久层发生的错误进行处理或继续交由上层处理，Controller层负责利用Service层提供的服务实现业务逻辑的实现，以及对前端提供的数据进行处理或向前端页面传递数据

前端页面部分美化利用到了开源的“AdminLTE2模块化前端模板”，其余部分及后端全部代码内容完全由本人独立完成

## 需求分析
企业管理工具的需求从企业这个概念诞生的一刻就开始存在，如何合理的对一个可能从几十人到几百的团队进行合理的管理，如何高效的规划项目控制流程，如何针对性的对企业员工进行业务分发都是一个优秀的企业管理工具需要考量的方向。
而几年前流行的通过应用软件进行企业管理的方式在多年的使用中也暴露出了许多难以解决的问题。

首先是界面管理的问题，应用软件从始至终采用一整套同样的图形化界面库，无论是要对界面进行进一步美观还是更换界面库，都是一项耗时巨大，劳心费力的活动，但反观利用网络应用进行企业管理，则可以轻松通过对前端页面模板的更换或是修改进行优化，以较低的成本实现前后端的分离。

其次是应用程序适配性差，无论是对显示屏分辨率的要求还是对系统版本的要求都较为苛刻，在公司这种集体办公的场所也许还比较容易处理，但员工在非公司地点（如居家办公，外地出差）情况下，应用程序的弱适配性可能直接导致软件的体验变差甚至无法运行，相比较而言，网络应用则可以基本保证对各种设备的适配，并且bootstrap框架本身设计概念中就针对不同设备进行不同配置，保证了在全平台的通用性

综上所述，利用网络应用进行企业管理的前景非常巨大，并且近年来也可以看到许多企业确实采用了这种方式进行对员工以及业务的管理

## 数据结构设计
企业管理系统设计过程中，最重要的结构设计是对企业信息数据库的设计，包括了如何对员工信息进行归类，如何对职务信息进行处理以及部门之间的关系的解决，该项目采用了六张数据表分别对企业信息进行分类

1. 员工基本信息表sys_user
该表由五个数据段组成，分别是ID对应员工的工作ID，为该表主键，采用自增方式生成，是员工的唯一标识信息。username为员工的用户名，为字符串类型，允许重复，不为空。email表示员工工作邮箱，可为空。password表示员工的登录密码，不可置空。phoneNum表示员工的电话号码，可为空
2.	职务信息表sys_role
该表由三个数据段组成，ID是职业的唯一标识符，自增主键，不可为空。roleName表示该职务的名称，不可为空。roleDesc表示职务的基本情况，可为空。
3.	部门信息表sys_dept
该表由三个字段组成，ID是唯一表示部门信息的数据，deptName表示部门名称，不可为空。deptDesc表示部门简介，可为空
4.	日志表sys_log
记录员工登录日志的表格，该表由四个字段组成，均不可为空。ID表示该日志记录的唯一标识，accessTime表示登录时间，userId表示登录用户的ID，ipAddress表示用户登陆时所用IP地址

由于职务信息与员工的对应关系是多对多关系，职务关系与部门的对应关系是一对多的关系，所以这里需要两张中间表来维系它们之间的关系

5.	员工信息与职务信息中间表sys_user_role
该表共两个字段，userId外键为sys_user表的id数据段，标识了用户ID，roleId外键为sys_role表的id数据段，标识了职务ID
6.	职务信息与部门信息中间表sys_role_dept
该表共两个字段，roleId外键为sys_role表的id数据段，标识了职务ID，deptId外键为sys_dept表的id数据段，标识了部门ID


## 算法设计的思想、流程图、分析：
该项目使用SSM框架实现后端逻辑结构，Spring负责抽取复杂的业务对象或工具类，负责项目中绝大多数常用对象的控制权，SpringMVC负责实现Web的MVC设计模式，对原先的Web层进行解耦并将项目分为三层，模型层封装了项目常用的POJO，视图层负责呈现业务模型与视图，控制层负责处理用户逻辑请求。MyBatis负责简化持久层的代码，将原先在Java类中编辑的SQL语句抽取到配置文件中进行编辑，实现了二者的解耦。项目中Spring,SpringMVC大部分配置采用了注解配置的方式，MyBatis中则采用了配置文件配置的方式（在多表查询的部分SQL语句较为复杂，使用注解配置不便于值观查看）。

用户在访问网站时首先进入主页mian.jsp此时用户通过按钮进行人机交互，前端主页各个按钮或搜索框是后端进入的入口，用户点击按钮跳转到后端Controller层执行业务逻辑，Controller层负责处理从前端获取的用户提交的信息的处理，并执行具体业务模块的流程控制，调用Service层的接口实现业务流程，Service层负责模型的封装以及在持久层不进行的较为复杂的逻辑操作，同时调用DAO层提供的方法进行实现。DAO层负责单纯的数据库操作，对数据库进行增删改查，由于使用MyBatis框架进行数据库管理，所以代码中只实现Mapper的接口类，所有SQL语句的编写配置在对应的配置文件中进行。

下图是网页应用运行过程中的逻辑流程，用户首先通过URL访问网页主页main.jsp，进入主页后用户点击按钮作为程序执行入口进入Controller层，Controller层根据用户需求进行逻辑操作并调用对应的Service层服务进行操作，随后Service层调用DAO层执行数据库操作并返回结果集或执行结果，最后Controller层返回视图，功能页面再进行结果的展示，将结果反馈给用户。用户接收到结果，结束操作或继续进行页面的交互。

![](https://ywrbyimg.oss-cn-chengdu.aliyuncs.com/img/shujujiegou10.png)

## 页面展示

![](https://ywrbyimg.oss-cn-chengdu.aliyuncs.com/img/shujujiegou1.png)
![](https://ywrbyimg.oss-cn-chengdu.aliyuncs.com/img/shujujiegou2.png)
![](https://ywrbyimg.oss-cn-chengdu.aliyuncs.com/img/shujujiegou3.png)
![](https://ywrbyimg.oss-cn-chengdu.aliyuncs.com/img/shujujiegou4.png)
![](https://ywrbyimg.oss-cn-chengdu.aliyuncs.com/img/shujujiegou6.png)
![](https://ywrbyimg.oss-cn-chengdu.aliyuncs.com/img/shujujiegou8.png)
