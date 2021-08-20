## Linq学习

### 什么是Linq

​	[微软官方文档](https://docs.microsoft.com/zh-cn/dotnet/csharp/programming-guide/concepts/linq/)

### 查询操作的三个步骤

1. #### 获取数据源

2. #### 创建查询

3. #### 执行查询

```csharp
class Linq_Prac
{
    static void Main()
    {
        //1.获取数据源
        int[] numbers = {1, 5, 6, 9, 0, 2, 4, 3, 7, 8 };
        //2.创建查询
        var numQuery = 
            from num in numbers
            where(num % 2) == 0
            select num;
        //3.执行查询
		Console.WriteLine("偶数:" + string.Join(",", numQuery));
    }
}
```

## Linq语法

### Where

与sql命令中的Where作用相似，起到范围限定的过滤作用。Where操作包括三种形式、分别是简单形式、关系条件形式，First()形式,下面举例说明。

1. 简单形式

   使用where筛选集合中小于5的数

   ```csharp
   var lowNums = 
       from n in numbers 
       where n < 5 
       select n; 
   ```

2. 关系条件形式

   筛选出来自Lake City，但是考试成绩存在不及格的学生

   ```csharp
   var q =
       from p in students
       where p.City == "Lake City" && p.Scores.Any(p => p < 60)
       select p;
   ```

3. First()形式

   选择集合中的第一个元素

   ```csharp
   var firstNum = numbers.First() 
   ```

   选择集合中第一个>5的元素

   ```csharp
   var firstNum = numbers.First(n => n > 5)
   ```

### Select

和sql中的select作用相似但是位置不同，起到将查询结果返回的作用。

1. 简单形式

   返回学生姓氏序列

   ```csharp
   var q =
       from p in students
       select p.Last;
   ```

   ==注意==

   该语句只是一个声明，并未真正的将数据取出，只有当你用到该数据的时候，才会执行该语句，只是一种延迟加载的模式。

   

2. 匿名类型形式

   说明：匿名类型是C#3.0中新特性。其实质是编译器根据我们自定义自动产生一个匿名的类来帮助我们实现临时变量的储存。匿名类型还依赖于另外一个特性：支持根据property来创建对象。比如，var d = new { Name = "s" };编译器自动产生一个有property叫做Name的匿名类，然后按这个类型分配内存，并初始化对象。

    例如下例：new{p.First,p.City};First和City都是在映射文件中定义与列表中字段相对应的property。编译器读取数据并创建对象时，会创建一个匿名类，这个类有两个属性，为First和City，然后根据数据初始化对象。另外编译器还可以重命名property的名字。

   

   使用 SELECT 和匿名类型返回仅含学生名字和所在城市的序列

   ```csharp
   var q =
       from p in students
       select new 
   	{
       	p.First,
       	p.City
   	};
   ```

   

3. 条件形式

   使用条件语句返回学生名字和成绩状态

   ```csharp
   var q = 
       from p in students
       select new
   	{
       	p.First,
       	Availability =
           p.Scores.Any(p => p <60) ?
           "挂科" : "没挂科"
   	};
   ```

   

4. 指定类型形式

   使用SELECT和已知类型返回学生姓名的序列

   ```csharp
   var q =
       from p in students
       select new Name
       {
           FirstName = p.First,
           LastName = p.Last
       };
   ```

   

5. 筛选形式

   说明：结合where使用起到过滤作用

   返回仅含Redmond学生名字的序列

   ```csharp
   var q = 
       from p in students
       where p.City == "Redmond"
       select c.First;
   ```

   

6. 整形类型

   说明：其select操作使用了匿名对象，而该匿名对象中，其属性也是个匿名对象。

   使用SELECT 和匿名类型返回有关学生的数据的整形子集。查询学生ID，姓名(名字，姓氏)，居住地（城市，街道）

   ```csharp
   var q =
       from p in students
       select new {
           p.ID,
           NameInfo = new {p.First, p.Last},
           AddressInfo = new {p.City, p.Street},
       	p.Scores
   	};
   ```

   

7. 嵌套类型形式

   说明：返回对象的属性又包含一个集合

   使用嵌套查询返回所有学生ID及其及格的成绩序列

   ```csharp
   var q =
       from p in students
       select new {
           p.ID,
           scores =
               from h in p.Scores
               where h >= 60
               select h
              };
   ```

   

8. 本地方法调用

   在查询中调用本地方法PhoneNumberConverter将电话号码转换为国际格式

   ```csharp
    var q = 
        from p in students
        where p.Country == "UK" || p.Country == "USA"
        select new
        {
            p.ID,
            p.First,
            Phone = p.Phone,
            InternationalPhone = 
            PhoneNumberConverter(p.Country, p.Phone)
        };
   ```

   PhoneNumberConverter方法如下：

   ```csharp
   public string PhoneNumberConverter(string Country, string Phone)
   {
       switch (Country)
       {
           case "USA":
               return "1-" + Phone;
           case "UK":
               return "44-" + Phone;
           default:
               return Phone;
       }
   }
   ```

   

9. Distinct形式

   说明：筛选字段中不同的值

   查询学生覆盖的城市

   ```csharp
   var q = 
       (
           from p in students
           select p.City
   	).Distinct();
   ```



### Join

适用场景：用来实现表之间的关联操作

1. 内关联

   查询有选课学生的选课信息

   ```csharp
   var q =
       from r1 in a.AsEnumerable()//需要将datatable转换成Enumerable类型才能被Linq识别
       join r2 in b.AsEnumerable()
       on r1["sid"] equals r2["sid"]
       select new
       {
           studentName = r1["sname"],
           cID  = r1["cid"]
       };
   ```

2. 左关联

   查询所有学生的选课情况

   ```csharp
   var q =
        from r1 in a.AsEnumerable()//需要将datatable转换成Enumerable类型才能被Linq识别
        join r2 in b.AsEnumerable()
        on r1["sid"] equals r2["sid"]
        into tmp
        from tt in tmp.DefaultIfEmpty()
        select new
        {
            studentName = r1["sname"],
            cID = tt == null ? "" : tt["cid"] //外联集合可能为null，所以需要判断
        };
   ```

   

### Order By

​	适用场景：对查询出的语句进行排序，比如按时间排序等等。

​    说明按指定表达式对集合进行排序，默认为升序，加上descending为降序，对应的扩展方法为OrderBy和OrderByDescending

1. 简单形式

   按年龄对学生进行排序

   ```csharp
   var q =
       from r in a.AsEnumerable()
       orderby r["sage"]
       select r;

2. 带条件形式

   按年龄对男同学进行排序

   ```csharp
   var q =
       from r in a.AsEnumerable()
       where Convert.ToString(r["ssex"]) == "男"
       orderby r["sage"]
       select r;
   ```

3. 降序排序

   对学生按年龄进行降序排序

   ```csharp
   var q =
       from r in a.AsEnumerable()
       orderby r["sage"] descending
       select r;
   ```

4. ThenBy

   说明：使用复合的orderby进行排序

   ```csharp
   var q =
       from r in a.AsEnumerable()
       orderby r["sage"],r["ssex"]
       select r;
   ```

   ```csharp
   var q = a.AsEnumerable()
       	.OrderBy(r => r["sage"])
       	.ThenBy(r => r["sex"]);
   ```

   ==注意==：OrderBy不支持byte类型

   

### Group By

​	适用场景：分组数据，为查找数据缩小范围

​	说明：分配并返回对传入参数进行分组操作后的可枚举对象

1. 简单形式

   将学生按男女进行分类

   ```csharp
   var q =
       from r in a.AsEnumerable()
       group r by r["ssex"] into g
       select g;
   ```

   想要遍历所有信息需进行如下操作

   ```c#
   foreach (var p in q)
   {
       foreach (var h in p)
       {
           Console.WriteLine(h["sname"]+" "+h["ssex"]);
       }
   }
   ```

   若想遍历某个类别中所有记录需进行如下操作

   ```csharp
   foreach (var p in q)
   {
       if(Convert.ToString(p.Key) == "男")
       {
            foreach (var h in p)
            {
                Console.WriteLine(h["sname"]+" "+h["ssex"]);
            }
       }
   }
   ```

2. where限制

   找出选课人数大于3人的课程ID

   ```csharp
   var q =
       from r in a.AsEnumerable()
       group r by r["cid"] into g
       where g.Count() > 3
       select new
   	{
       	CID = g.Key
   	};
   ```

3. 关联形式

   查询男女课程1的平均成绩

   ```csharp
   //找出选修了课程编号为1的学生性别与成绩
   //并转换成List
   var q =
       (from r1 in a.AsEnumerable()
       join r2 in b.AsEnumerable()
       on r1["sid"] equals r2["sid"]
       where Convert.ToInt32(r2["cid"]) == 1
       select new { 
           Sex = r1["ssex"],
           Score = r2["score"]
       }).ToList();
   
   //按学生性别计算平均成绩
   var p = 
       from h in q
       group h by h.Sex into g
       select new
       {
           Key = g.Key,
           Avg = g.Average(p => Convert.ToDecimal(p.Score))
       };
   ```

   ```csharp
   var q = from k in
         ((from r1 in a.AsEnumerable()
           join r2 in b.AsEnumerable()
           on r1["sid"] equals r2["sid"]
           where Convert.ToString(r2["cid"]) == "01"
           select new
           {
               sex = r1["ssex"],
               score = r2["score"]
           }).AsEnumerable().GroupBy(p => p.sex))
           select new
           {
               Key = k.Key,
               Avg = k.Average(g => Convert.ToDouble(g.score)),
           };
   ```

   

4. 表达式形式

   按是否及格筛选选修“01”课程的学生成绩

   ```csharp
   var q =
       from r in a.AsEnumerable()
       where Convert.ToString(r["cid"]) == "01"
       group r by new {score = Convert.ToInt32(r["score"]) > 60} into g
       select g;
   ```

   

### Any/All

#### Any

​		说明：用于判断集合中是否有元素满足某一条件。（若条件为空，则集合只要不为空就返回True，否则为False）

1. 简单形式

   查询出有成绩的学生信息

   ```csharp
   var q =
       from p in students
       where p.Scores.Any()
       select p;
   ```

2. 带条件形式

   查询出成绩中有不及格成绩的学生信息

   ```csharp
   var q =
       from p in students
       where p.Scores.Any(p => p < 60)
       select p;
   ```

   

#### All

​		说明：用于判断集合中所有元素是否都满足某一条件

1. 带条件形式

   查询所有成绩均及格的学生信息

   ```csharp
   var q =
       from p in students
       where p.Scores.All(p => p >= 60)
       select p;
   ```

   

### Concat/Union/Intersect/Except

#### Concat(连接)

​		适用场景：对两个集合的处理，例如追加、合并、取相同项、相交项等等

​		说明：连接不同的集合，不会自动过滤相同项

1. 简单形式

   取所有学生和老师的姓氏

   ```csharp
   var q =
       (
       	from p in students
       	select p.Las
   	)
       .Concat(
        	from p in teachers
        	select p.Last
   	);
   ```

2. 复合形式

   取得所有学生老师的姓名以及居住的城市

   ```csharp
   var q =
       (from p in students
        select new
        {
         	Name  = p.First+" "+  p.Last,
         	City  = p.City 
        })
       .Concat(
        from p in teachers
        select new
        {
         	Name = p.First + " " + p.Last,
         	City = p.City
        });
   ```

   

#### Union(合并)

​		说明：连接不同的集合，自动过滤相同项

1. 取得老师和学生居住的城市

   ```csharp
   var q =
       (
       	from p in students
        	select p.City
   	).Union
       (
       	from p in teachers
        	select p.City
   	);
   ```

   

#### Intersect(相交)

​		说明：获取不同集合的相同项（交集）

1. 查询老师和学生同在的城市

   ```csharp
   var q =
       (
       	from p in students
           select p.City
   	).Intersect
       (
       	from p in teachers
        	select p.City
   	);
   ```

   

#### Except(与非)

​		说明：排除相交项

1. 查询老师和学生不同在的城市

   ```csharp
   var q =
       (
           from p in students
           select p.City
       ).Except
       (
           from p in teachers
           select p.City
       );
   ```



### Take/Skip/TakeWhile/SkipWhile/Like

#### Take

说明：获取集合的前n个元素，只返回限定数量的结果集

1. 获取有一科成绩超过90的前两位学生

   ```csharp
   var q =
       (
       	from p in students
       	where p.Scores.Any(p => p>90
       ) 
       select new
       {
           Name = p.First + " " + p.Last
       }).Take(2);
   ```

   

#### Skip

说明：跳过集合的前n个元素,即跳过给定的数目返回后面的结果集

1. 获取课程id为01的第三名之后的成绩情况

   ```csharp
   var q =
       (
           from p in a.AsEnumerable()
           where Convert.ToString(p["cid"]) == "01"
           orderby p["score"] descending
           select p["score"]
       ).Skip(3);
   ```

   

#### TakeWhile

​		说明：直到某一条件成立就停止获取。即用其条件去依次判断源序列中的元素，返回符合判断条件的元素，该判断操作将在返回false或源序列的末尾结束 。

1. 

   ```csharp
   var q = students.TakeWhile(p => p.Scores.All(p => p>=60));
   ```

   

#### SkipWhile

​		说明：直到某一条件成立就停止跳过。即用其条件去判断源序列中的元素并且跳过第一个符合判断条件的元素，一旦判断返回false，接下来将不再进行判断并返回剩下的所有元素

 1. 

    ```csharp
    var q = students.SkipWhile(p => p.Scores.All(p => p > 60));
    ```

    

### Count/Sum/Min/Max/Avg

适用场景：统计数据，比如求一些数据的个数，求和，最小值，最大值，平均数。

#### Count

说明：返回集合中的元素个数

1. 简单形式

   取得学生数量

   ```csharp
   var q = students.Count();
   ```

2. 带条件形式

   取得来自USA的学生总数

   ```csharp
   var q = students.Count(p => p.Country == "USA");
   ```



#### Sum

说明：返回集合中数值类型的元素之和

1. 取得学生id为01的学生所有学科总成绩

   ```csharp
   var q = (from p in a.AsEnumerable()
            where Convert.ToString(p["sid"]) == "01" 
            select Convert.ToDouble(p["score"])).Sum();
   ```

   


#### Min

说明：返回集合中的最小值

1. 查找每个每科中最低的成绩

   ```csharp
   var q = from p in a.AsEnumerable()
           group p by p["cid"] into g
           select new
           {
               CourseID = g.Key,
               MinScore = g.Min(p => Convert.ToDecimal(p["score"]))             };
   ```

   

#### Max

说明：返回集合中的最大值

1. 查找出每科中的最高成绩

   ```csharp
   var q = from p in a.AsEnumerable()
           group p by p["cid"] into g
           select new
           {
               CourseID = g.Key,
               MinScore = g.Max(p => Convert.ToDecimal(p["score"]))
           };
   ```

   

说明：返回集合中数值类型元素的平均值

1. 查找各科成绩的平均值

   ```csharp
   var q = from p in a.AsEnumerable()
           group p by p["cid"] into g
           select new
           {
               CourseID = g.Key,
               MinScore = g.Average(p => Convert.ToDecimal(p["score"]))
           };
   ```

### 嵌套使用函数

1. 查找01课程比02课程成绩高的学生信息及课程分数

   ```csharp
    var k = from z in b.AsEnumerable()
            join g in
           ((from p in a.AsEnumerable()
             group p by p["sid"] into o
             select new
             {
                 sid = o.Key,
                 score1 = (from k in o
                           where Convert.ToString(k["cid"]) == "01"
                           select k["score"]).Any() ? (from k in o
                                                       where Convert.ToString(k["cid"]) == "01"
                                                       select k["score"]).First() : null,
                 score2 = (from k in o
                           where Convert.ToString(k["cid"]) == "02"
                           select k["score"]).Any() ? (from k in o
                                                       where Convert.ToString(k["cid"]) == "02"
                                                       select k["score"]).First() : null,
             }).AsEnumerable().Where(obj => obj.score1 != null
                                 && obj.score2 != null
                                 && Convert.ToDouble(obj.score1) > Convert.ToDouble(obj.score2))
                            .Select(obj => new
                            {
                                sid = obj.sid,
                                score1 = Convert.ToDouble(obj.score1),
                                score2 = Convert.ToDouble(obj.score2)
                            })) on z["sid"] equals g.sid
            select new
            {
                g.sid,
                sname = z["sname"],
                g.score1,
                g.score2
            };
   ```

   
