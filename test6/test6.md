# 实验6（期末考核） 基于Oracle的`某项目`数据库设计

- 期末考核要求
  - 自行设计一个信息系统的数据库项目，自拟`某项目`名称。
  
  - 设计项目涉及的表及表空间使用方案。至少5张表和5万条数据，两个表空间。
  
  - 设计权限及用户分配方案。至少两类角色，两个用户。
  
  - 在数据库中建立一个程序包，在包中用PL/SQL语言设计一些存储过程和函数，实现比较复杂的业务逻辑，用模拟数据进行执行计划分析。
  
  - 设计自动备份方案或则手工备份方案。
  
  - 设计容灾方案。使用两台主机，通过DataGuard实现数据库整体的异地备份(可选)。
  
    

# 表设计

1.1 数据表设计

（1）商品表



| 属性名      | 数据类型      | 是否为空 | 主键/外键 | 备注           |
| ----------- | ------------- | -------- | --------- | -------------- |
| goods_id    | varchar2(10)  | 否       | 主键      | 商品id         |
| goods_name  | varchar2(50)  | 否       |           | 商品名称       |
| class_id    | varchar2(10)  | 否       | 外键      | 商品类别表外键 |
| goods_price | number(10,2)  | 否       |           | 不能为负       |
| goods_info  | varchar2(100) | 否       |           | 商品介绍       |

 

（2）商品库存表



| 属性名       | 数据类型      | 是否为空 | 主键/外键 | 备注       |
| ------------ | ------------- | -------- | --------- | ---------- |
| inventory_id | varchar2 (10) | 否       | 主键      | 库存id     |
| goods_id     | varchar2 (10) | 否       | 外键      | 商品表外键 |
| quantity     | number(10,0)  | 否       |           | 库存数量   |
| position     | varchar2 (50) | 否       |           | 分表       |

 

（3）商品分类表

​               商品分类表（class）



| 属性名     | 数据类型      | 是否为空 | 主键/外键 | 备注     |
| ---------- | ------------- | -------- | --------- | -------- |
| class_id   | varchar2 (10) | 否       | 主键      | 分类id   |
| class_name | varchar2 (50) | 否       |           | 分类名称 |

 

（4）管理员表



| 属性名         | 数据类型      | 是否为空 | 主键/外键 | 备注         |
| -------------- | ------------- | -------- | --------- | ------------ |
| admin_id       | varchar2 (10) | 否       | 主键      | 管理员id     |
| admin_username | varchar2 (20) | 否       |           | 管理员用户名 |
| admin_password | varchar2(20)  | 否       |           | 管理员密码   |
| admin_sex      | varchar2 (10) | 否       |           | 男,女,未知   |

 

（5）用户表



| 属性名   | 数据类型      | 是否为空 | 主键/外键 | 备注     |
| -------- | ------------- | -------- | --------- | -------- |
| user_id  | varchar2 (10) | 否       | 主键      | 用户id   |
| username | varchar2 (20) | 否       |           | 用户名   |
| password | varchar2 (20) | 否       |           | 密码     |
| name     | varchar2 (20) | 否       |           | 用户名称 |
| sex      | varchar2(10)  | 否       |           | 性别     |
| phone    | number(11,0)  | 否       |           | 手机号码 |

 

（6）订单表

​               订单表（order）



| 属性名      | 数据类型       | 是否为空 | 主键/外键 | 备注     |
| ----------- | -------------- | -------- | --------- | -------- |
| order_id    | varchar2(10)   | 否       | 主键      | 订单id   |
| order_date  | date           | 否       |           | 下单时间 |
| order_price | number (10,2)  | 否       |           | 订单价格 |
| order_info  | varchar2 (100) | 否       |           | 订单详情 |
| user_id     | varchar2 (10)  | 否       | 外键      | 用户id   |
| class_id    | varchar2 (10)  | 否       | 外键      | 类别id   |

 



1.2 表空间设计

（1）创建数据库以及管理员

![img](file:///C:/Users/贺巍/AppData/Local/Temp/msohtmlclip1/01/clip_image002.jpg)

![img](file:///C:/Users/贺巍/AppData/Local/Temp/msohtmlclip1/01/clip_image004.jpg)

（2）创建表空间

![img](file:///C:/Users/贺巍/AppData/Local/Temp/msohtmlclip1/01/clip_image006.jpg)

![img](file:///C:/Users/贺巍/AppData/Local/Temp/msohtmlclip1/01/clip_image008.jpg)

(3)创建表

create table class (

  class_id varchar2 (10) not null primary key,

  class_name varchar2 (50) not null,

)TABLESPACE lz_space1;

 

create table goods (

  goods_id varchar2(10) not null primary key,

  goods_name varchar2(50) not null,

  goods_price float not null CHECK(goods_price > 0),

  commodity_info varchar2(100) not null,

  class_id varchar2(10) not null,

  CONSTRAINT class_id_lz FOREIGN KEY (class_id) REFERENCES class (class_id)

)TABLESPACE lz_space1;

 

create table user (

  user_id varchar2(10) not null primary key,

  username varchar2(20) not null,

  password varchar2(20) not null,

  name varchar2(20) not null,

  sex varchar2(10) not null,

  phone number(11,0)

)TABLESPACE lz_space1;

 

create table admin (

  admin_id varchar2 (10) not null primary key,

  admin_username varchar2 (20) not null,

  admin_password varchar2 (20) not null,

  admin_sex varchar2 (10) not null check(admin_sex in ('男','女')),

)TABLESPACE lz_space1;

 

create table inventory (

  inventory_id varchar2(10) not null primary key,

  goods_id varchar2(10) not null,

  quantity number(10,0),

  position varchar2(50),

  CONSTRAINT inventory_goods_id FOREIGN KEY (goods_id) REFERENCES goods (goods_id)

)TABLESPACE lz_space1;

 

CREATE TABLE order (

 order_id varchar2(10) NOT NULL primary key, 

 order_info varchar2(100) NOT NULL, 

 order_price number(10,2) NOT NULL,

 user_id char(10),

 classr_id char(10),

 order_date date,

 CONSTRAINT order_class_id FOREIGN KEY (class_id) REFERENCES class (class_id),

 CONSTRAINT order_user_id FOREIGN KEY (user_id) REFERENCES user (user_id)

) partition by range (order_date)

 (

  partition p1 values LESS THAN (TO_DATE(' 2021-01-01 00:00:00', 'SYYYY-MM-DD HH24:MI:SS', 'NLS_CALENDAR=GREGORIAN')) tablespace lz_space1,

  partition p2 values LESS THAN (TO_DATE(' 2022-01-01 00:00:00', 'SYYYY-MM-DD HH24:MI:SS', 'NLS_CALENDAR=GREGORIAN')) tablespace lz_space2,

  partition p3 values LESS THAN (TO_DATE(' 2023-01-01 00:00:00', 'SYYYY-MM-DD HH24:MI:SS', 'NLS_CALENDAR=GREGORIAN')) tablespace lz_space3,

  partition p4 values LESS THAN (MAXVALUE) tablespace lz_space4

 )



 

# 2 用户管理

2.1创建用户,授权,插入数据，以及对分区，授权的验证

  （1）分别创建两个用户lz_user1和lz_user2,并允许他们是使用lz_space1表空间

![img](file:///C:/Users/贺巍/AppData/Local/Temp/msohtmlclip1/01/clip_image010.jpg)

（2）创建两个角色lz_role1，lz_role2，并分别赋予lz_role1读任何表和读，lz_role2读任何表和读、修改任何表的权利；

![img](file:///C:/Users/贺巍/AppData/Local/Temp/msohtmlclip1/01/clip_image012.jpg)

（3）将lz_role1，lz_role2分别赋予给用户lz_user1，lz_user2

  ![img](file:///C:/Users/贺巍/AppData/Local/Temp/msohtmlclip1/01/clip_image014.jpg)

（4）向表中插入数据：

declare

  dt date;

  order_id varchar2(10);

  order_info varchar2(100);

  order_price number (10,2);

  class_id varchar2(10);

  user_id char(5);

begin

  insert into class(class_id,class_name) values ('1','食品');

  insert into class(class_id,class_name) values ('2','服饰');

  insert into class(class_id,class_name) values ('3','数码');

  

  Insert into goods(goods_id,goods_name,goods_price,class_id) values ('101','手撕面包',20,'1');

  insert into goods(goods_id,goods_name,goods_price,class_id) values ('102','坚果',29,'1');

  insert into goods(goods_id,goods_name,goods_price,class_id) values ('103','T恤',65,'2');

  insert into goods(goods_id,goods_name,goods_price,class_id) values ('104','短裤',88,'2');

  insert into goods(goods_id,goods_name,goods_price,class_id) values ('105','iphone11',4999,'3');

  insert into goods(goods_id,goods_name,goods_price,class_id) values ('106','小米11',3999,'3');

  

  insert into admin(admin_id,admin_sex,admin_username,admin_password) 

  values ('1','男','0101','123456');

  

  insert into user(user_id,user_name,user_sex,user_phone) values ('1','hw','男','17369066636');

  

  for i in 1..50000

  loop

​    if i mod 3=0 then

​    dt := to_date('2018-01-01','yyyy-mm-dd')+(i mod 60);

  --PARTITION_2018

​    elsif i mod 6 = 1 then

​    dt := to_date('2019-01-01','yyyy-mm-dd')+(i mod 60);

  --PARTITON_2019

​    elsif i mod 6 = 2 then

​    dt := to_date('2020-01-01','yyyy-mm-dd')+(i mod 60);

  --PARTITION_2020

​    end if;

  --开始向order表里插入数据

​    order_id := i;

​    user_id := '1';

​    class_id := '1';

​    order_info := '订单详情';

​    order_price := dbms_random.value(5,30);

​    

​    insert into orders (order_id,order_info,order_price,user_id,class_id)

​    values (order_id,order_info,order_price,user_id,class_id);

​    

  end loop;

end;

（5）之前创建了两个用户，一个是lz_user1,一个是lz_user2，并且给lz_user1和lz_user2分别授予了查看表，查看表以及更新表的权限，接下来进行验证：

![img](file:///C:/Users/贺巍/AppData/Local/Temp/msohtmlclip1/01/clip_image016.jpg)

首先，让lz_user1连接数据库lzpdb,让其查看lz的ORDER表，如下图所示，是可以看见的。

![img](file:///C:/Users/贺巍/AppData/Local/Temp/msohtmlclip1/01/clip_image018.jpg)

然后，让lz_user1更新一条数据，会发现其权限不足，由此可以证明，授权成功。

![img](file:///C:/Users/贺巍/AppData/Local/Temp/msohtmlclip1/01/clip_image020.jpg)

接着对lz_user2(之前赋予了更新和查看权限）用户查看和更新lz的ORDERS表，发现均成功，但让其删除一条记录是会显示权限不足，由此可证明，授权无误。

![img](file:///C:/Users/贺巍/AppData/Local/Temp/msohtmlclip1/01/clip_image022.jpg)

![img](file:///C:/Users/贺巍/AppData/Local/Temp/msohtmlclip1/01/clip_image024.jpg)

![img](file:///C:/Users/贺巍/AppData/Local/Temp/msohtmlclip1/01/clip_image026.jpg)

（1）根据REGISTRATION_FORM表中的REGISTRATION_DATE字段根据时间段的不同，将其放在了不同的表空间里面，由下图可知，每个表空间里面均有REGISTRATION_FORM的数据，也可证明分区成功。

![img](file:///C:/Users/贺巍/AppData/Local/Temp/msohtmlclip1/01/clip_image028.jpg)

![img](file:///C:/Users/贺巍/AppData/Local/Temp/msohtmlclip1/01/clip_image030.jpg)

![img](file:///C:/Users/贺巍/AppData/Local/Temp/msohtmlclip1/01/clip_image032.jpg)

![img](file:///C:/Users/贺巍/AppData/Local/Temp/msohtmlclip1/01/clip_image034.jpg)



 

# 3 PL/SQL设计

3.1 程序包的建立

建立一个程序包，里面有一个函数和一个存储过程，其功能分别为：统计一段时间内的订单总收入；统计一段时间内，各个导购员的接待数。具体过程如下：

create or replace PACKAGE MyPack is

  function get_total(dt1 char,dt2 char) return number;

  procedure get_people(dt1 char,dt2 char);

END MyPack;

/

create or replace PACKAGE BODY MyPack is

function get_total(dt1 char,dt2 char) return number

  AS

​    n number;

​    begin

​      select sum(order_price) into n from orders where order_date >=to_date(dt1,'yyyy-mm-dd hh24:mi:ss')

​      and order_date<=to_date(dt2,'yyyy-mm-dd hh24:mi:ss');

​    return n;

  end;

 

procedure get_people(dt1 char,dt2 char)

  AS

​    a number;

​    b number;

​    cursor people is

​      select * from orders where order_date>= to_date(dt1,'yyyy-mm-dd hh24:mi:ss')

​      and order_date<= to_date(dt2,'yyyy-mm-dd hh24:mi:ss');

​    begin

​      a:=0;

​      b:=0;

​      for v in people

​      loop

​        if v.class_id = '1'

​          then a:= a+1;

​        elsif v.class_id = '2'

​          then b:= b+1;

​        end if;

​      end loop;

​      dbms_output.put_line('零食类售出数量为:'|| a);

​      dbms_output.put_line('服饰类售出数量为:'||b);

  end;

END MyPack;

![img](file:///C:/Users/贺巍/AppData/Local/Temp/msohtmlclip1/01/clip_image036.jpg)

 接下来是效果展示：

函数效果展示

![img](file:///C:/Users/贺巍/AppData/Local/Temp/msohtmlclip1/01/clip_image038.jpg)

存储过程效果展示

![img](file:///C:/Users/贺巍/AppData/Local/Temp/msohtmlclip1/01/clip_image040.jpg)



 

# 4备份方案

4.1 系统定时自动备份

（1）编写rman增量备份脚本

\#rman_level1.sh 

\#!/bin/sh

  export NLS_LANG='SIMPLIFIED CHINESE_CHINA.AL32UTF8'

  export ORACLE_HOME=/home/oracle/app/oracle/product/12.1.0/dbhome_1 

  export ORACLE_SID=orcl 

  export PATH=$ORACLE_HOME/bin:$PATH 

  rman target / nocatalog msglog=/home/oracle/rman_backup/lv1_`date +%Y%m%d-%H%M%S`_L0.log << EOF

  run{

  configure retention policy to redundancy 1;

  configure controlfile autobackup on;

  configure controlfile autobackup format for device type disk to '/home/oracle/rman_backup/%F';

  configure default device type to disk;

  crosscheck backup;

  crosscheck archivelog all;

  allocate channel c1 device type disk;

  backup as compressed backupset incremental level 1 database format '/home/oracle/rman_backup/dblv1_%d_%T_%U.bak'

  plus archivelog format '/home/oracle/rman_backup/arclv1_%d_%T_%U.bak';

  report obsolete;

  delete noprompt obsolete;

  delete noprompt expired backup;

  delete noprompt expired archivelog all;

  release channel c1;

}

EOF

exit

（2）启动linux的crontab定时任务，每天的凌晨一点自动进行备份

![img](file:///C:/Users/贺巍/AppData/Local/Temp/msohtmlclip1/01/clip_image042.jpg)

（3）演示备份与恢复：

 

（3.1）执行rman_level1.sh脚本，进行数据库备份

![img](file:///C:/Users/贺巍/AppData/Local/Temp/msohtmlclip1/01/clip_image044.jpg)

（3.2）删除数据库

（3.3）启用rman，进行数据库恢复

![img](file:///C:/Users/贺巍/AppData/Local/Temp/msohtmlclip1/01/clip_image046.jpg)

![img](file:///C:/Users/贺巍/AppData/Local/Temp/msohtmlclip1/01/clip_image048.jpg)

（3.4）查看删除的是否恢复

![img](file:///C:/Users/贺巍/AppData/Local/Temp/msohtmlclip1/01/clip_image050.jpg)

（3.5）恢复成功

 

 