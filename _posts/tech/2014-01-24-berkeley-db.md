---
layout: post
author: yangtze736
title: Berkeley DB
category: 技术
tags: 数据库
keywords: 
description: 
---

###Berkeley DB的简介
--------------------

Berkeley DB（BDB）是一个高性能的嵌入式数据库编程库（引擎），它可以用来保存任意类型的键／值对 (Key/Value Pair)，而且可以为一个键保存多个数据。Berkeley DB可以支持数千的并发线程同时操作数据库，支持最大256TB的数据。

BDB提供诸如C语言，C++，Java，Perl，Python，Tcl等多种编程语言的API，并且广泛支持大多数类Unix操作系统和Windows操作系统以及实时操作系统（如 VxWorks）。

1991年，Berkeley DB的第一个版发行（Linux系统也在这一年诞生），其最初的开发目的是以新的HASH访问算法来代替旧的hsearch函数和大量的dbm实现，该版本还包含了B+树数据访问算法。

1992年，BSD UNIX第4.4发行版中包含了Berkeley DB1.85版。基本上认为这是Berkeley DB的第一个正式版。

1996年，Sleepycat软件公司成立，提供对Berkeley DB的商业支持。

2006年，Sleepycat被Oracle收购，当时最新版本是4.7.25。

2009年，SUN被Oracle收购，不知道MySQL会不会再次启用Berkeley DB。


###Berkeley DB的特点
--------------------

Berkeley DB可以轻松支持上千个线程同时访问数据库，支持多进程、事务等特性。Berkeley DB运行在大多数的操作系统中，例如大多数的UNIX系统， 和windows系统，以及实时操作系统。
Berkeley DB 还拥有对一些老的UNIX数据库，例如dbm, ndbm und hsearch的兼容接口，
对于在java系统中的使用，Berkeley DB提供了一个压缩成jar单个文件的java版本。 这个版本可以运行在java虚拟机上使用，并且拥有和C语言版本相同的所有操作和功能。
Berkeley DB XML，是一个接口，通过它可以实现对XML数据存贮的支持。对XML数据的访问，会使用相应的查询语句如Xquery, Xpath。
Berkeley DB只支持单一的数据结构，它的所有数据包括两个部分：key 和　data.
Berkeley DB原则上是为嵌入式数据库设计的。
 
###Berkeley DB的编译安装和使用
------------------------------

编译安装

```
# cd /usr/local/src
# wget http://download.oracle.com/berkeley-db/db-4.6.18.tar.gz
# tar -zxvf db-4.6.18.tar.gz
# cd db-4.6.18
# cd build_unix
# ../dist/configure --enable-cxx
# make
# make install
```

编写test.cpp，将字符“hello world”存入bdb

```c 
#include <iostream>
#include <string.h>
#include <db_cxx.h>
#include <db.h>

using std::cout;
using std::endl;
using std::cerr;

int
main (int argc, char *argv[])
{
	try
	{
		/* 1: create the database handle */
		Db db (0, 0);

		/* 2: open the database using the handle */
		db.open (NULL, "./chap4_db", NULL, DB_BTREE, DB_CREATE, 0644);

		/* 3: create the key and value Dbts */
		char *first_key = "first_record";
		u_int32_t key_len = (u_int32_t) strlen (first_key);

		char * first_value = "Hello World - Berkeley DB style!!";
		u_int32_t value_len = (u_int32_t) strlen (first_value);

		Dbt key (first_key, key_len + 1);
		Dbt value (first_value, value_len + 1);

		/* 4: insert the key-value pair into the database */
		int ret;
		ret = db.put (0, &key, &value, DB_NOOVERWRITE);
		if (ret == DB_KEYEXIST)
		{
			cout << "hello_world: " << first_key << " already exists in db" <<endl;
		}

		/* 5: read the value stored earlier in a Dbt object */
		Dbt stored_value;
		ret = db.get (0, &key, &stored_value, 0);

		/* 6: print the value read from the database */
		cout << (char *) stored_value.get_data () << endl;

		/* 7: close the database handle */
		db.close (0);
	}
	catch (DbException & dbex)
	{
		cerr << "hello_world: exception caught: " << dbex.what () << endl;
	}
}
```

```
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/BerkeleyDB.4.6/lib/
g++ -Wall test.cpp -o test -ggdb -I/usr/local/BerkeleyDB.4.6/include/ -L/usr/local/BerkeleyDB.4.6/lib/  -ldb_cxx -lpthread
```

验证结果

```
# ./test
Hello World - Berkeley DB style!!
# ./test
hello_world: first_record already exists in db
Hello World - Berkeley DB style!!
# file chap4_db 
chap4_db: Berkeley DB (Btree, version 9, native byte-order)
```

使用db_dump查看

```
# /usr/local/BerkeleyDB.4.6/bin/db_dump -p chap4_db
VERSION=3
format=print
type=btree
db_pagesize=4096
HEADER=END
first_record\00
Hello World - Berkeley DB style!!\00
DATA=END
```

-----------------------------------------------------
   写完收工　　☆(￣▽￣)/$:.°★

