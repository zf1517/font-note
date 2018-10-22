Sequelize Learning Note
======================
Sequelize是一个基于 promise 的 Node.js ORM，将数据库的表映射为javaScript对象，使用nodeJs语法间接进行数据库操作。
### 基本示例
#### 建立与数据库连接
```js
	const Sequelize = required('sequelize');
	const sequelize = new Sequelize('database','username','password',{
		host: 'localhost',
  		dialect: 'mysql'|'sqlite'|'postgres'|'mssql',
  		//禁用修改表名; 默认情况下，sequelize将自动将所有传递的模型名称（define的第一个参数）转换为复数。 
		define: {
    		freezeTableName: true,
  		},
  		pool: {
   			 max: 5,
   			 min: 0,
    		 acquire: 30000,
    		 idle: 10000
 			 },
		})
	// 或者你可以简单地使用 uri 连接
	const sequelize = new Sequelize('postgres://user:pass@example.com:5432/dbname');
```
#### 测试连接
```js
sequelize
  .authenticate()
  .then(() => {
    console.log('Connection has been established successfully.');
  })
  .catch(err => {
    console.error('Unable to connect to the database:', err);
  });
```
#### 建立表模型
```js
const User = sequelize.define('user', {
  firstName: {
    type: Sequelize.STRING
  },
  lastName: {
    type: Sequelize.STRING
  }
});

// force: true 如果表已经存在，将会丢弃表
User.sync({force: true}).then(() => {
  // 表已创建
  return User.create({
    firstName: 'John',
    lastName: 'Hancock'
  });
});
```
#### 简单查询
```js
User.findAll().then(users => {
  console.log(users)
})
```

### 增、刪、改、查
#### Create-增
1.构建非持久化实例
```js
Task
  .build({ title: 'foo', description: 'bar', deadline: new Date() })
  .save()
  .then(anotherTask => {
    // 您现在可以使用变量 anotherTask 访问当前保存的任务
  })
  .catch(error => {
    // Ooops，做一些错误处理
  })
```
2.创建持久性实例
```js
Task.create({ title: 'foo', description: 'bar', deadline: new Date() }).then(task => {
  // 你现在可以通过变量 task 来访问新创建的 task
})
```
#### Destory - 删除
```js
task.destroy({ force: true })
```
#### Update - 修改
```js
task.update({
  title: 'a very different title now'
}).then(() => {})
```
#### Querying - 查询
*find  - 搜索数据库中的一个特定元素
*findAll - 搜索数据库中的多个元素
*max - 获取特定表中特定属性的最大值
*sum - 特定属性的值求和
```js
// 获取10个实例/行
Project.findAll({ limit: 10 })

// 跳过8个实例/行
Project.findAll({ offset: 8 })

// 跳过5个实例，然后取5个
Project.findAll({ offset: 5, limit: 5 })
```
###Association - 关联
1.BelongsTo
2.HasOne
3.HasMany
4.BelongsToMany