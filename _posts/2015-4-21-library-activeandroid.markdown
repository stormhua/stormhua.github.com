---
layout: post
keywords: blog
description: blog
title: "Activeandroid的使用"
categories: [开源库]
tags: [activeandroid]
---
{% include codepiano/setup %}

## android orm框架之ActiveAndroid

是一个轻量级的Android ORM框架

[jar包下载](http://download.csdn.net/detail/guanjianwoshinidaye/8620735 "csdn下载地址")

## 工程配置

{% highlight java %}
 <meta-data
            android:name="AA_DB_NAME"
            android:value="Pickrand.db" />
 <meta-data
            android:name="AA_DB_VERSION"
            android:value="1" />
{% endhighlight %}

如果这两个都不写的话，默认会是Application.db，库版本号为1。

在Application中初始化 
可以继承com.activeandroid.app.Application，可以在自己的Application里初始化，看com.activeandroid.app.Application文件就明白了：

{% highlight java %}
public class Application extends android.app.Application {
    @Override
    public void onCreate() {
        super.onCreate();
        ActiveAndroid.initialize(this);
    }
    @Override
    public void onTerminate() {
        super.onTerminate();
        ActiveAndroid.dispose();
    }
}
{% endhighlight %}

所以如果不继承只需要在自己的Application 添加ActiveAndroid.initialize(this)和 
ActiveAndroid.dispose()的操作即可。

## 实际应用

### 创建Model类

{% highlight java %}
@Table(name = "Items")
public class Item extends Model {
    // 这样可以避免重复
    @Column(name = "remote_id", unique = true, onUniqueConflict = Column.ConflictAction.REPLACE)
    public int remoteId;
    @Column(name = "Name")
    public String name;
    @Column(name = "Category")
    public Category category;
    // 确保每个model类中都有一个默认的构造方法
    public Item(){
       super();
    }
    public Item(String remoteId, String name, Category category){
        super();
        this.remoteId = remoteId;
        this.name = name;
        this.category = category;
    }
}
{% endhighlight %}

这个Item类中包含了一个Category类型的对象

{% highlight java %}
@Table(name = "Categories")
public class Category extends Model {
    @Column(name = "remote_id", unique = true, onUniqueConflict = Column.ConflictAction.REPLACE)
    public int remoteId;
    @Column(name = "Name")
    public String name;
    @Column(name = "Entry")
    public Entry entry;
    public Category(){
       super();
    }
    public void saveEntry() {
        if (entry != null) {
            entry.save();
        }
    }
    public List<Item> data;
    // 使用外键，实现一对多存储
    public List<Item> items() {
        data = getMany(Item.class, "Category");
    }
}
{% endhighlight %}

这其中又包含了一个Entry对象

{% highlight java %}
@Table(name = "Entrys")
public class Entry extends Model {
    @Column(name = "Name")
    public String name;
}
{% endhighlight %}

具体的model类其实是根据实际情况来写的，这里只写最简单的。在数据库应用中，我们涉及的无外乎是增删改查，这里以一个简单的例子来研究一下。

实际项目中，我们经常会遇到这样的情况，一个类Category 中包含一个Entry对象，包含一个List<Item>对象，一个String对象。

模拟一下数据：

{% highlight java %}
List<Category> Categorys = new ArrayList<Category>();
 List<Item> Items = new ArrayList<Item>();
 for (int i = 0; i < 100; i++) {
       Category restaurants = new Category();
       restaurants.name = "Category " + i;
       Entry entry = new Entry();
       entry.name = "entry " + i;
       restaurants.entry = entry;
       Categorys.add(restaurants);
       for (int j = 0; j < 3; j++) {
           Item item = new Item();
           item.name = "Item " + i + " from " + "Category " + i;
           item.category = restaurants;
           Items.add(item);
       }
  }
{% endhighlight %}

在这个框架中，一个对象中包含一个List对象，其实就是把List对象全部存到另一个表里，通过外键来关联。

## 增

通过事务，批量添加进数据库。

{% highlight java %}
 ActiveAndroid.beginTransaction();
        try {
            int len = Categorys.size();
            for (int i = 0; i < len; i++) {
                Categorys.get(i).saveEntry();
                Categorys.get(i).save();
            }
            len = Items.size();
            for (int i = 0; i < len; i++) {
                Items.get(i).save();
            }
            ActiveAndroid.setTransactionSuccessful();
        } finally {
            ActiveAndroid.endTransaction();
        }
{% endhighlight %}

这里需要注意的是Item类中包含Category对象，Category类中包含Entry对象。必须先保存Entry对象，Entry对象会得到一个Id，然后才能存储Category对象，否则Category对象中的Entry对象就会为null。这部分逻辑可以查看Model类中save()方法的源码：

{% highlight java %}
else if (ReflectionUtils.isModel(fieldType)) {
    values.put(fieldName, ((Model) value).getId());
}
{% endhighlight %}

如果Entry对象不先存储，Id是为null的。Entry与Category关联的依赖就是Entry 
的Id。所以执行完Categorys.get(i).saveEntry()后才执行Categorys.get(i).save()其后才执行Item的存储，这时候Category对象都已经有了Id，Item会自动关联。

## 删

{% highlight java %}
Item item = Item.load(Item.class, 1);
item.delete();
// 或者
new Delete().from(Item.class).where("remote_id = ?", 1).execute();
{% endhighlight %}

批量删除就用事务。

## 改

先看下Model类源码152行：

{% highlight java %}
if (mId == null) {
    mId = db.insert(mTableInfo.getTableName(), null, values);
}else {
    db.update(mTableInfo.getTableName(), values, idName+"=" + mId, null);
}
{% endhighlight %}

很明显，如果mId不存在就会去数据库创建这跳数据，而如果数据存在的话，那就是更新了。所以，更改数据后直接执行save方法即可。

## 查

{% highlight java %}
public static List<Item> getAll(Category category，int num) {
       return new Select()
          .from(Item.class)
          .where("Category = ?", category.getId())
          .orderBy("Name ASC")
          .limit(num)
          .execute();
}
{% endhighlight %}

执行一个Select方法，指定表Item.class，限定条件where("Category = ?", category.getId())，排序为orderBy("Name ASC")，取出个数为limit(num) 
当然还支持其他非常多的指令

1. offset

1. as

1. desc/asc

1. inner/outer/cross join

1. group by

1. having 

这个就根据自己需求了。
在查询Category数据中，因为每个对象中包括了一个List<Item>对象，所以，我们需要在查询出Category对象后，在把关联的List表查询一遍。

{% highlight java %}
ActiveAndroid.beginTransaction();
try {
    List<Category> testCategory = new Select().from(Category.class).limit(20).execute();
    if (testCategory != null) {
        int len = testCategory.size();
        for (int i = 0; i < len; i++) {
            testCategory.get(i).items();
        }
    }
   ActiveAndroid.setTransactionSuccessful();
   } finally {
       ActiveAndroid.endTransaction();
   }
{% endhighlight %}

如此执行完，对象中的data就可以直接用了。

## 总结

作为一个轻量级的ORM框架，在快速开发中，应用起来还是很方便的，比ORMLite要简单一些，但某些细节不如ORMLite更强大，不如对象中包含对象时，ORMLite中可以有参数设定自动创建，而不是需要我们手动执行下sava，也许在ActiveAndroid中也可以这样，但目前我没搞明白注解中另几个属性的意思。整体性能经测试（100条Category数据，300条Item数据，100条Entry数据）存储耗时1.1s-1.2s，与ORMLite是持平的，但也许是测试数据太小了，没有说服性，有时间可以进行10w条数据的测试。

[转载自：](https://www.zybuluo.com/flyouting/note/6915)