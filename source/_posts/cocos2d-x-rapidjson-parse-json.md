---
title: Cocos2d-x 3.x 使用rapidjson解析json文件
categories:
  - Develop
  - Cocos2d-x
tags:
  - Cocos2d-x
  - RapidJson
  - Json
id: 10041
date: 2014-11-02 11:26:53
---

之前在写一个小游戏的时候要把一些`关卡`、`属性`等数据保存在`json`里边，供代码读取，所以就看了下关于`Cocos2d-x`中解析`json`的一些方法，发现在`3.x`里只集成有`json`解析库的，就是`rapidjson`，还挺好用，然后稍微对解析`json`数据进行了下面的封装；

先留个坑吧 这里只是简单的记录下怎么解析的，代码中也有注释，就不一行行的解释了！有时间再详细的说说

### 首先是解析字符串数据
```c++
//根据传入的参数获取字符串（json最外围格式为Object格式）
string MyFunc::getJsonString(const char* alias, const char* filename){

	rapidjson::Document doc;

	//判断文件是否存在
	if(!FileUtils::getInstance()->isFileExist(MyFunc::plusChar(g_file_text_path.c_str(), filename))){
		log("json file not exist!!!");
		return "";
	}
	//读取json文件并初始化 doc
	string data = FileUtils::getInstance()->getStringFromFile(MyFunc::plusChar(g_file_text_path.c_str(), filename));
	doc.Parse<rapidjson::kparsedefaultflags>(data.c_str());

	//判断读取成功与否 和 是否为数组类型  
	if (doc.HasParseError() || !doc.IsArray()) {
		log("get json data err!");  
		return "";
	}  
	int count = 0;
	rapidjson::Value& texts = doc[count];

	string text = texts[alias].GetString();
	log("text: %s", text.c_str());

	return text;
}
```

### 当要解析的json数据包含有数组格式时，
```C++
// 根据传入的参数获取字符串（json最外围格式为Object格式）内部包含array格式数据
void MyFunc::getJsonArray(const char* alias, const char* filename, vector<string> &words){
	rapidjson::Document doc;
	if(!FileUtils::getInstance()->isFileExist(filename)){
		log("json file is not exist!!");
		return;
	}
	std::string data = FileUtils::getInstance()->getStringFromFile(filename);
	doc.Parse<rapidjson::kparsedefaultflags>(data.c_str());
	if(doc.HasParseError() || !doc.IsObject()){
		log("parse json error!!");
		return;
	}  
	rapidjson::Value& texts = doc[alias];
	for(int i=0; i<texts .Size(); i++){
		words.push_back(texts[i].GetString());
	}
}
```