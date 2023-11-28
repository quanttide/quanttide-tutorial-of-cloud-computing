# 目录
- ORM介绍与安装
- 建立ORM数据模型
- 数据库基本操作（增删改查）
- 利用已有数据库生成数据模型

## ORM介绍与安装
ORM全称**Object Relational Mapping**（对象关系映射）。特点是**操纵Python对象**而不是SQL查询，也就是在代码层面考虑的是对象，而不是SQL，体现的是一种程序化思维，这样使得Python程序更加简洁易读。
具体的实现方式是将**数据库表转换为Python类**，其中数据列作为属性，数据库操作作为方法。

使用ORM的优点如下：
- 简洁易读：将数据表抽象为对象（数据模型），更直观易读
- 可移植：封装了多种数据库引擎，面对多个数据库，操作基本一致，代码易维护
- 更安全：有效避免SQL注入

本教程使用python的SQLalchemy库使用ORM框架搭建并操作数据库：
```pycon
pip install sqlalchemy
```

## 建立ORM数据模型
### 预备知识
在建立我们的ORM数据模型之前，我们需要了解ORM模型与真实的数据库模型之间一一对应的关系，
如下表所示：

 **概念**  | **对应数据库** | **说明**     
---------|-----------|------------
 Engine  | 连接        | 驱动引擎       
 Session | 连接池，事务    | 由此开始查询     
 Model   | 表         | 类定义        
 Column  | 列         | 支持运算符操作    
 Query   | 若干行       | 可以链式添加多个条件 

下面针对我们不同的业务需求，我们会不断用到上表中的engine，session，model，column和query。
在这里我们先理解每一个Model对应一张表，每一个Column对应表中的一列。

由于我们是在python脚本之中操作，那么数据类型也应该和python中的类一一对应的，具体如下表所示：

 **数据类型** | **数据库数据类型** | **python数据类型**    | **说明**      
----------|-------------|-------------------|--------------
 Integer  | int         | int               | 整型           
 String   | varchar     | string            | 字符串          
 Text     | text        | string            | 长字符串         
 Float    | float       | float             | 浮点型          
 Boolean  | tinyint     | bool              | 布尔型          
 Date     | date        | datetime.date     | 存储时间年月日      
 Datetime | datetime    | datetime.datetime | 存储年月日时分秒毫秒等  
 Time     | time        | datetime.datetime | 存储时分秒        

### 建立数据库连接
sqlalchemy库中的create_engine()方法用来初始化数据库连接。SQLAlchemy用一个字符串表示连接信息：
```
'数据库类型+数据库驱动名称://用户名:密码@数据库URL地址:端口号/数据库名'
```
其中数据库类型本教程默认为mysql，数据库驱动名称可以是pymysql，mysqlconnector等。

示例如下：
```python
from sqlalchemy import create_engine

engine = create_engine('mysql+pymysql://test:j78>=mVOPJJQo_c$@cdb-oasqez4f.gz.tencentcdb.com:10139/test?charset=utf8')
```

### 建立数据模型
首先，我们需要新建一个元类，然后让我们所有的模型都以这个元类为父类。
```python
from sqlalchemy.ext.declarative import declarative_base

Base = declarative_base()
```
接下来就可以建我们的数据模型啦！

我们以前面一讲中的数据模型为例。__tablename__是确定该表的表名，之后再用Column类生成每一列。
其中我们必须确定**数据类型**（Integer，String，Text，Float），是否为主键（默认为False），是否为空
（默认为True）。除此之外我们还可以添加表约束如（UniqueConstraint，CheckConstraint等）

```python
from sqlalchemy import Column, String, Integer, CheckConstraint, ForeignKey

class Course(Base):
    __tablename__ = 'courses'
    course_id = Column(String(50), primary_key=True)
    course_num = Column(String(10), nullable=False)
    student_quantity = Column(Integer, nullable=False)
    teacher_in_charge = Column(String(10), nullable=False)

    __table_args__ = (
        CheckConstraint('student_quantity >= 0'),
    )


class Student(Base):
    __tablename__ = 'students'
    student_id = Column(String(50), primary_key=True)
    name = Column(String(10), nullable=False)
    course_id = Column(String(50), ForeignKey('courses.course_id'))
    gender = Column(String(2), default='M')
    age = Column(Integer, nullable=False)

    __table_args__ = (
        CheckConstraint('age >= 18'),
    )
```

### 完整代码示例
```python
from sqlalchemy import create_engine
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy import Column, Integer, String, ForeignKey, CheckConstraint


engine = create_engine('mysql+pymysql://test:j78>=mVOPJJQo_c$@cdb-oasqez4f.gz.tencentcdb.com:10139/test?charset=utf8')
Base = declarative_base()


class Course(Base):
    __tablename__ = 'courses'
    course_id = Column(String(50), primary_key=True)
    course_num = Column(String(10), nullable=False)
    student_quantity = Column(Integer, nullable=False)
    teacher_in_charge = Column(String(10), nullable=False)

    __table_args__ = (
        CheckConstraint('student_quantity >= 0'),
    )


class Student(Base):
    __tablename__ = 'students'
    student_id = Column(String(50), primary_key=True)
    name = Column(String(10), nullable=False)
    course_id = Column(String(50), ForeignKey('courses.course_id'))
    gender = Column(String(2), default='M')
    age = Column(Integer, nullable=False)

    __table_args__ = (
        CheckConstraint('age >= 18'),
    )


if __name__ == '__main__':
    Base.metadata.create_all(engine)
```

## 数据库基本操作
sqlalchemy中使用session用于创建程序和数据库之间的会话，所有对象的载入和保存都需要通过session对象 。
通过sessionmaker调用可以创建一个工厂，并关联Engine以确保每个session都可以使用该Engine连接资源：

```python
from sqlalchemy.orm import sessionmaker

DbSession = sessionmaker(bind=engine)
session = DbSession()
```

session的常见操作方法包括：

- flush：预提交，提交到数据库文件，还未写入数据库文件中
- commit：提交了一个事务
- rollback：回滚
- close：关闭

### 增
```python
import uuid

session.add(Course(course_id=str(uuid.uuid4()), course_num='205', student_quantity=40, teacher_in_charge='邹老师'))
session.add(Course(course_id=str(uuid.uuid4()), course_num='206', student_quantity=38, teacher_in_charge='苏老师'))
session.add(Course(course_id=str(uuid.uuid4()), course_num='207', student_quantity=41, teacher_in_charge='林老师'))
session.add(Course(course_id=str(uuid.uuid4()), course_num='208', student_quantity=39, teacher_in_charge='赵老师'))
session.commit()
```

### 删
删有如下两种办法，当批量删除时使用第二种方法：
```python
delete_users = session.query(Course).filter(Course.course_num == "207").first()
if delete_users:
    session.delete(delete_users)
    session.commit()
```
```python
session.query(Course).filter(Course.course_num == "207").delete()
session.commit()
```

### 改
更新数据有两种方法，一种是使用query中的update方法：
```python
session.query(Course).filter(Course.course_num == "207").update({'teacher_in_charge': "王老师"})
```
另外一种是操作对应的表模型：
```python
courses = session.query(Course).filter(Course.course_num == "207").first()
courses.teacher_in_charge = "王老师"
session.add(courses)
session.commit()
```

### 查
通常我们通过以下查询模式获取数据，需要注意的是，通过session.query()我们查询返回了一个Query对象，
此时还没有去具体的数据库中查询，只有当执行具体的.all()，.first()等函数时才会真的去操作数据库。
```python
courses = session.query(Course).all()
for c in courses:
    print(c.course_num)
```

## 利用已有数据库生成数据模型
现有项目或者别人的代码里如果已经用其他的方式写好了表定义，不想再定义Model了，想用SQLAlchemy直接使用对应的数据库表。
文档地址：https://docs.sqlalchemy.org/en/14/orm/extensions/automap.html

```python
from sqlalchemy.ext.automap import automap_base
from sqlalchemy import create_engine
from sqlalchemy.orm import Session
import uuid

Base = automap_base()
engine = create_engine('mysql+pymysql://test:j78>=mVOPJJQo_c$@cdb-oasqez4f.gz.tencentcdb.com:10139/test?charset=utf8')
Base.prepare(engine, reflect=True)

Course = Base.courses.course

session = Session(engine)
session.add(Course(course_id=str(uuid.uuid4()),course_num='210', student_quantity=45, teacher_in_charge='李老师'))
session.commit()
```

如以上代码所示，我们首先新建了一个automap元类，prepare方法会调用metadata.reflect()方法，
然后所有在metadata里面的表会被自动创建成对应的类。如ForeignKeyConstraint对象则会创建成不同类之间的relationship对象。
之后我们遍可以通过操纵类来操纵表。