
---
title: Cocos2d-x 3.x使用Sqlite3操作数据库
categories:
  - Develop
  - Cocos2d-x
tags:
  - Database
  - Sqlite
id: 851
date: 2014-11-03 11:46:33
---

好像新版的`Cocos2d-x`引擎包含了`sqlite3.h`,但是，在我引用时会提示`sqlite3_open()`无法解析，不知道怎么回事，所以我就不用他内置的了;
首先去[sqlite官网](http://www.sqlite.org)下载`sqlite`的源码文件，解压得到四个文件`shell.c`、`sqlite3.c`、`sqlite3.h`、`sqlite3ext.h`把这四个文件拷贝到项目`Classes`目录下，或者在此目录下再建立一个`sqlite`目录（便于代码的区分管理），然后右击项目`Classes`->`添加`->`现有项`，把这四个文件添加进来，就可以使用了，
新建`MLSqlite.cpp`和`MLSqlite.h`文件，这个就是我的操作`sqlite3`数据库的文件 

### MLSqlite.h
定义了几个简单的方法，简单的实现了数据库的增删改查
```C++
#ifndef _ml_sqlite_h_
#define _ml_sqlite_h_

#include "cocos2d.h"
#include "sqlitesqlite3.h"

USING_NS_CC;
using namespace std;

class MLSqlite {
public:
	static MLSqlite* getInstance();
	// 创建数据库
	void openDatabase(const char* dbname);
	// 创建数据表， 以sql语句的形式
	void createTable(const char* tablename, const char* sqlstr);
	// 插入数据， 以sql语句的形式
	void insertData(const char* tablename, const char* sqlstr);
	// 删除一条数据， 以sql语句的形式
	void deleteData(const char* tablename, const char* sqlstr);
	// 更新一条数据， 以sql语句的形式
	void updateData(const char* tablename, const char* sqlstr);

	/**
	 * 查询所有数据 传入处理查询结果的回调函数，
	 * 这个回调函数有三个参数，可以通过这三个参数来获得所有的数据
	 */
	void queryAllData(const char* tablename, const function<void (char**, int, int)>& callback);	
	/**
	 * 查询所有数据，并传入处理数据的回调函数
	 */
	void queryAllData(const char* tablename, int (*callback)(void*,int,char**,char**));	
	// 查询特定的数据
	string queryOneData(const char* tablename, const char* sqlstr, int i);	
	// 查询有多少条数据
	int queryData(const char* tablename, const char* sqlstr);	
	// 删除表
	void deleteTable(const char* tablename);
	// 关闭数据库
	void closeDatabase();
private:
	MLSqlite();
	~MLSqlite();

	sqlite3* mldb;
	char* error;
	int result;
};
#endif // _ml_sqlite_h_
```

### MLSqlite.cpp
实现以上那些个方法
```C++
#include "MLSqlite.h"

MLSqlite* m_MLSqlite = nullptr;

MLSqlite::MLSqlite(){
	mldb = NULL;
	error = "";
	result = 0;
}

MLSqlite::~MLSqlite(){
	mldb = NULL;
	error = "";
	result = 0;
	m_MLSqlite = nullptr;
}

// 单例模式
MLSqlite* MLSqlite::getInstance(){
	if(m_MLSqlite == nullptr){
		 m_MLSqlite = new MLSqlite();
	}
	return m_MLSqlite;
}

void MLSqlite::openDatabase(const char* dbname){
	string dbpath = FileUtils::getInstance()->getWritablePath() + dbname;
	// 打开数据库，如果数据库文件不存在则创建！
	result = sqlite3_open(dbpath.c_str(), &mldb);
	if(result != SQLITE_OK){
		log("open database fail %d", result);
	}
}

void MLSqlite::createTable(const char* tablename, const char* sqlstr){
	const char* sql = String::createWithFormat("create table %s (%s)", tablename, sqlstr)->getCString();
	result = sqlite3_exec(mldb, sql, NULL, NULL, &error);
	if(result != SQLITE_OK){
		log("create table fail %d, error:%s", result, error);
	}
}

// 添加插入数据
void MLSqlite::insertData(const char* tablename, const char* sqlstr){
	const char* sql = String::createWithFormat("insert into %s %s", tablename, sqlstr)->getCString();
	result = sqlite3_exec(mldb, sql, NULL, NULL, &error);
	if(result != SQLITE_OK){
		log("insert data fail %d, error:%s", result, error);
	}
}

/**
* 删除数据
* DELETE FROM table_name WHERE  {CONDITION};
*/
void MLSqlite::deleteData(const char* tablename, const char* sqlstr){
	const char* sql = String::createWithFormat("delete from %s %s", tablename, sqlstr)->getCString();
	result = sqlite3_exec(mldb, sql, NULL, NULL, &error);
	if(result != SQLITE_OK){
		log("delete data fail %d, error:%s", result, error);
	}
}

// 更新数据
void MLSqlite::updateData(const char* tablename, const char* sqlstr){
	const char* sql = String::createWithFormat("update %s set %s", tablename, sqlstr)->getCString();
	result = sqlite3_exec(mldb, sql, NULL, NULL, &error);
	if(result != SQLITE_OK){
		log("update data fail %d, error:%s", result, error);
	}
}

// 查询所有数据
void MLSqlite::queryAllData(const char* tablename, const function<void (char**, int, int)>& callback){
	char** cResult;
	int row, col;
	const char* sql = String::createWithFormat("select * from %s", tablename)->getCString();
	result = sqlite3_get_table(mldb, sql, &cResult, &row, &col, &error);
	callback(cResult, row, col);
}
void MLSqlite::queryAllData(const char* tablename, int (*callback)(void*,int,char**,char**)){
	const char* sql = String::createWithFormat("select * from %s", tablename)->getCString();
	// 如果想处理查询的数据，需要提供一个回调函数
	result = sqlite3_exec(mldb, sql, callback, NULL, &error);
	if(result != SQLITE_OK){
		log("query bad %d, error:%s", result, error);
	}
}

// 查询特定的数据
string MLSqlite::queryOneData(const char* tablename, const char* sqlstr, int i){
	char** cResult;
	int row, col;
	const char* sql = String::createWithFormat("select * from %s %s", tablename, sqlstr)->getCString();
	// 如果想处理查询的数据，需要提供一个回调函数，这里只需要反悔特定的数据
	result = sqlite3_get_table(mldb, sql, &cResult, &row, &col, &error);
	if(result != SQLITE_OK){
		log("query bad %d, error:%s", result, error);
	}
	return cResult[col + i];
}

// 查询有多少条数据
int MLSqlite::queryData(const char* tablename, const char* sqlstr){
	char** re;
	int row, col;
	const char* sql = String::createWithFormat("select * from %s %s", tablename, sqlstr)->getCString();
	result = sqlite3_get_table(mldb, sql, &re, &row, &col, &error);
	return row;
}

// 删除表
void MLSqlite::deleteTable(const char* tablename){
	const char* sql = String::createWithFormat("drop table %s", tablename)->getCString();
	result = sqlite3_exec(mldb, sql, NULL, NULL, &error);
	if(result != SQLITE_OK){
		log("drop table bad %d, error:%s", result, error);
	}
}

void MLSqlite::closeDatabase(){
	sqlite3_close(mldb);
}
```
OK 在需要操作数据库的地方引用这个文件，并调用其中的方法就