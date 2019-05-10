--1、查看表空间的名称及大小 
SELECT t.tablespace_name, round(SUM(bytes / (1024 * 1024)), 0) ts_size 
FROM dba_tablespaces t, dba_data_files d 
WHERE t.tablespace_name = d.tablespace_name 
GROUP BY t.tablespace_name; 


# 创建表空间的语法
create tablespace 表间名 datafile '数据文件名' size 表空间大小

create tablespace idx_test datafile 'D:\database\oradata\test\test.dbf' size 1000M;
(*数据文件名 包含全路径, 表空间大小 2000M 表是 2000兆) 



2、建好tablespace, 就可以建用户了
格式: 
create user 用户名 identified by 密码 default tablespace 表空间表;

create user c##ming identified by 123456 default tablespace idx_test;



(*我们创建一个用户名为 study,密码为 study, 缺少表空间为 data_test -这是在第二步建好的.)
(*缺省表空间表示 用户study今后的数据如果没有专门指出，
其数据就保存在 data_test中, 也就是保存在对应的物理文件 e:\oracle\oradata\test\data_1.dbf中)
                
                
                


