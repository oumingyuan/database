dba权限连接数据库。
sqlplus / as sysdba
创建tableplace1表空间，存储在c盘。
create tablespace tableplace1 datafile 'c:\tableplace.dbf' size 1000m;
创建用户user1，密码为pwd，默认表空间为tableplace1。
create user user1 identified by pwd default tablespace tableplace1;
给user1赋予权限。
grant create table,unlimited tablespace,create session,connect,resource,dba to user1;




查看当前用户
show user

select * from dba_users; 查看数据库里面所有用户，前提是你是有dba权限的帐号，如sys,system
select * from all_users;  查看你能管理的所有用户！
select * from user_users; 查看当前用户信息 ！

 

-- 查询你 当前用户下,有哪些表
SELECT * FROM user_tables

-- 查询你 当前用户下, 可以访问哪些表 [也就是访问自己 和 其他用户的]
SELECT * FROM all_tables

-- 查询当前数据库所有的表， 需要你有 DBA 的权限
SELECT * FROM dba_tables



3.若修改某一个用户密码， 修改用户口令 格式为：

alter user 用户名 identified by 新密码；

以system 为例，密码修改为 123456. 可输入

alter user system identified by 123456;


因为新建的用户和默认的用户是锁住的，没有权限。所以新建用户后要给用户赋予权限

grant dba to 用户名    --给用户赋予所有权限，connect是赋予连接数据库的权限，resource 是赋予用户只可以创建实体但是没有创建数据结构的权限。

grant create session to 用户名  　　   --这个是给用户赋予登录的权限。

grant create table to  用户名     　　   --给用户赋予表操作的权限

grant unlimited tablespace to  用户名     --给用户赋予表空间操作的权限

grant select any table to 用户名         --给该用户赋予访问任务表的权限   同理可以赋予update 和delete 的

grant select on srapp_hz_zhpt_yl.jggl to srapp_hz_zhpt_ylcs   --这里是给srapp_hz_zhpt_ylcs用户赋予selectsrapp_hz_zhpt_yl用户的jggl表的查询的权限。同理可以有alter，drop，insert等权限。   -----------------------------注意 这个语句在没有访问另一个用户的权限情况下这个语句要在另一个用户登录情况下执行，这样才能生效。

 
 
 
 





--1、查看表空间的名称及大小 
SELECT t.tablespace_name, round(SUM(bytes / (1024 * 1024)), 0) ts_size 
FROM dba_tablespaces t, dba_data_files d 
WHERE t.tablespace_name = d.tablespace_name 
GROUP BY t.tablespace_name; 
--2、查看表空间物理文件的名称及大小 
SELECT tablespace_name, 
file_id, 
file_name, 
round(bytes / (1024 * 1024), 0) total_space 
FROM dba_data_files 
ORDER BY tablespace_name; 
--3、查看回滚段名称及大小 
SELECT segment_name, 
tablespace_name, 
r.status, 
(initial_extent / 1024) initialextent, 
(next_extent / 1024) nextextent, 
max_extents, 
v.curext curextent 
FROM dba_rollback_segs r, v$rollstat v 
WHERE r.segment_id = v.usn(+) 
ORDER BY segment_name; 
--4、查看控制文件 
SELECT NAME FROM v$controlfile; 
--5、查看日志文件 
SELECT MEMBER FROM v$logfile; 
--6、查看表空间的使用情况 
SELECT SUM(bytes) / (1024 * 1024) AS free_space, tablespace_name 
FROM dba_free_space 
GROUP BY tablespace_name; 
SELECT a.tablespace_name, 
a.bytes total, 
b.bytes used, 
c.bytes free, 
(b.bytes * 100) / a.bytes "% USED ", 
(c.bytes * 100) / a.bytes "% FREE " 
FROM sys.sm$ts_avail a, sys.sm$ts_used b, sys.sm$ts_free c 
WHERE a.tablespace_name = b.tablespace_name 
AND a.tablespace_name = c.tablespace_name; 
--7、查看数据库库对象 
SELECT owner, object_type, status, COUNT(*) count# 
FROM all_objects 
GROUP BY owner, object_type, status; 
--8、查看数据库的版本　 
SELECT version 
FROM product_component_version 
WHERE substr(product, 1, 6) = 'Oracle'; 
--9、查看数据库的创建日期和归档方式 
SELECT created, log_mode, log_mode FROM v$database; 
 

--1G=1024MB 
--1M=1024KB 
--1K=1024Bytes 
--1M=11048576Bytes 
--1G=1024*11048576Bytes=11313741824Bytes 
SELECT a.tablespace_name "表空间名", 
total "表空间大小", 
free "表空间剩余大小", 
(total - free) "表空间使用大小", 
total / (1024 * 1024 * 1024) "表空间大小(G)", 
free / (1024 * 1024 * 1024) "表空间剩余大小(G)", 
(total - free) / (1024 * 1024 * 1024) "表空间使用大小(G)", 
round((total - free) / total, 4) * 100 "使用率 %" 
FROM (SELECT tablespace_name, SUM(bytes) free 
FROM dba_free_space 
GROUP BY tablespace_name) a, 
(SELECT tablespace_name, SUM(bytes) total 
FROM dba_data_files 
GROUP BY tablespace_name) b 
WHERE a.tablespace_name = b.tablespace_name 


