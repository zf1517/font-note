Sequelize Learning Note
======================
Sequelize是一个基于 promise 的 Node.js ORM，将数据库的表映射为Model，使用nodeJs语法间接进行数据库操作。
# 基本示例
### 建立与数据库连接
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
### 测试连接
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
### 建立表模型
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
### 简单查询
```js
User.findAll().then(users => {
  console.log(users)
})
```
