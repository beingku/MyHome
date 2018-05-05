# Django的模型关系说明（二）

>Author: Sinpei 
>Date: 2018-4-26
>CSDN: https://blog.csdn.net/u012581982
>GitHub: https://github.com/sinpeilee/MyHome
>Email: sinpeilee@163.com 

---
### 前言
>该文档中主要介绍模型的对应关系，一对一，一对多，以及多对多的关系。并且举例说明 模型对应关系描述如下： 1：1 一对一 OneToOneField 1：N 一对多 ForeignKey M：N 多对多 ManyToManyField 常见的几种数据关系，django都提供了很好的支持。

关于Django的模型基础知识，请见Django开发知识（二）

---
### 模型的关联关系

#### 一对一关联关系（OneToOneField)
> 这种关联关系，主键和外键是一对一的关系，在关联表中只能关联一个主表的id。
> 一对一关联关系主要是应用于：表的拓展以及实际业务需求。
```python
# 创建模型
class Student(models.Model):
    s_name = models.CharField(max_lenth=10)
  
    class Meta:
        db_table = 'dbname_tbname'
    
class StudentInfo(models.Model):
    s_addr = models.CharField(max_lenth=50)
    # 使用OneToOneField()
    s = models.OneToOneField(Student, on_delete=models.CASCADE,
    related_name='stu_info')
  
```

```python
# 业务实现
    # 查询叫阿良学生的信息
    # 通过学生表来查学生扩展表
    stu = Student.objects.filter(s_name='阿良').first()
    selstu = stu.stu_info

```
#### 属性说明
> 说明：数据库表命名规范：数据库名_表名。
> related_name:为该项设置别名, 调用时必须就得用别名了。
> on_delete：关联表删除后指定的行为。
> - CASCADE: 这就是默认的选项，级联删除，你无需显性指定它。
> - PROTECT: 保护模式，如果采用该选项，删除的时候，会抛出ProtectedError错误。
> - SET_NULL: 置空模式，删除的时候，外键字段被设置为空，前提就是blank=True, null=True,定义该字段的时候，允许为空。
> - SET_DEFAULT: 置默认值，删除的时候，外键字段设置为默认值，所以定义外键的时候注意加上一个默认值。
> - SET(): 自定义一个值，该值当然只能是对应的实体了。


  

---
#### 一对多关联关系（ForeignKey）
> 这种关联关系，主键和外键是一对多的关系，在关联表中能关联多个主表的id。
> 一对多关联关系主要是应用于：表的依赖以及实际业务需求。
```python
# 模型构建
class Grade(models.Modle):
    g_name = models.CharField(max_lenth=20)
    
    class Meta:
        db_table = 'dbname_tbname'

class Student(models.Modle):
    s_name = models.CharField(max_lenth=20)
    s_grade = models.ForeigKey(Grade, on_delete=PROTECT)
    
    class Meta:
        db_table = 'dbname_tabname'
        
```
```python
# 业务逻辑
# 查询班级的学生
g = Grade.objects.all().first()
s = g.student_set.all()
```

___
#### 多对多关联关系（ManyToManyField）
> 多对多表关系建立之后，会自动生产一张表。
> 所生产的表用来维护两个表的关系。
> 生成的表是使用两个外键来维护多对多的关系。
> 两个一对多的关系来实现多对多的实现。
> 删除一个表的数据的话，中间关联表也要删除相关的信息。

> 该关系主要应用于：如用户与商品的关系等实际业务需求。

```python
# 模型构建
class User(models.Modle):
    u_name = models.CharField(max_lenth=20)

class Goods(models.Modle):
    g_name = models.CharField(max_lenth=20)
    g_user = models.ManytoMantField(User)
```
```python
# 业务逻辑
# 获取第一个用户购买了哪些商品
u = User.object.all().first()
ug = u.goods_set.all()

# 获取指定用户的购买商品
u = User.object.filter(id=1).first()
ug = u.goods_set.add()
```

说明：部分概念转摘自[GitHub - coco369/knowledge](https://github.com/coco369/knowledge)
