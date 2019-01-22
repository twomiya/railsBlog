# rails note
## Active Record

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

