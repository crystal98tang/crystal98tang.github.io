---
layout: post
title: RESTful levels & HATEOAS
date: 2019-04-02
tags:
  - JAVA 
  - RESTful
categories: 
  - JAVA
---
==第六周WEB JAVA作业（1）==
<!-- more -->
## 什么是RESTful
REST这个词，是Roy Thomas Fielding在他2000年的博士论文中提出的。翻译过来就是"表现层状态转化。”

Fielding在论文中将REST定位为“分布式超媒体应用（Distributed Hypermedia System）”的架构风格，它在文中提到一个名为“HATEOAS（Hypermedia as the engine of application state）”的概念。

## HATEOAS又是什么鬼？
我们知道REST是使用标准的HTTP方法来操作资源的，但仅仅因此就理解成带CURD的Web数据库架构就太过于简单了。 这种说法忽略了一个核心概念: “超媒体即应用状态引擎（hypermedia as the engine of application state）”。 超媒体是什么? 当你浏览Web网页时，从一个连接跳到一个页面，再从另一个连接跳到另外一个页面，就是利用了超媒体的概念: 把一个个把资源链接起来。
要达到这个目的，就要求在表述格式里边加入链接来引导客户端。在《RESTFul Web Services》一书中，作者把这种具有链接的特性成为连通性。
RESTful API最好做到Hypermedia,或HATEOAS，即返回结果中提供链接，连向其他API方法，使得用户不查文档，也知道下一步应该做什么。比如，当用户向api.example.com的根目录发出请求，会得到这样一个文档。

{“link”: {
“rel”: “collection [https://www.example.com/zoos](https://www.example.com/zoos)”,
“href”: “[https://api.example.com/zoos](https://api.example.com/zoos)”,
“title”: “List of zoos”,
“type”: “application/vnd.yourformat+json”
}}
上面代码表示，文档中有一个link属性，用户读取这个属性就知道下一步该调用什么API了。rel表示这个API与当前网址的关系（collection关系，并给出该collection的网址），href表示API的路径，title表示API的标题，type表示返回类型。
Hypermedia API的设计被称为HATEOAS。Github的API就是这种设计，访问api.github.com会得到一个所有可用API的网址列表。

{
“current_user_url”: “[https://api.github.com/user](https://api.github.com/user)”,
“authorizations_url”: “[https://api.github.com/authorizations](https://api.github.com/authorizations)”,
// …
}
从上面可以看到，如果想获取当前用户的信息，[应该去访问api.github.com/user，然后就得到了下面结果。](http://xn--api-du3ey90cj37dtha474e.github.com/user%EF%BC%8C%E7%84%B6%E5%90%8E%E5%B0%B1%E5%BE%97%E5%88%B0%E4%BA%86%E4%B8%8B%E9%9D%A2%E7%BB%93%E6%9E%9C%E3%80%82)

{
“message”: “Requires authentication”,
“documentation_url”: “[https://developer.github.com/v3](https://developer.github.com/v3)”
}
以上内容都摘自阮一峰和其它作者博客，如有冒犯，请及时告知
我当时第一眼看到HATEOAS也是一脸懵逼，因为在Spring依赖中看到过这个词，所以就留意了一下。其实在我看来，HATEOAS是符合RESTful规范的一个方面，客户端在消费RESTful服务的时候，出了得到资源本身以外，还可以得到一些相关其他信息，比如，其他相关链接，返回类型等等。
2.构建RESTful服务最佳实践
第一条也是最容易犯错的：URI中不应该包含动词。 因为"资源"表示一种实体，所以应该是名词，URI不应该有动词，动词应该放在HTTP协议中。举例来说，某个URI是/posts/show/1，其中show是动词，这个URI就设计错了，正确的写法应该是/posts/1，然后用GET方法表示show。如果某些动作是HTTP动词表示不了的，你就应该把动作做成一种资源。比如网上汇款，从账户1向账户2汇款500元，错误的URI是：POST /accounts/1/transfer/500/to/2,正确的写法是把动词transfer改成名词transaction，然后以参数的方式注明其它参数
POST /accounts/transaction?from=1&to=2&amount=500.00
RESTful API最好做到Hypermedia（HATEOAS），即返回结果中提供链接，连向其他API方法，使得用户不查文档，也知道下一步应该做什么。
其它需要注意的地方参见文末贴出的链接
下面重头戏来了：
3.使用SpringBoot构建符合Hypermedia规范的RESTful 服务
我以后每次都要说一遍：SpringBoot框架是所有Java开发者的福音
在SpringBoot中构建符合Hypermedia规范的RESTful 服务简单到不能再简单-----只需要添加一条依赖：

org.springframework.boot spring-boot-starter-data-rest 添加一个简单的领域类：

@Entity
public class Person {
@Id
@GeneratedValue(strategy = GenerationType.AUTO)
private long id;

```
private String firstName;
private String lastName;

//getter and setter

```

}
以及一个dao层接口：

//@RepositoryRestResource(collectionResourceRel = “people”,path=“people”)
public interface PersonRepository extends PagingAndSortingRepository<Person,Long>{
List findByLastName(@Param(“name”) String name);
}

注释掉的标签可选，主要是在只用RESTful的时候可以改变URI，比如，加上此处就把/person变成/people

一切都和我们正常开发web没啥区别，但是现在，见证奇迹的时刻到了：
1.GET localhost:8080
返回：

{
“_links”:{
“people”:{
“href”: “[http://localhost:8080/people{?page,size,sort}](http://localhost:8080/people%7B?page,size,sort%7D)”,
“templated”: true
},
“profile”:{
“href”: “[http://localhost:8080/profile](http://localhost:8080/profile)”
}
}
}

上面的返回中包括了people这个资源的链接明确指出了我们可以用类似http://localhost:8080/people?page=1&size=10&sort=firstname这样的方式请求资源。

2.增加一个people资源：POST localhost:8080/people,请求数据用json{ “firstName” : “李”, “lastName” : “雷” }
返回：

{
“firstName”: “李”,
“lastName”: “雷”,
“_links”:{
“self”:{
“href”: “[http://localhost:8080/people/5](http://localhost:8080/people/5)”
},
“person”:{
“href”: “[http://localhost:8080/people/5](http://localhost:8080/people/5)”
}
}
}
返回信息中出了新加入信息的各个字段，还有一个href链接指向它–这是合乎情理的，客户端总是想要看看新加入的这条信息长什么样，从这个角度说，这条返回信息还是很贴心的。
3.GET localhost:8080/people/

{
“_embedded”:{
“people”:[
{
“firstName”: “李”,
“lastName”: “雷”,
“_links”:{“self”:{“href”: “[http://localhost:8080/people/5](http://localhost:8080/people/5)” }, “person”:{“href”: “[http://localhost:8080/people/5](http://localhost:8080/people/5)”…}
}
]
},
“_links”:{
“self”:{
“href”: “[http://localhost:8080/people](http://localhost:8080/people)”
},
“profile”:{
“href”: “[http://localhost:8080/profile/people](http://localhost:8080/profile/people)”
},
“search”:{
“href”: “[http://localhost:8080/people/search](http://localhost:8080/people/search)”
}
},
“page”:{
“size”: 20,
“totalElements”: 1,
“totalPages”: 1,
“number”: 0
}
}

返回一个people列表，包含所有数据
page标签的出现，是由于我们的repository继承了PagingAndSortingRepository接口
search标签的出现，是由于我们的repository声明了一个方法，List findByLastName(@Param(“name”) String name);这个方法可以像search标签描述的那样调用：[http://localhost:8080/people/search/findByLastName{?name},示例：http://localhost:8080/people/search/findByLastName?name=雷](http://localhost:8080/people/search/findByLastName%7B?name%7D,%E7%A4%BA%E4%BE%8B%EF%BC%9Ahttp://localhost:8080/people/search/findByLastName?name=%E9%9B%B7)

4.PUT localhost:8080/people/5,json：{ “firstName” : “李”, “lastName” : “小雷” }

{
“firstName”: “李”,
“lastName”: “小雷”,
“_links”:{
“self”:{
“href”: “[http://localhost:8080/people/5](http://localhost:8080/people/5)”
},
“person”:{
“href”: “[http://localhost:8080/people/5](http://localhost:8080/people/5)”
}
}
}
资源被正确更新
5.PATCH localhost:8080/people/5,json：{ “lastName” : “大雷” }

{
“firstName”: “李”,
“lastName”: “大雷”,
“_links”:{
“self”:{
“href”: “[http://localhost:8080/people/5](http://localhost:8080/people/5)”
},
“person”:{
“href”: “[http://localhost:8080/people/5](http://localhost:8080/people/5)”
}
}
}
