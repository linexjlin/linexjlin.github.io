---
layout: post
title: go-oci8 使用
---
# go-oci8 使用
 
## Linux 下OCI 配置
1. instant client
在[这里](http://www.oracle.com/technetwork/cn/topics/linuxx86-64soft-092277.html)  下载 instant client 的basic + sdk +顺便把sqlplus 也下了。
目前能下到的最新的是这几个文件
```
-rw-r--r--.  1 root root 63352239 Jun  9 14:56 instantclient-basic-linux.x64-12.1.0.2.0.zip
-rw-r--r--.  1 root root   667174 Jun  9 14:56 instantclient-sdk-linux.x64-12.1.0.2.0.zip
-rw-r--r--.  1 root root   861284 Jun 10 11:18 instantclient-sqlplus-linux.x64-12.1.0.2.0.zip
```

1. 把这几个文件解压出来全都放在一个文件夹里面就像这样
```
ls -l instantclient_12_1/
total 193340
-rwxrwxr-x. 1 root root     29404 Jul  7  2014 adrci
-rw-rw-r--. 1 root root       440 Jul  7  2014 BASIC_README
-rwxrwxr-x. 1 root root     43944 Jul  7  2014 genezi
-r-xr-xr-x. 1 root root       342 Jul  7  2014 glogin.sql
-rwxrwxr-x. 1 root root   6990875 Jul  7  2014 libclntshcore.so.12.1
-rwxrwxr-x. 1 root root  58793741 Jul  7  2014 libclntsh.so.12.1
-r-xr-xr-x. 1 root root   1768370 Jul  7  2014 libipc1.so
-r-xr-xr-x. 1 root root    544150 Jul  7  2014 libmql1.so
-r-xr-xr-x. 1 root root   6213011 Jul  7  2014 libnnz12.so
-rwxrwxr-x. 1 root root   2576030 Jul  7  2014 libocci.so.12.1
-rwxrwxr-x. 1 root root 109549133 Jul  7  2014 libociei.so
-r-xr-xr-x. 1 root root    156353 Jul  7  2014 libocijdbc12.so
-r-xr-xr-x. 1 root root    337137 Jul  7  2014 libons.so
-rwxrwxr-x. 1 root root    118491 Jul  7  2014 liboramysql12.so
-r-xr-xr-x. 1 root root   1564082 Jul  7  2014 libsqlplusic.so
-r-xr-xr-x. 1 root root   1546540 Jul  7  2014 libsqlplus.so
-r--r--r--. 1 root root   3692096 Jul  7  2014 ojdbc6.jar
-r--r--r--. 1 root root   3698857 Jul  7  2014 ojdbc7.jar
drwxrwxr-x. 5 root root      4096 Jul  7  2014 sdk
-r-xr-xr-x. 1 root root      9581 Jul  7  2014 sqlplus
-rw-rw-r--. 1 root root       444 Jul  7  2014 SQLPLUS_README
-rwxrwxr-x. 1 root root    227410 Jul  7  2014 uidrvci
-rw-rw-r--. 1 root root     71202 Jul  7  2014 xstreams.jar
```
 
1. 安装
```
mv instantclient_12_1/ /usr/local
cd /usr/local/instantclient_12_1
ln -s libclntsh.so.12.1 libclntsh.so
```

1.  配置 oci8.pc
```
vim /usr/lib/pkgconfig/oci8.pc
echo export PKG_CONFIG_PATH=/user/lib/pkgconfig >>/etc/profile
```
判断是否oci8已经安全成功
```
pkg-config --list-all | grep oci8
```
 
1. 环境变量
```
vim ~/.bash_profile
加入以下内容：
export ora_home=/usr/local/instantclient_12_1
export PATH=$PATH:$ora_home
export LD_LIBRARY_PATH=$ora_home
使之生效：
source ~/.bash_profile
```
 
## 连接oracle 测试
1.  简易连接
不需要更多的配置文件
```
sqlplus user/pwd@ip:1521/test
```
2. 通过tnsnames 连接
```
vim $ora_home/tnsnames.ora
填入以下内容：
test =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = ip或主机名称)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SERVICE_NAME = orcl)
    )
  )
然后，
sqlplus user/pwd@test
```
连接可能会出现ORA-21561 错误，下面有解决办法。
 
## go-oci8
以上步骤完成后：
```
go get github.com/mattn/go-oci8
```
如果没有报错就说明安装成功了。
 
 
## go-oci8 测试
测试例1:
```golang
package main
 
import (
    "database/sql"
    "fmt"
    _ "github.com/mattn/go-oci8"
    "log"
)
 
type Users struct {
    ID int
}
 
func main() {
    log.Println("Oracle Driver Connecting....")
    //用户名/密码@实例名 如system/123456@orcl、sys/123456@orcl
    db, err := sql.Open("oci8", "user/pwd@226w")
    if err != nil {
        log.Fatal(err)
        panic("数据库连接失败")
    } else {
        fmt.Println("Connect success!")
        defer db.Close()
        var users []Users = make([]Users, 0)
        rows, err := db.Query("select 1 ID from dual")
        if err != nil {
            log.Fatal(err)
        } else {
            for rows.Next() {
                var u Users
                rows.Scan(&u.ID)
                users = append(users, u)
            }
            fmt.Println(users)
            defer rows.Close()
        }
    }
} 
```
测试例2:
```go
package main
 
import (
        "database/sql"
        "flag"
        "fmt"
        _ "github.com/mattn/go-oci8"
        "log"
        "strings"
)
 
var conn string
 
func main() {
        flag.Parse()
        if flag.NArg() >= 1 {
                conn = flag.Arg(0)
        } else {
                conn = "user/pwd@226W"
        }
 
        db, err := sql.Open("oci8", conn)
        if err != nil {
                fmt.Println("can't connect ", conn, err)
                return
        }
        if err = test_conn(db); err != nil {
                fmt.Println("can't connect ", conn, err)
                return
        }
        var in string
        var sqlquery string
        fmt.Print("> ")
        for {
                fmt.Scan(&in)
                if in == "q;" {
                        break
                }
                if in[len(in)-1] != ';' {
                        sqlquery += in + " "
                } else {
                        sqlquery += in[:len(in)-1]
                        rows, err := db.Query(sqlquery)
                        if err != nil {
                                fmt.Println("can't run ", sqlquery, "\n", err)
                                fmt.Print("> ")
                                sqlquery = ""
                                continue
                        }
                        cols, err := rows.Columns()
                        if err != nil {
                                log.Fatal(err)
                        }
                        fmt.Println(strings.Join(cols, "\t"))
                        var result = make([]string, len(cols))
                        var s = make([]interface{}, len(result))
                        for i, _ := range result {
                                s[i] = &result[i]
                        }
                        for rows.Next() {
                                rows.Scan(s...)
                                fmt.Println(strings.Join(result, "\t"))
                        }
                        rows.Close()
                        fmt.Print("> ")
                        sqlquery = ""
                }
        }
 
        db.Close()
}
 
func test_conn(db *sql.DB) (err error) {
        query := "select * from dual"
        _, err = db.Query(query)
        return err
}
```
注1:
上面open 的第二个参数改成简易连接也是可以的。
 
 
## 各种错误
 
1.  undefined reference to `lxgcvpc'
在 go get github.com/mattn/go-oci8 时出现这个错：
```
/usr/lib/gcc/x86_64-linux-gnu/4.8/../../../../lib/libclntsh.so: undefined reference to `lxgcvpc'
/usr/lib/gcc/x86_64-linux-gnu/4.8/../../../../lib/libclntsh.so: undefined reference to `lnxnftu'
/usr/lib/gcc/x86_64-linux-gnu/4.8/../../../../lib/libclntsh.so: undefined reference to `lbivor'
/usr/lib/gcc/x86_64-linux-gnu/4.8/../../../../lib/libclntsh.so: undefined reference to `lemgpd'
/usr/lib/gcc/x86_64-linux-gnu/4.8/../../../../lib/libclntsh.so: undefined reference to `lfimkpth'
/usr/lib/gcc/x86_64-linux-gnu/4.8/../../../../lib/libclntsh.so: undefined reference to `ssOswPclos
```
这说明，你的lib库有错， 你需要
```
source .bash_profile
```
 
1.  ORA-21561
在连接oracle 时， 如果你遇到这个错，你只需要：
```
echo hostname> /etc/hostname
vim /etc/hosts
127.0.0.1 hostname
```

 
## 参考
http://usr.cc/thread-57972-1-1.html
http://www.cnblogs.com/ghj1976/p/3512555.html
