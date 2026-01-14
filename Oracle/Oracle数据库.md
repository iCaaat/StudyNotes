# 基本概念

## 1.实例

一个Oracle实例有一系列后台进程和内存结构组成，一个数据库可以有多个实例，一般来说，单机Oracle一个数据库只有一个实例（一般情况）；RAC集群模式下采用多实例。

如ORCL

* `.CTL`：控制文件（最小，最重要）
* `.LOG`：重做日志文件（记录数据变化，提供恢复机制）、归档日志文件（重做日志的历史备份，归档、非归档模式）
* `.DBF`：数据文件

数据文件

* 系统数据文件（`SYSTEM01.DBF`和`SYSAUX01.DBF`）
* 回滚数据文件（`UNDOTBS01.DBF`）
* 用户数据文件（`USERS01.DBF`、`TBSP_1.DBF`）
* 临时数据文件（`TEMP02.DBF`)

其他文件：

* 服务器参数文件（orcl.ora）
* 密码文件（PWDorcl.ora）
* 警告文件（alert_orcl.log）
* 跟踪文件

> # 扩展
>
> ## 1. 实例（Instance）
>
> - **是什么**：一组内存（SGA）+ 后台进程（DBWR、LGWR、SMON…）。
> - **作用**：实例负责“跑起来”，把数据读写到物理文件中。
> - **特点**：实例本身不存数据，关闭实例数据还在。
>
> 👉 类比：实例就是“物业团队”，他们不拥有房子，但掌管运行。
>
> ------
>
> ## 2. 数据库（Database）
>
> - **是什么**：一组物理文件的集合（数据文件 *.dbf*、控制文件 *.ctl*、日志文件 *.log*）。
> - **作用**：存储真正的数据和元数据。
> - **特点**：数据库是“静态的存储”，必须靠实例来“活起来”。
>
> 👉 类比：数据库是“小区建筑+地基”，没有物业（实例），小区就死气沉沉。
>
> ------
>
> ## 3. 实例与数据库的关系
>
> - 最常见：**一个实例 ↔ 一个数据库**（单机模式）。
> - 特殊情况（RAC 集群）：**多个实例 ↔ 一个数据库**，多个节点的实例共享同一组数据文件。
> - 不存在：一个实例管理多个数据库。
>
> ------
>
> ## 4. CDB 与 PDB（多租户模式）
>
> - **CDB（Container Database）**：是一个数据库的形式（包含根容器 + 种子库 + 若干 PDB）。
> - **PDB（Pluggable Database）**：挂在 CDB 下的“子库”，给应用单独用。
> - **一个实例只能挂一个 CDB**，但这个 CDB 里面可以有很多个 PDB。
>
> 👉 换句话说：
>
> - “数据库” = “CDB + PDB 架构下的一整套物理文件”。
> - **CDB 和 PDB 是数据库内部的逻辑划分，不是实例。**
>
> **PDB存放位置**：
>
> * PDB的数据文件存放在哪里，是DBA创建时指定的
>
> * 并**不是必须在`ORADATA\ORCL`下
>
> * 常见做法：
>
>   * 为了规范和集中管理，DBA 通常把所有 PDB 的数据文件放在 `ORADATA\ORCL\pdbname\` 目录下。
>
>   * 但你完全可以放到别的磁盘或目录，比如：
>
>     ```sql
>     CREATE PLUGGABLE DATABASE testpdb
>       ADMIN USER pdbadmin IDENTIFIED BY 123456
>       FILE_NAME_CONVERT = ('D:\ORADATA\ORCL\pdbseed\', 'E:\MYDATA\TESTPDB\');
>     ```
>
> ------
>
> ## 5. ORCL 究竟是什么？
>
> 在你安装 Oracle 时：
>
> - 默认会创建一个 **数据库**，名字一般是 **ORCL**。
> - 这个数据库是 **一个 CDB**（根容器 = CDB$ROOT），里面可以再建 PDB。
> - 同时会启动一个 **实例**，名字也叫 ORCL（实例名通常和数据库名相同，但它们是不同概念）。
>
> 👉 所以 ORCL 既可能指：
>
> - **数据库名**（一组物理文件）。
> - **实例名**（内存+进程）。
>    实际中 DBA 会根据上下文区分。

## 2.数据文件（dbf）

数据文件是数据库的物理存储单位。数据库的数据是存储在表空间中，真正是在某一个或者多个数据库文件，而一个表空间可以由一个或多个数据文件组成，一个数据文件只能属于一个表空间。一旦数据文件被加入到某个表空间后，就不能删除这个文件，如果要删除某个数据文件，只能删除其所属于的表空间才行。

## 3.表空间

表空间是Oracle对物理数据库上相关数据文件（ORA或者DBF文件）的逻辑映射。一个数据库在逻辑上被划分成一到多个表空间，每个表空间包含了在逻辑上相关联的一组结构，每个数据库至少有一个表空间（system表空间）

每个表空间由同一磁盘上的一个或多个文件组成，这些文件叫数据文件（datafile），一个数据文件只能属于一个表空间。

表的数据，是有用户放入某一表空间的，而这个表空间会随机把这些表数据放到一个或多个数据文件中。由于Oracle的数据库不是普通的概念，Oracle是有用户和表空间对数据进行管理和存放的，但是表不是由表空间去查询的，而是由用户去查的。因为不同用户可以在同一个表空间建立同一个名字的表，这里区分就是用户了。

## 4.用户(Schema)

用户是在表空间下建立的。用户登录后只能看到和操作自己的表，Oracle的用户与MySQL的数据库类似，每建立一个应用需要创建一个用户。

## 5.Oracle服务器结构

Oracle服务器主要由以下部分组成：

* 实例
* 数据库
* 程序全局区（PGA）
* 前台进程

系统全局区（SGA）

* 高速数据缓冲区：用来存放Oracle系统最近访问过的数据块

* 共享池：存储最近执行过的SQL语句和最近使用过的数据定义，包括库高速缓冲区和字典高速缓冲区

程序全局区PGA

* 私有SQL区
* 会话区

前台进程：

* 用户进程：使用SQL Plus连接成功后生成。包含两个重要概念：连接和会话
* 服务器进程：处理用户会话过程中的SQL语句和SQL Plus命令

后台进程：

* 数据写入进程
* 检查点进程
* 日志写入进程
* 归档进程

## 6.数据字典

Oracle存储数据库内部信息的地方，描述数据库内部运行和管理情况。名称由前缀和后缀组成：

* dba_：包含数据库实例的所有对象信息
* v$_：当前实例的动态视图，包含系统管理和系统优化等使用的视图
* user_：记录用户的对象信息
* gv_：分布式环境下所有实例的动态视图，包含系统管理和系统优化使用的视图
* all_：记录用户的对象信息和被授权访问的对象信息

---

---

# Oracle数据库的管理

安装略

## 1.Oracle数据库创建

### 1.1 创建新PDB容器

启动DBCA工具：

```bash
dbca
```

向导步骤：

* 选择管理可插接式数据库
* 新增可插接式数据库
* ...

---

### 1.2 创建表空间

```plsql
CREATE TABLESPACE notecloud_data 
	DATAFILE 'D:\Oracle\oradata\ORCL\notecloudkf\notecloud_data01.dbf'
	SIZE 512M
	AUTOEXTEND ON
	NEXT 100M
	MAXSIZE UNLIMITED;
```

* **表空间的归属由容器上下文决定（执行该创建语句时所在的实例）**，而不是由路径决定。
* **文件路径是 DBA 自己组织的目录结构**（为了管理方便，常见做法是每个 PDB 一个目录）。

**查看所有表空间**：

```sql
SELECT tablespace_name, status, contents, logging
FROM dba_tablespaces;
```

- `tablespace_name`：表空间名（比如 SYSTEM、USERS、UNDO 表空间等）。
- `status`：ONLINE / OFFLINE。
- `contents`：是 PERMANENT（永久表空间）、UNDO（回滚表空间）还是 TEMPORARY（临时表空间）。
- `logging`：是否启用日志。

**查看表空间对应的数据文件**：

```sql
SELECT tablespace_name, file_name, bytes/1024/1024 AS size_MB
FROM dba_data_files
ORDER BY tablespace_name;
```

**查看临时表文件**：

```sql
SELECT tablespace_name, file_name, bytes/1024/1024 AS size_MB
FROM dba_temp_files;
```

**查看某个用户默认在哪个表空间**：

```sql
SELECT username, default_tablespace
FROM dba_users;
```

---

### 1.3 创建用户并授权

❗注：创建用户时若未加**双引号**，**用户名**不区分大小写，实际存的为大写，登录时也不区分大小写，密码从11g起默认**区分大小写**。若使用双引号，则登录、删除等操作时也需使用**双引号**，且区分大小写。

```plsql
-- 创建用户
CREATE USER notecloud IDENTIFIED BY notecloud
DEFAULT TABLESPACE NOTECLOUD_DATA     -- 默认存放数据的表空间
TEMPORARY TABLESPACE TEMP    -- 临时表空间，用于排序等操作，TEMP为O自带的
QUOTA UNLIMITED ON NOTECLOUD_DATA    -- 在表空间里不限空间配额
profile DEFAULT;    -- 用系统默认的密码策略 & 资源限制

GRANT CONNECT, RESOURCE TO notecloud;    -- 给用户授权，允许连接和创建对象
grant create cluster to notecloud;    -- 允许用户创建 簇
grant create procedure to notecloud;    -- 允许用户在 自己 schema 下创建 存储过程（procedure）
grant create synonym to notecloud;    -- 同义词（synonym）：是数据库对象的别名
grant create trigger to notecloud;    -- 触发器（trigger）：在 INSERT/UPDATE/DELETE 等事件发生时自动执行一段逻辑
grant create view to notecloud;    -- 视图（view）：查询语句的封装
grant debug any procedure to notecloud;    -- 允许调试 任意 schema 下的存储过程
grant debug connect session to notecloud;    -- 允许用户通过 debug 会话来调试 PL/SQL
grant CREATE JOB to notecloud;    -- 允许用户使用 DBMS_SCHEDULER 创建定时任务（job）
-- grant unlimited tablespace to notecloud;    -- （有了QUOTA语句则不需要这个）允许用户在数据库中的表空间 无限制使用配额
```

**若创建用户时未做配置**：

```sql
-- 默认存放数据的表空间
ALTER USER testuser DEFAULT TABLESPACE users;
-- 临时表空间，用于排序等操作
ALTER USER testuser TEMPORARY TABLESPACE temp;
 -- 在users表空间里不限空间配额
ALTER USER testuser QUOTA 100M ON users;     -- 限制为 100MB
ALTER USER testuser QUOTA UNLIMITED ON users; -- 不限额
```

> ## 用户权限
>
> ### 1. Oracle 用户权限分两大类
>
> 1. **系统权限（System Privileges）**
>
>    - 管理性权限，允许用户执行某类操作。
>    - 比如：`CREATE SESSION`（能登录）、`CREATE TABLE`（能建表）、`DROP USER`（能删用户）。
>    - Oracle 有上百个系统权限。
>
>    **常用的系统权限**：
>
>    ```sql
>    -- 登录 & 会话相关
>    CREATE SESSION -- → 能登录数据库。
>    -- 对象创建相关
>    CREATE CLUSTER -- -> 建立cluster（簇表），一种特殊的表存储结构，可以让多个表共享相同的数据块
>    CREATE TABLE -- → 建表。
>    CREATE VIEW -- → 建视图。
>    CREATE SEQUENCE -- → 建序列。
>    CREATE PROCEDURE -- → 建存储过程。
>    CREATE TRIGGER -- → 建触发器。
>    CREATE SYNONYM -- → 建同义词。
>    -- 管理相关
>    UNLIMITED TABLESPACE -- → 使用无限表空间配额（否则需要配额）。
>    ALTER USER、DROP USER -- → 管理用户。
>    CREATE ANY TABLE、DROP ANY TABLE -- → 在任意 schema 下建/删表（危险）。
>    SELECT ANY TABLE -- → 查询任意用户的表。
>    ```
>
> 2. **对象权限（Object Privileges）**
>
>    - 针对某个对象（表、视图、序列等）的权限。
>
>    - 比如：`SELECT`、`INSERT`、`UPDATE`、`DELETE`、`EXECUTE`。
>
>    - 授权形式：
>
>      ```sql
>      GRANT SELECT, INSERT ON employees TO guide_planet;
>      ```
>
> ## 角色(ROLE)
>
> 权限集合，里面预装了一堆系统权限或对象权限
>
> ### 1.常用的内置角色
>
> | 角色                       | 含义                                                         | 常见用途                       |
> | -------------------------- | ------------------------------------------------------------ | ------------------------------ |
> | **CONNECT**                | 允许登录数据库（含 `CREATE SESSION`）。在早期版本还包含更多权限，现在被简化。 | 开发用户最基本的“能连上库”。   |
> | **RESOURCE**               | 允许创建常见对象（表、视图、存储过程、触发器等）。           | 开发用户常用，用来建业务对象。 |
> | **DBA**                    | 数据库管理员权限，几乎所有权限。                             | DBA 专用，千万别给普通开发。   |
> | **EXP_FULL_DATABASE**      | 允许执行全库导出。                                           | 数据迁移、备份。               |
> | **IMP_FULL_DATABASE**      | 允许执行全库导入。                                           | 数据恢复、迁移。               |
> | **SELECT_CATALOG_ROLE**    | 允许查询数据字典视图。                                       | DBA/开发需要查看系统信息时用。 |
> | **AQ_ADMINISTRATOR_ROLE**  | 高级队列 (Advanced Queuing) 管理角色。                       | 用于消息队列功能。             |
> | **RECOVERY_CATALOG_OWNER** | RMAN 恢复目录的所有者角色。                                  | 备份/恢复专用。                |

**删除用户**：

```plsql
DROP USER notecloud;  -- 删除用户，保留其所有对象
DROP USER notecloud CASCADE； -- 删除用户，并且删除所有对象
```

**查看用户数据库有哪些用户（所有用户）**：

```sql
SELECT username FROM dba_users;
```

* 需要有 `DBA` 权限才能查到全部。

* 结果会列出数据库里所有用户（包括系统用户，比如 `SYS`, `SYSTEM`, `OUTLN`, `HR` 等）

**查看当前用户能看到的用户**：

```sql
SELECT username FROM all_users;
```

**查看自己是谁**：

```sql
SELECT user FROM dual;
```

> ## Oracle常自动创建的系统用户/组件用户
>
> ### 🔹1. 数据库核心用户
>
> - **SYS**：超级管理员，拥有所有权限，存放数据字典（Oracle 最核心的用户）。
> - **SYSTEM**：管理员用户，用来创建/管理普通用户和一些工具表。
> - **OUTLN**：存储查询执行计划的用户（很少直接使用）。
>
> ------
>
> ### 🔹2. 安全 & 管理用户
>
> - **AUDSYS**：审计日志用户（数据库审计用）。
> - **DBSNMP**：用于 Oracle Enterprise Manager 监控。
> - **APPQOSSYS**：应用质量服务 (QoS) 相关。
> - **SYSBACKUP / SYSDG / SYSKM**：12c 之后引入的专用管理用户，分别负责备份、数据保护和加密密钥管理。
> - **XS$NULL**：一个内部安全账号。
>
> ------
>
> ### 🔹3. 组件/功能用户
>
> 这些是 Oracle 自带功能的 schema（相当于功能模块的“房间”）：
>
> - **CTXSYS**：Oracle Text，文本检索功能。
> - **MDSYS, MDDATA**：空间数据（GIS）支持。
> - **OLAPSYS**：OLAP（联机分析处理）。
> - **XDB**：XML 数据库支持。
> - **WMSYS**：工作空间管理。
> - **ORDSYS, ORDDATA, ORDPLUGINS**：多媒体功能（图像/音视频处理）。
> - **OJVMSYS**：Java 虚拟机支持。
>
> ------
>
> ### 🔹4. 安全/加密组件用户
>
> - **LBACSYS**：Label Security（标签安全）。
> - **DVSYS, DVF**：Database Vault（数据库防护）。
> - **GSMADMIN_INTERNAL / GSMCATUSER / GSMUSER**：全局服务管理 (Global Service Management)。
> - **REMOTE_SCHEDULER_AGENT**：远程调度代理。
> - **SYSRAC**：RAC 集群相关。
>
> ------
>
> ### 🔹5. 其他内部用户
>
> - **ANONYMOUS**：Web 访问时的匿名账号。
> - **ORACLE_OCM**：Oracle Configuration Manager。
> - **SI_INFORMTN_SCHEMA**：示例 schema，用于存放示例信息。
> - **SYS$UMF**：内部维护用户。

## 2.Oracle数据库配置

### 2.1 打开数据库

CDB容器：

```sql
ALTER DATABASE OPEN;
```

CDB中的某个PDB容器：

```sql
ALTER PLUGGABLE DATABASE TESTPDB OPEN;
```

全部打开：

```sql
ALTER PLUGGABLE DATABASE ALL OPEN;
```

设置开机打开所有PDB

1. 登录CDB容器

   ```bash
   sqlplus / as sysdba
   ```

2. 打开要设置的PDB（如果没打开）

   ```sql
   ALTER PLUGGABLE DATABASE TESTPDB OPEN;
   ```

3. 执行保存状态命令

   ```sql
   ALTER PLUGGABLE DATABASE TESTPDB SAVE STATE;
   ```

或者一次性设置所有PDB自动打开：

```sql
ALTER PLUGGABLE DATABASE ALL OPEN;
ALTER PLUGGABLE DATABASE ALL SAVE STATE;
```

状态检查命令：

```sql
-- 查看数据库整体状态（CDB 层）
SELECT INSTANCE_NAME, STATUS FROM V$INSTANCE;

-- 查看所有 PDB 状态
SELECT NAME, OPEN_MODE, RESTRICTED FROM V$PDBS;
```

### 2.3 将监听改为动态监听

**原理：**

Oracle 有两种方式让监听知道实例和服务：

1. **静态注册**（listener.ora → SID_LIST，手动配置）
2. **动态注册**（数据库 PMON 自动把实例/服务注册到监听）

* 19c 默认是动态注册，尤其是 CDB + PDB 架构下，每个 PDB 在 **`srvctl`** 或 **`alter pluggable database open`** 后会自动生成服务名，比如

  ```nginx
  temppdb
  temppdb.domainname
  ```

* 只要监听进程启动、数据库参数 `LOCAL_LISTENER` 正确，PMON 会自动把 CDB 和 PDB 服务注册进去，你在 **Net Manager** 或 **tnsnames.ora** 里配置好这个服务名就能连。

**步骤：**

1.改listener.ora

* **删除或注释掉** CDB/PDB 的 SID_DESC（保留 CLRExtProc 那个即可），让监听只保留监听地址部分：

  ```ora
  SID_LIST_LISTENER =
    (SID_LIST =
      (SID_DESC =
        (SID_NAME = CLRExtProc)
        (ORACLE_HOME = C:\Users\GodsCat\Desktop\WINDOWS.X64_193000_db_home)
        (PROGRAM = extproc)
        (ENVS = "EXTPROC_DLLS=ONLY:C:\Users\GodsCat\Desktop\WINDOWS.X64_193000_db_home\bin\oraclr19.dll")
      )
    )
  
  LISTENER =
    (DESCRIPTION_LIST =
      (DESCRIPTION =
        (ADDRESS = (PROTOCOL = TCP)(HOST = localhost)(PORT = 1521))
      )
      (DESCRIPTION =
        (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
      )
      (DESCRIPTION =
        (ADDRESS = (PROTOCOL = TCP)(HOST = 192.168.148.139)(PORT = 1521))
      )
    )
  
  ADR_BASE_LISTENER = C:\Users\GodsCat\Desktop\WINDOWS.X64_193000_db_home\log
  ```

2. 在数据库端设置 LOCAL_LISTENER

* 在 `sqlplus / as sysdba` 中执行：

  ```sql
  ALTER SYSTEM SET LOCAL_LISTENER='(ADDRESS=(PROTOCOL=TCP)(HOST=192.168.148.139)(PORT=1521))' SCOPE=BOTH;
  ```

* 然后：

  ```sql
  ALTER SYSTEM REGISTER;
  ```

* 这会立刻让 PMON 把 CDB 和所有已打开的 PDB 服务注册到监听。

3. 确认 PDB 状态

   ```sql
   SHOW PDBS;
   ```

* 确保 `temppdb` 状态是 `READ WRITE`，否则不会注册服务。如果是 `MOUNTED`，执行：

  ```sql
  ALTER PLUGGABLE DATABASE temppdb OPEN;
  ```

4. 检查监听是否有新 PDB

   ```bash
   lsnrctl status
   ```

## 3.远程连接

1. 主机下载安装`instant client`工具，如`instantclient_23_8`，找到其路径，在PL/SQL下`配置-首选项-连接`中，`Oracle Home`设置为instancelient的安装路径，`OCI库`设置为安装路径下的oci.dll文件（推荐：将该安装路径设置为环境变量）
2. 服务器可使用Oracle自带的图形工具`Net Maneger`添加监听和服务器名，分别添加服务器的ip地址和1521端口。（也可不使用图形工具，手动找到oracle安装目录下的`network/admin/`下的`listener.ora`文件和`tnsnames.ora`文件，添加服务器ip相关设置）
3. 服务器防火墙开放TCP协议的1521端口
4. 服务器重启监听服务（指令：`lsnrctl stop`、`lsnrctl start`）
5. 此时PL/SQL可远程连接数据库

> ## Sysdba
>
> ### 1.什么是Sysdba？
>
> * `SYSDBA` 是 Oracle 中的一个 **特殊系统权限**，它是数据库的“超级管理员权限”。
>
> * 拥有 `SYSDBA` 权限的用户可以执行所有数据库管理任务，比如启动/关闭数据库、管理实例、恢复数据库、绕过常规权限限制等等。
>
> * **不只是“普通权限”，而是能完全控制整个数据库实例的最高权限。**
>
> ### 2.主要区别
>
> | 方面                | `AS SYSDBA` 登录                       | 普通 `SYSTEM` 用户登录               |
> | ------------------- | -------------------------------------- | ------------------------------------ |
> | 权限级别            | 数据库最高权限，几乎无限制             | 高权限，但有限制                     |
> | 能否启动/关闭数据库 | 可以（例如 `shutdown immediate`）      | 不可以                               |
> | 能否绕过权限检查    | 可以，几乎不受权限限制                 | 受普通权限限制                       |
> | 连接方式            | `sqlplus sys/xxx@service AS SYSDBA`    | `sqlplus system/xxx@service`         |
> | 适用场景            | 数据库管理员进行维护、恢复、备份等操作 | 管理用户、创建表、授权等常规管理操作 |
>
> ### 3.注意事项
>
> * 只有特定用户（默认是 `SYS`，也可以赋予其他用户）才能用 `AS SYSDBA` 登录。
>
> * 普通用户（包括 `SYSTEM`）即使权限很高，也不能用 `AS SYSDBA` 登录，除非被授权。
>
> * `AS SYSDBA` 登录时，Oracle 会认为你是“操作系统管理员”，忽略密码验证（在某些配置中），安全性更高。

## 4.Oracle开发常用数据字典视图

### 1. 和 **用户对象** 相关

- `USER_TABLES`：当前用户拥有的表
- `USER_TAB_COLUMNS`：当前用户表里的列信息（有 `COLUMN_NAME`, `DATA_TYPE`, `DATA_LENGTH` 等字段）
- `USER_INDEXES`：当前用户的索引
- `USER_CONSTRAINTS`：当前用户的约束（主键、外键、唯一等）
- `USER_SEQUENCES`：当前用户的序列

### 2. 和 **所有对象** 相关

- `ALL_TABLES`：用户有权限访问的表（包括别的 schema 的）
- `ALL_TAB_COLUMNS`：用户有权限访问的表的列
- `ALL_VIEWS`：能访问的视图
- `ALL_OBJECTS`：能访问的所有对象（表、视图、索引、同义词等）

### 3. 和 **全局DBA** 相关（需要 DBA 权限）

- `DBA_USERS`：数据库所有用户
- `DBA_TABLES`：数据库所有表
- `DBA_TAB_COLUMNS`：数据库所有表的列信息
- `DBA_ROLES`：所有角色
- `DBA_SYS_PRIVS`：用户或角色拥有哪些系统权限
- `DBA_ROLE_PRIVS`：用户被授予了哪些角色
- `DBA_TS_QUOTAS`：用户在各表空间上的配额
- `DBA_DATA_FILES`：数据文件信息（物理存放位置）

### 4.常见字段

这些视图里经常会看到一些典型字段：

- `TABLE_NAME` → 表名
- `COLUMN_NAME` → 列名
- `DATA_TYPE` → 数据类型（VARCHAR2, NUMBER, DATE…）
- `DATA_LENGTH` → 字段长度
- `OWNER` → 拥有者（schema 名）
- `CONSTRAINT_TYPE` → 约束类型（P=主键, R=外键, U=唯一, C=检查）
- `INDEX_NAME` → 索引名
- `SEQUENCE_NAME` → 序列名

### 5.技巧

`USER_` → 当前用户自己拥有的对象

`ALL_` → 当前用户能访问的对象（别人授权的也算）

`DBA_` → 数据库里所有对象（需要 DBA 权限）

---

---

# DDL

## 1.表-TABLE

### 1.1 最小表

```sql
CREATE TABLE user (
	id NUMBER,
    user_name VARCHAR2(64),
    user_email VARCHAR2(128),
    user_passwd VARCHAR2(128),
    user_profile_photo BLOB
);
```

> ## Oracle 数据类型
>
> ### 1.字符类型
>
> - `CHAR(n)`：定长字符串（最多 2000 字节）。
> - `VARCHAR2(n)`：变长字符串（最多 4000 字节）。👉 **最常用**。
> - `NCHAR(n)`：定长字符串（支持 Unicode，多字节）。
> - `NVARCHAR2(n)`：变长字符串（支持 Unicode，多字节）。
> - `CLOB`：大文本（最大 4GB）。👉 **常用**。
> - `NCLOB`：Unicode 大文本。
>
> ### 2.数值类型
>
> - `NUMBER(p,s)`：通用数值（p=精度，s=小数位）。👉 **最常用**。
> - `INTEGER`、`INT`：等价于 `NUMBER(38)`。
> - `DECIMAL(p,s)`：同 `NUMBER(p,s)`，只是别名。
> - `FLOAT(n)`：近似数值，基于二进制浮点。
>
> ### 3.日期 / 时间类型
>
> - `DATE`：日期 + 时间（精确到秒）。👉 常用。
> - `TIMESTAMP(n)`：日期 + 时间（精确到纳秒）。
> - `TIMESTAMP WITH TIME ZONE`：带时区。
> - `TIMESTAMP WITH LOCAL TIME ZONE`：存储时转换为数据库时区，查询时转换为会话时区。
> - `INTERVAL YEAR TO MONTH`：表示年-月间隔。
> - `INTERVAL DAY TO SECOND`：表示天-秒间隔。
>
> ### 4.二进制类型
>
> - `RAW(n)`：二进制数据，最多 2000 字节。
> - `LONG RAW`：老的二进制大对象（不推荐）。
> - `BLOB`：大二进制对象（最大 4GB）。👉 **最常用**。
> - `BFILE`：指向外部二进制文件的指针。
>
> ### 5.其他特殊类型
>
> - `ROWID`：行地址 ID。
> - `UROWID`：可变 ROWID。
> - `XMLTYPE`：存储 XML 数据。
> - `JSON`（19c 开始支持原生 JSON 数据类型）。

### 1.2 约束

* `PRIMARY KEY`：主键（唯一+非空）
* `UNIQUE`：唯一约束（允许空值）
* `NOT NULL`：列不能为空
* `FOREIGN FEK`：外键约束，引用别的表的主键或唯一键
* `CHECK`：检查条件（如`CHECK(age >= 0)

```sql
CREATE TABLE users (
  id NUMBER GENERATED ALWAYS AS IDENTITY PRIMARY KEY,  -- GENERATED ALWAYS AS IDENTITY 自增
  user_name VARCHAR2(64) NOT NULL,
  user_email VARCHAR2(128) UNIQUE NOT NULL,
  user_passwd VARCHAR2(128) NOT NULL,
  user_profile_photo BLOB
);
```

高级功能还有：自增、分区表、临时表、存储参数等。



---

---

# SQL



SQL（Structured Query Language）结构化查询语言，关系数据库的标准语言

特点：

* 集数据定义语言（DDL），数据操作语言（DML），数据控制语言（DCL）功能于一体
* 可以独立完成数据库生命周期中的全部活动
* 高度非过程化
* 面向集合
* 以同一种语法结构提供多种适用方式

> ## Oracle 常用数据类型
>
> **字符类型**
>
> - `CHAR(n)`：固定长度字符
> - `VARCHAR2(n)`：可变长度字符串（最常用）
> - `CLOB`：大文本
>
> **数值类型**
>
> - `NUMBER(p,s)`：数字（p=精度，s=小数位数）
> - `INTEGER`：其实是 `NUMBER` 的一种子集
>
> **日期/时间**
>
> - `DATE`：日期+时间（精确到秒）
> - `TIMESTAMP`：精确到纳秒
> - `INTERVAL`：时间间隔
>
> **二进制**
>
> - `RAW(n)`：原始二进制数据
> - `BLOB`：大二进制对象（图片、文件等）

## 1.DDL

### 1.表-TABLE

```SQL
CREATE TABLE user (
	id NUMBER PRIMARY KEY,
    user_name VARCHAR2(64),
    user_email VARCHAR2(128),
    user_passwd VARCHAR2(128),
    created_time DATE
)
```





## 1.查询

语句格式

```sql
SELECT [ALL|DISTINCT] <目标列表达式>[,<目标列表达式>] ...
FROM <表名或视图名>[,<表名或视图名>]...|(SELECT语句)[AS]<别名>
[WHERE <条件表达式>]
[GROUP BY <列名1>[HAVING<条件表达式>]]
[ORDER BY <列名2>[ASC|DESC]];
```

`SELECT`：指定要显示的属性列

`FROM`：指定查询的对象（基本表或视图）

`WHERE`：指定查询条件

`GROUP BY`：对查询结果按指定列的值分组，该属性列值相等的元组为一个组。通常会在每组中作用聚集函数

`HAVING`：只有满足指定条件的组才予以输出

`ORDER BY`：对查询的结果按指定列值的升序或降序排序

### 1.1 GROUP BY 分组

将查询结果按照一个或多个字段进行分组，并对每组执行聚合操作（如 COUNT、SUM、AVG、MAX、MIN 等）

细化聚集函数的作用对象

* 如果未对查询结果分组，聚集函数将作用于整个查询结果
* 对查询结果分组后，聚集函数将分别作用于每个组
* 按指定的一列或多列值分组，值相等的为一组

基本语法：

```sql
SELECT 分组字段，聚合函数（字段）
FROM 表名
GROUP BY 分组字段；
```

注意事项

1. `GROUP BY` 后的字段必须出现在 `SELECT` 中，除非用聚合函数包裹。
2. 如果有 `WHERE`，`GROUP BY` 在其后，`HAVING` 在其后用于筛选分组后的结果。

### 1.2 内外连接

| 连接类型     | 中文名称                     | 作用简述                                      |
| ------------ | ---------------------------- | --------------------------------------------- |
| `INNER JOIN` | 内连接                       | **只返回两个表中匹配的记录**                  |
| `LEFT JOIN`  | 左外连接（LEFT OUTER JOIN）  | **返回左表的所有记录，右表匹配不到的填 NULL** |
| `RIGHT JOIN` | 右外连接（RIGHT OUTER JOIN） | 返回右表的所有记录，左表匹配不到的填 NULL     |
| `FULL JOIN`  | 全外连接（FULL OUTER JOIN）  | 返回左右两边所有记录，不匹配的也保留（NULL）  |

想象一张Venn图

`INNER JOIN`：两个圆的交集

`LEFT JOIN`：左圆全部 + 右圆交集

`RIGHT JOIN`：右圆全部 + 左圆交集

`FULL JOIN`：两个圆所有部分的并集

### 1.3 带ANY或ALL的子查询

必须同时使用比较运算

* <=ANY小于等于子查询结果中的某个值
* <=ALL小于等于子查询结果中的所有值
* =ANY等于子查询结果中的某个值
* =ALL等于子查询结果中的所有值（通查没有实际意义）
* ！=ANY不等于子查询结果中的某个值
* ！=ALL不等于子查询结果中的任何一个值

### 1.4 集合查询

`UNION`：并集，联合查询多个查询的结果，适用于多个查询查同一张表，有去重效果，或者使用`UNION ALL`

`INTERSECT`：交集

`MINUS`：差集，第一条SQL语句中减去与第二条SQL语句查询结果相同的部分，不包含

### 1.5 基于派生表的查询

`FROM`关键字后有一个查询，查询出的结果为派生表，作为另一个查询的表

如果子查询中没有聚集函数，派生表可以不指定属性列，子查询SELECT子句后面的列名为其缺省属性。

### 1.6 分页查询(Oracle 12c+)

基本语法：

```plsql
SELECT 列名
FROM 表名
ORDER BY 排序字段
OFFSET 偏移量 ROWS FETCH NEXT 行数 ROWS ONLY;
```

| 关键字                   | 作用说明                      |
| ------------------------ | ----------------------------- |
| `OFFSET n ROWS`          | 跳过前 n 条记录（偏移量）     |
| `FETCH NEXT m ROWS ONLY` | 再取 m 条记录（分页的页大小） |

> 例：
>
> 例1：查询第一页（第1~10条）
>
> ```sql
> SELECT *
> FROM employees
> ORDER BY employee_id
> OFFSET 0 ROWS FETCH NEXT 10 ROWS ONLY;
> ```
>
> 例3：只指定 OFFSET，不加 FETCH（从第11条到最后）
>
> ```sql
> SELECT *
> FROM employees
> ORDER BY employee_id
> OFFSET 10 ROWS;
> ```
>
> 例4：只取前5条记录（等价于 `LIMIT 5`）
>
> ```sql
> SELECT *
> FROM employees
> ORDER BY hire_date DESC
> FETCH FIRST 5 ROWS ONLY;
> ```

**分页配合 Java/MyBatis 使用建议**

一般会在 Mapper.xml 中这样写（传入 `pageNo` 和 `pageSize`）：

```xml
<select id="queryPage" resultType="Employee">
  SELECT *
  FROM employees
  ORDER BY employee_id
  OFFSET #{offset} ROWS FETCH NEXT #{pageSize} ROWS ONLY
</select>
```

然后在 Java 里计算：

```java
int offset = (pageNo - 1) * pageSize;
```

## 2.插入、修改、删除

### 2.1 插入

语法格式：

```sql
INSERT INTO <表名> 
[(<属性列1>[,<属性列2>···])]
VALUES (<常量1>[,<常量2>]···);
```

INTO子句：

* 指定要插入数据的表名及属性列
* 属性列的顺序可与表定义中的顺序不一致
* 没有指定属性列：表示要插入的是一条完整的元组，且属性列属性与表定义中顺序一致
* 指定部分属性列：插入的元组在其余属性列上取空值

一般指定列属性

可插入子查询的结果，代替VALUES

### 2.2 修改数据

语法格式：

```sql
UPDATE <表名>
SET <列名>=<表达式>[,<列名> = <表达式>]···
[WHERE <条件>];
```

功能

* 修改指定表中满足WHERE子句条件的元组
* SET子句给出<表达式>的值用于取代相应的属性列
* 如果省略WHERE子句，表示要修改表中所有的元组

### 2.3 删除

语法格式：

```sql
DELETE FROM <表名>
[WHERE <条件>];
```

WHERE子句：

* 指定要删除的元组
* 缺省表示要删除表中全部元组，表的定义仍在字典中

## 3.空值的处理

一般有以下几种情况

* 该属性应该有一个值，但目前不知道它的具体值
* 该属性不应该有值
* 由于某种原因不便于填写

空值是一个很特殊的值，含有不确定性。对关系运算带来特殊的问题，需要做特殊的处理，一般不应该出现空值，而是设置默认值

属性定义中（或者域定义）中

* 有NOT NULL约束条件的不能取空值
* 加了UNIQUE限制的属性不能取空值
* 码属性不能取空值

空值处理：

* 空值与另一个值（包括另一个空值）的算术运算的结果为空值
* 空值与另一个值（包括另一个空值）的比较运算的结果为UNKNOWN
* 有UNKNOWN后，传统二值（TRUE,FALSE）逻辑就扩展成了三值逻辑

---

---

# PL/SQL编程

PL/SQL（Procedural Language/SQL）是Oracle在数据库中引入的一种过程化编程语言，构建于SQL之上，可以用来编写包含SQL语句的程序。

PL/SQL中可以通过IF语句或LOOP语句控制程序的执行流程，甚至可以定义变量，在语句之间传递数据信息，从而控制程序处理细节。

因此能将SQL语句的数据操纵能力、数据查询能力和PL/SQL的过程处理能力结合在一起。

## 1.程序块

```plsql
DECLARE 声明部分，可选
-- --
BEGIN 执行部分，必须
-- --
EXCEPTION 异常处理部分，可选
-- -- 
END 表示结束
```

---

## 2.基础语法

### 2.1 其他符号

* 赋值`:=`
  * `a:=a+1`
* 并置`||`
  * `full_name := 'Narth' || 'Yebba'`，与concat函数效果相同

### 2.2 变量与常量

变量语法：`变量名 数据类型[(长度) [:= 初始值]]`,PL/SQL中未初始化的变量是NULL

常量语法：`常量名 constant 数据类型[:= 常量值]`

### 2.3 数据类型

1. 数值类型

* 包括NUMBER、PLS_INTERGER和、INARY_INTEGER
* NUMBER可以存储整数或浮点数

2. 字符类型

* 包括VARCHAR2、CHAR（有默认值1）、LONG、NCHAR和NVARCHAR2等

### 2.4 选择与循环

1. if-then语句

```plsql
if<condition_expression> then
	plsql_sentence;
[elsif<condition_expression2> then
	plsql_sentence2;]
[else
	plsql_sentence3;]
end if;
```

2. loop语句

```plsql
loop
	循环执行的语句块;
	exit when 循环结束条件;
	循环结束条件修改；
end loop;
```

3. while-loop

```plsql
while(循环结束条件)loop
	循环执行的语句块；
	循环结束的条件修改;
end loop;
```

4.for语句

```plsql
for variable_counter_name in [reverse] lower_limit..upper_limit loop
	plsql_sentence;
end loop;
```

5. case语句

```plsql
declare
	season int;
	aboutinfo varchar2(50);
begin
	season := &season;
	case season
		when 1 then
		aboutinfo := season || 'month:1、2、3';
		...
		else
		aboutinfo := 'seson error';
	end case;
end;
```

6. goto语句 无条件跳转

```plsql
declare
	v_result := 1;
begin
	for v_result in 1..100 loop
		if v_result = 2 then
			goto endpoint;
		end if;
		plsql_sentences;
	end loop;
	<<endpoint>>
end;
```

---

## 3.游标

对查询结果的一个指针，允许你在 PL/SQL 中 逐行读取结果集。

使用场景：

* 批量处理数据（比如循环插入、更新、判断）

* 需要逐行分析、记录日志、或带条件处理

* 替代集合操作中一些不能直接完成的复杂逻辑

### 3.1 显式游标

是由用户声明和操作的一种游标，通常用于操作查询select语句返回的结果集

使用游标：

* 声明游标

  ```plsql
  declare
  	cursor cur_stu(var_sdept varchar2:='CS') is select sno,sname,sage from student where sdept = var_sdept;
  ```

* 打开游标

* 读取数据（FETCH）

* 关闭游标（需要数据为空）

---

## 4.异常处理

编译时异常/运行时异常

### 4.1 异常处理语法

基本语法

```plsql
exception
	when value_error then
	dbms_output.put_line('value error');
```

### 4.2 用户自定义异常

```plsql
declare
	v_data number;
	v_myexp exception;
begin
	v_data := &inputdata;
	if v_data>100 then
		raise v_myexp;
	end if;
exception
	when OTHERS then
	Dbms_Output.put_line('MY EXCEPTION');
END;
```

---

---

# 存储过程与触发器

把需要重复执行的内容放在存储过程中，实现代码的复用

## 1.存储过程

### 1.1 创建

```plsql
CREATE OR REPLACE PROCEDURE procedure_name (
    param1 IN  datatype,
    param2 OUT datatype,
    ...
)
IS
    -- 变量定义
BEGIN
    -- SQL逻辑
END;
```

### 1.2 调用

```plsql
DECLARE
    v_salary NUMBER;
BEGIN
    calc_annual_salary(101, v_salary);
    DBMS_OUTPUT.PUT_LINE('Annual Salary: ' || v_salary);
END;
```

### 1.3 修改

```plsql
create or replace procedure my_proc1 is
begin
	insert into student(sno, sname, sage) values('201215201','李四',20);
	dbms_output.put_line('insert success')
end my_proc1;
```

## 2.函数

函数一般用于计算或表示某种功能，函数可以接受参数，有的函数也可以没有参数。但是函数必须要有返回值。

### 2.1 创建

```plsql
CREATE OR REPLACE FUNCTION function_name (
    param1 IN datatype,
    ...
) RETURN datatype
IS
    -- 变量定义
BEGIN
    -- SQL逻辑
    RETURN some_value;
END;
```

### 2.2 调用

```plsql
-- 在 SQL 中调用：
SELECT get_annual_salary(101) FROM dual;

-- 在匿名块中调用：
DECLARE
    v_sal NUMBER;
BEGIN
    v_sal := get_annual_salary(101);
    DBMS_OUTPUT.PUT_LINE('Annual Salary: ' || v_sal);
END;
```

## 3.触发器

触发器（**Trigger**）是 Oracle 数据库中的一种特殊对象，它是一段 **在特定事件发生时被自动执行的 PL/SQL 代码块**，不需要手动调用。

### 3.1 常见触发器类型

| 类型                          | 描述                                  | 示例                                    |
| ----------------------------- | ------------------------------------- | --------------------------------------- |
| **行级触发器**（ROW）         | 每处理一行就触发一次                  | UPDATE 一张有 100 行的表，会触发 100 次 |
| **语句级触发器**（STATEMENT） | 每次执行一次 SQL 语句只触发一次       | UPDATE 一张有 100 行的表，也只触发一次  |
| **BEFORE 触发器**             | 在数据变更前触发                      | 可用于数据校验或修改                    |
| **AFTER 触发器**              | 在数据变更后触发                      | 可用于日志记录                          |
| **INSTEAD OF 触发器**         | 用于视图上，替代 INSERT/UPDATE/DELETE | 常用于复杂视图                          |

### 3.2 基本语法（行级）

```plsql
CREATE OR REPLACE TRIGGER trigger_name
BEFORE INSERT ON table_name
FOR EACH ROW
BEGIN
    -- 逻辑代码
END;
```

### 3.3 注意事项

* **触发器不能直接提交或回滚事务（不能写 `COMMIT` 或 `ROLLBACK`）**。

* 使用过多或过于复杂的触发器会导致 **性能问题或逻辑混乱**。

* 行级触发器必须加 `FOR EACH ROW`，否则默认是语句级。

## 4.包（Package）

在 Oracle 数据库中，**包（Package）** 是一种 **PL/SQL 编程结构**，它将**相关的过程（Procedure）、函数（Function）、变量、常量、游标等封装在一起**，提供一种模块化、结构化管理 PL/SQL 代码的方法。

### 4.1 包组成结构

* 包规范：定义对外可见的接口（过程、函数、变量）
* 包体：实现包规范中声明的过程、函数的具体逻辑

### 4.2 包规范语法

```plsql
CREATE OR REPLACE PACKAGE package_name IS
    -- 变量声明
    -- 常量声明
    -- 过程声明
    -- 函数声明
END package_name;
```

### 4.3 包体语法

```plsql
CREATE OR REPLACE PACKAGE BODY package_name IS
    -- 过程实现
    -- 函数实现
    -- 可选的私有过程/函数
END package_name;
```

---

# 实战

从0开始使用Oracle创建项目数据库并实现增删改查

## 1.创建用户

```plsql
-- 创建用户
CREATE USER guide_planet IDENTIFIED BY guide_planet
DEFAULT TABLESPACE users     -- 默认存放数据的表空间
TEMPORARY TABLESPACE temp    -- 临时表空间，用于排序等操作
QUOTA UNLIMITED ON users;    -- 在users表空间里不限空间配额
-- 给用户授权，允许连接和创建对象
GRANT CONNECT, RESOURCE TO guide_planet;
```

> ## 表空间
>
> Oracle 不直接把数据存放在操作系统文件上，而是将数据存放在 **表空间** 中。
>
> - **表空间 = 逻辑存储单元**，它由一个或多个物理数据文件组成。
> - 表空间包含用户的表、索引等数据库对象的数据。
> - 简单来说，表空间是数据库中用来管理数据文件的一种机制，方便对存储进行管理和分配。
>
> ### DEFAULT TABLESPACE users
>
> ```plsql
> DEFAULT TABLESPACE users
> ```
>
> * 这是指定该用户创建的表和其他对象默认存储在哪个表空间。
>
> * users 是 Oracle 默认安装时自带的一个普通表空间，通常用来存放普通用户数据。
>
> * 上如果你不提前创建新的表空间，通常用 users 就足够了。

之后使用创建的用户连接数据库

## 2.建表

建立业务表：

```plsql
DROP TABLE users;
-- 建立业务表
-- user表
CREATE TABLE users (
  id NUMBER PRIMARY KEY,                         -- 主键
  username VARCHAR2(50) NOT NULL UNIQUE,         -- 用户名，唯一
  email VARCHAR2(100) NOT NULL UNIQUE,           -- 邮箱，唯一
  password VARCHAR2(100) NOT NULL,               -- 加密后密码
  salt VARCHAR2(50),                             -- 密码加密盐（可选）
  phone VARCHAR2(20),                            -- 手机号（可选）
  status NUMBER(1) DEFAULT 1,                    -- 状态：1正常，0禁用
  created_at DATE DEFAULT SYSDATE,               -- 创建时间
  updated_at DATE DEFAULT SYSDATE,               -- 更新时间
  last_login DATE                                -- 上次登录时间
);
```

## 3.注册-插入

