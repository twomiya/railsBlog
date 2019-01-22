## rails note
### Active Record

#### 什么是Active Record
* Active Record 是MVC的M（模型），负责处理数据和业务逻辑
* 负责创建和使用需要持久存入数据库的数据
* Active Record实现了Active Record模式，是一种对象关系映射系统

#### Active Record模式
* 在Active Record模式中，对象中既有持久存储的数据，也有针对数据的操作。
* Active Record模式把数据存取逻辑作为对象的一部分，处理对象的用户知道如何把数据写入数据库，
还知道如何从数据库中读出数据。

#### 对象关系映射
* 对象关系映射是一种技术手段，把应用中的对象和关系型数据库中的数据表连接起来。

#### 用作ORM框架的Active Record
* 表示模型和其中的数据
* 表示模型之间的关系
* 通过相关联的模型表示继承层次结构
* 持久存入数据库之前，验证模型
* 以面向对象的方式处理数据库操作

#### Active Record中的多约定少配置原则
* 命名约定
	* 数据库表名：复数，下划线分隔单词（book_clubs）
	* 模型类名：单数，每个单词首字母大写（BookClub）

* 模式约定
根据字段的作用不同，Active Record对数据库表中的字段命名也做了相应的约定	
	* 外键：使用`singularized_table_name_id`形式命名，例如`item_id`,`order_id`。创建模型关联后，Active Record会查找这个字段
	* 主键：默认情况下，Active Record使用整数字段id作为表的主键。使用Active Record迁移创建数据库表时，会自动创建这个字段
	* create_at：创建记录时，自动设为当前的日期和时间
	* updated_at：更新记录时，自动设为当前的日期和时间
	* lock_version：在模型中添加乐观锁
	* type：让模型使用单表继承
	* `(association_name)_type`：存储多态关联的类型
	* `(table_name)_count`：缓存所关联对象的数量。比如说，一个 Article 有多个 Comment，那么 comments_count 列存储各篇文章现有的评论数量

#### 创建Active Record模型
* 创建 Active Record 模型的过程很简单，只要继承 ApplicationRecord 类
	```
		class Product < ApplicationRecord
		end
	```
* 覆盖命名约定
	```
		class Product < ApplicationRecord
			self.table_name = "my_products"
		end
	```

#### CRUD：读写数据
	CURD是四种数据操作的简称：C表示创建，R表示读取，U表示更新，D表示删除。Active Record自动创建了处理数据表中的数据的方法
* 创建
	* Active Record对象可以使用散列创建，在块中创建，或者创建后手动设置属性。new方法创建一个新的对象，create方法创建新对象，并将其存入数据库
	* create方法
		```
			user = User.create(name: "David", occupation: "Code Artist")
		```
	* new方法实例化一个新对象，但不保存
		```
			user = User.new
			user.name = "David"
			user.occupation = "Code Artist"
		```
	* 如果在create和new方法中使用块，会把新创建的对象拉入块中，初始化对象：
		```
			user = User.new do |u| 
				u.name = "David" 
				u.occupation = "Code Artist" 
			end
		```
* 读取
	* 返回所有用户组成的集合
		`users=User.all`
	* 返回第一个用户
		`user=User.first`	
	* 返回第一个名为David的用户
		`david=User.find_by(name:'David')`
	* 查找所有名为David，职业为Code Artists的用户，而且按照created_at	反向排列
		`users=User.where(name:'David',occupation:'Code Artist').order(created_at: :desc)`
* 更新
	* 检索到Active Record 对象后，可以修改其属性，然后再将其存入数据库	
		```
			user = User.find_by(name: 'David') 
			user.name = 'Dave' 
			user.save
		```	
	* 使用散列的简写方式，指定属性名和属性值
		```
			user = User.find_by(name: 'David') 
			user.update(name: 'Dave')
		```
	* 一次更新多个属性时使用这种方法最方便。如果想批量更新多个记录，可以使用类方法update_all:
		`User.update_all "max_login_attempts = 3, must_change_password = 'true'"`
* 删除
	* 检索到Active Record对象后还可以将其销毁，从数据库中删除
		```
			user = User.find_by(name: 'David') 
			user.destroy
		```

#### 数据验证
* 把数据存入数据库之前进行验证是十分重要的步骤，所以调用save和update方法时会做数据验证。验证失败时返回false，此时不会对数据库做任何操作。这两个方法都有对应的爆炸方法（save！和update！）。爆炸方法要严格一些，如果验证失败，抛出ActiveRecord::RecordInvalid异常。
	```
	class User < ApplicationRecord 
		validates :name, presence: true 
	end
	user = User.new 
	user.save # => false 
	user.save! # => ActiveRecord::RecordInvalid: Validation failed: Name can't be blank
	```

#### 回调
* Active Record 回调用于在模型生命周期的特定事件上绑定代码，相应的事件发生时，执行绑定的代码。例如创建新纪录时、
更新记录时、删除记录时等等

#### 迁移
* Rails提供了一个DSL用来处理数据库模式。叫做“迁移”。迁移的代码存储在特定的文件中，通过rails命令执行，
可以用在Active Record支持的所有数据库上。
* 创建表 `rails db:migrate`
* 回滚 `rails db:rollback`
