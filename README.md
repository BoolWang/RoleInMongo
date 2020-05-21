# RoleInMongo

Mongodb中的角色介绍

## 1.概念介绍

### 1.1 访问控制Access Control

`Mongo`默认没有访问控制，用户需要通过--auth或者`security.authorization`来使得访问控制可用，启用了内部的身份验证就可以通过客户端来连接数据库

### 1.2 角色Roles

角色具有特定的权限，可以授权给用户，用户拥有角色后就可以对指定的资源执行指定的操作；

角色的权限可以显示指定，也可以从其它角色继承

### 1.3 权限Privileges

权限包括指定的资源和对该资源允许的操作组成

`资源`可以是数据库、集合等

### 1.4 用户和角色

可以在用户的创建过程中为用户分配角色，也可以更新现有用户的角色

```sql
#新建用户user1，并为其分配对于数据库db1的readWrite权限
db.createUser(
... {
... user:"user1",
... pwd:"*******",
... roles:[{role:"readWrite",db:"db1"}]
... })
```

```sql
#对现有用户user1进行角色更新，使其对数据库db1具有dbAdmin角色权限，会覆盖其之前所有的角色权限
 db.updateUser(
... "user1",
... {
... roles:[{role:"dbAdmin",db:"db1"}]
... })
```

```
#updateUser()会覆盖其之前所有的角色权限
>db.getUser("user1")
{
	"_id" : "db1.user1",
	"user" : "user1",
	"db" : "db1",
	"roles" : [
		{
			"role" : "readWrite",
			"db" : "db1"
		}
	]
}
```

```
#对现有用户user1新增角色权限，不会覆盖之前的角色
db.grantRolesToUser(
... "user1",
... [{role:"readWrite",db:"db1"}]
... )
#同样还可以通过revokeRolesFromUser()为用户移除角色
db.revokeRolesFromUser(
... "user1",
... [{role:"readWrite",db:"db1"}]
... )
```

更多内容见[Mongodb官网]( https://docs.mongodb.com/manual/reference/method/js-user-management/)

## 2.角色

### 2.1 数据库用户角色Database User Roles

#### read

允许读取当前数据库的非系统类集合以及`system.js`(包含特殊的 JavaScript 代码，用于服务器端 JavaScript)合，可执行的操作如下表：

| [`changeStream`](https://docs.mongodb.com/manual/reference/privilege-actions/#changeStream) | [`collStats`](https://docs.mongodb.com/manual/reference/privilege-actions/#collStats) | [`dbHash`](https://docs.mongodb.com/manual/reference/privilege-actions/#dbHash) |
| :----------------------------------------------------------: | ------------------------------------------------------------ | ------------------------------------------------------------ |
| [`dbStats`](https://docs.mongodb.com/manual/reference/privilege-actions/#dbStats) | [`find`](https://docs.mongodb.com/manual/reference/privilege-actions/#find) | [`killCursors`](https://docs.mongodb.com/manual/reference/privilege-actions/#killCursors) |
| [`listIndexes`](https://docs.mongodb.com/manual/reference/privilege-actions/#listIndexes) | [`listCollections`](https://docs.mongodb.com/manual/reference/privilege-actions/#listCollections) |                                                              |

#### readWrite

允许读写当前数据库的非系统类集合以及`system.js`，可执行的操作如下表：

| [`collStats`](https://docs.mongodb.com/manual/reference/privilege-actions/#collStats) | [`convertToCapped`](https://docs.mongodb.com/manual/reference/privilege-actions/#convertToCapped) | [`createCollection`](https://docs.mongodb.com/manual/reference/privilege-actions/#createCollection) | [`dbHash`](https://docs.mongodb.com/manual/reference/privilege-actions/#dbHash) |
| :----------------------------------------------------------: | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| [`dbStats`](https://docs.mongodb.com/manual/reference/privilege-actions/#dbStats) | [`dropCollection`](https://docs.mongodb.com/manual/reference/privilege-actions/#dropCollection) | [`createIndex`](https://docs.mongodb.com/manual/reference/privilege-actions/#createIndex) | [`dropIndex`](https://docs.mongodb.com/manual/reference/privilege-actions/#dropIndex) |
| [`find`](https://docs.mongodb.com/manual/reference/privilege-actions/#find) | [`insert`](https://docs.mongodb.com/manual/reference/privilege-actions/#insert) | [`killCursors`](https://docs.mongodb.com/manual/reference/privilege-actions/#killCursors) | [`listIndexes`](https://docs.mongodb.com/manual/reference/privilege-actions/#listIndexes) |
| [`listCollections`](https://docs.mongodb.com/manual/reference/privilege-actions/#listCollections) | [`remove`](https://docs.mongodb.com/manual/reference/privilege-actions/#remove) | [`renameCollectionSameDB`](https://docs.mongodb.com/manual/reference/privilege-actions/#renameCollectionSameDB) | [`update`](https://docs.mongodb.com/manual/reference/privilege-actions/#update) |

### 2.2 数据库管理角色Database Administration Roles

#### dbAdmin

允许对当前数据库进行管理任务(如数据库架构相关任务、索引和收集统计信息等)，`但不能对数据库的用户和角色进行管理`，可执行的操作如下表：

| [`changeStream`](https://docs.mongodb.com/manual/reference/privilege-actions/#changeStream) | [`dropCollection`](https://docs.mongodb.com/manual/reference/privilege-actions/#dropCollection) | [`collStats`](https://docs.mongodb.com/manual/reference/privilege-actions/#collStats) | [`find`](https://docs.mongodb.com/manual/reference/privilege-actions/#find) |
| :----------------------------------------------------------: | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| [`convertToCapped`](https://docs.mongodb.com/manual/reference/privilege-actions/#convertToCapped) | [`killCursors`](https://docs.mongodb.com/manual/reference/privilege-actions/#killCursors) | [`createCollection`](https://docs.mongodb.com/manual/reference/privilege-actions/#createCollection) | [`listCollections`](https://docs.mongodb.com/manual/reference/privilege-actions/#listCollections) |
| [`dbHash`](https://docs.mongodb.com/manual/reference/privilege-actions/#dbHash) | [`listIndexes`](https://docs.mongodb.com/manual/reference/privilege-actions/#listIndexes) | [`dbStats`](https://docs.mongodb.com/manual/reference/privilege-actions/#dbStats) | [`planCacheRead`](https://docs.mongodb.com/manual/reference/privilege-actions/#planCacheRead) |

#### userAdmin

允许对当前数据库的用户和角色进行管理，因此如果是admin数据库中的userAdmin，则相当于间接拥有了超级用户的权限，可允许的操作如下表：

| [`changeCustomData`](https://docs.mongodb.com/manual/reference/privilege-actions/#changeCustomData) | [`changePassword`](https://docs.mongodb.com/manual/reference/privilege-actions/#changePassword) | [`createRole`](https://docs.mongodb.com/manual/reference/privilege-actions/#createRole) | [`createUser`](https://docs.mongodb.com/manual/reference/privilege-actions/#createUser) |
| :----------------------------------------------------------: | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| [`dropRole`](https://docs.mongodb.com/manual/reference/privilege-actions/#dropRole) | [`dropUser`](https://docs.mongodb.com/manual/reference/privilege-actions/#dropUser) | [`grantRole`](https://docs.mongodb.com/manual/reference/privilege-actions/#grantRole) | [`revokeRole`](https://docs.mongodb.com/manual/reference/privilege-actions/#revokeRole) |
| [`setAuthenticationRestriction`](https://docs.mongodb.com/manual/reference/privilege-actions/#setAuthenticationRestriction) | [`viewRole`](https://docs.mongodb.com/manual/reference/privilege-actions/#viewRole) | [`viewUser`](https://docs.mongodb.com/manual/reference/privilege-actions/#viewUser) |                                                              |



