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



