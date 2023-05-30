## 1. list 遍历前判空

语义：list如果不为空，stream流式处理，为空直接返回一个空list

```java
ArrayList <TestDTO> list = new List<>();
Optional.ofNullable(list).orElse(Collections.emptyList()).stream().map().collect(Collectors.toList());
```

## 

## 2. stream()可以接的方法(前面默认加了上面的判空)

### 1 ) filter()

语义 ：作为list的过滤条件用,filter可以放在前面也可以放在后面，代表过滤filter()之前的list(最重要的是一定要用一个新list去接收返回结果)

```java
List<DTO> Blist = Alist.stream().filter(d - > d.getId() != null || d.getInfo().getAge() > 0).collect(Collectors.toList());
```

### 2 ）map() 

语义：主要是进行数据重组，例如只需要一个dto里面的某些字段，一般后面配合.collect(Collectors.*toList*());

使用。

```java
stream().map(TestDTO::getName).collect(Collectors.toList());
//返回的是一个List<String>

stream().map(item -> item.getName()).collect(Collectors.toList());
//和上面的作用一样

stream().map(
	item ->{
    	idAndNameDTO r = new idAndNameDTO();
        r.setId(item.getId());
        r.setName(item.getName());
        return r; //这里return什么类型，返回的list就是什么类型
    }
).collect(Collectors.toList());
//返回List<idAndNameDTO>
```

### 3 ） collect()



#### 3.1 (map<Integer,String>)这里展示拿去TestDTO一级属性和二级属性的两个值组成一个map,并且去重(不去重key重复报错)

```java
stream().collect(Collectors.toMap(TestDTO::getId,it -> it.getInfoDTO().getName(),(v1,v2) -> v2));
{
	1 : "小明",
    2 : "小红"
}
```

#### 3.2 (List<Integer>)取DTO某些内容重新组一个list,这里返回list<int>,效果和上面的map()一样

```java
stream().collect(Collectors.mapping(TestDTO::getId, Collectors.toList()))	;
[18,22,25]
```

#### 3.3 (map<Integer,List<TestDTO>>)按某个字段分组

```java
stream().collect(Collectors.groupingBy(TestDTO::getAge));
{
  18 : [
      {DTO},
      {DTO}
  ],
  21 : [
      {DTO}
  ]
}
```

#### 3.4 (map<String,List<TestDTO>>) 按条件分组

```java
stream().collect(Collectors.groupingBy(item -> {
    if (item.getAge() > 20) {
        return "大人";
    } else {
        return "小孩";
    }
}));
{
  "大人" : [
      {DTO},
      {DTO}
  ],
  "小孩" : [
      {DTO}
  ]
}
```

#### 3.5 (map<Integer,map<String,List<TestDTO>>)分组再分组(先按年龄分组，再按男女分组)

```java
list.stream().collect(Collectors.groupingBy(TestDTO::getAge, Collectors.groupingBy(item -> {
    if (item.getSex() == 0) {
        return "man";
    } else {
        return "women";
    }
})));
{
	15:{
    	"man" : [
            {DTO},
            {DTO}
        ],
        "women" : [
            {DTO}
        ]
    },
    22:{...}
}
```

#### 3.6 (map<Integer,List<String>>)3.3升级，分组完再提取某些字段

```java
stream().collect(Collectors.groupingBy(TestDTO::getAge,Collectors.mapping(TestDTO::getName, Collectors.toList())));
{
    18 : [
    	"小李",
        "小红"
    ],
    21 : [
    	"小明"
    ]
}
```



#### 3.7 (map<Integer,Ingeger>)求某个字段出现的次数

```java
stream().collect(Collectors.groupingBy(TestDTO::getAge,Collectors.counting));

{
	18 : 21,
    19 : 3,
    20 : 4
}
```



#### 3.8 (map<String,Integer>) 求List<DTO>某个字段“值”的和

```java
stream().collect(Collectors.groupingBy(TestDTO::getType,Collectors.summingLong(TestDTO::getCount)));

{
	"类型A" : 180,
    "类型B" : 111
}
```