# linux常用命令笔记

### 查看开启的端口号

```shell
#查看某端口是否开启
lsof -i:端口号

#查看所有开启的端口
netstat -aptn

#查看开启的udp端口
netstat -nupl

#查看开启的tcp端口
netstat -ntpl
```



### 启动Jenkins并指定端口

```shell
java -jar jenkins.war --httpPort=9090

#后台启动
nohup java -jar jenkins.war --httpPort=9090  > log.file  2>&1 &
```



### 修改文件目录的所有者

```shell
#修改文件所属用户
chown [-R] 账号名称 文件或目录
chown zxj testfile

#修改文件所属用户和用户组
chown [-R] 账号名称:用户组名称 文件或目录
chown root:root testfile
```



### 查看日志常用命令

1. 实时查看最新的100条日志

   ```shell
   tail -100f test.log 
   ```

2. 查关键字的日志

   ```shell
   cat -n test.log |grep "debug"
   ```

3. 查询日志尾部最后100行的日志

   ```shell
   tail  -n  10  test.log
   ```

4. 查询10行之后的所有日志

   ```shell
   tail  -n  +10  test.log
   ```

5. 查询日志头10行的日志

   ```shell
   head -n 10 test.log
   ```

6. 查询除了头10行之外的日志

   ```shell
   head -n -10 test.log
   ```

   

应用场景：按行号查看---过滤出关键字附近的日志

```shell
#1.得到关键日志的行号
cat -n test.log |grep "debug"  

#2.选择关键字所在的中间一行. 然后查看这个关键字前10行和后10行的日志
#tail -n +92表示查询92行之后的日志
#head -n 20 则表示在前面的查询结果里再查前20条记录
cat -n test.log |tail -n +92|head -n 20 
```

日志内容特别多，打印在屏幕上不方便查看

```shell
#分页打印了,通过点击空格键翻页
cat -n test.log |grep "debug" |more
```

### 其他命令

启动nacos单机部署模式

```shell
bash startup.sh -m standalone
```



统计目录下子文件夹和文件数量

```shell
ls | wc -l
```

### yum和rpm递归依赖解析

```shell
repoquery -a --tree-requires PACKAGE

repoquery -a --tree-requires PACKAGE | sed 's/^[ |]*\\_\s*\(\S\+\)/\1/' | grep -Eo '^\S+' | sort | uniq
```

