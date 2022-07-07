> 从头开始，任何pom.xml文件都是以<project>标签开始，代表项目的开始

```xml
<project xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns="http://maven.apache.org/POM/4.0.0"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
</project>
```



> modelVersion : 描述这个POM文件是遵从哪个版本的项目描述符，对于Maven2及Maven 3来说，它只能是4.0.0

```xml
<project xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns="http://maven.apache.org/POM/4.0.0"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
</project>
```

> packaging ：打包方式，有（pom / jar / war）三种方式，默认不写这个标签就是jar包；**pom一般是用在父级模块**，在maven install的时候该模块不会打成jar/war包；如果打包方式指定了pom,相应的改项目的依赖坐标会加上一个type标签，值为pom，代表这是一个pom类型的项目。

```xml
<project xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns="http://maven.apache.org/POM/4.0.0"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <packaging>pom</packaging>
</project>
```

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-dependencies</artifactId>
  <version>2.7.1</version>
  <type>pom</type>
</dependency>
```

> groupId、artifactId、version ：简称gav，依赖在仓库中定位的三元组，groupId一般填写公司或者组织名，artifactId填写项目名，version则是这个项目的版本。当然还有一个name标签：只是一个名称，项目的全名称，可以是大写空格多个词，比如 Spring Boot Starter Parent，而 artifactId 是用来区分同一个groupId 下的子项目，比如前面对应的是 spring-boot-starter-parent。Ps：但是一般是实际使用中，我们可以把name的值赋成和artifactId一样的

```xml
<project xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns="http://maven.apache.org/POM/4.0.0"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <packaging>pom</packaging>
  
  <groupId>cn.test</groupId>
  <artifactId>test-demo</artifactId>
  <name>test-demo</name>
  <version>0.0.1-SNAPSHOT</version>
  
</project>
```

> modules、module :这两个标签一般在父pom.xml中出现，module填写的是子模块的artifactId。用modules标签管理这些子模块，同时modeule在modules中的顺序代表了子模块的编译顺序，Maven引入依赖使用最短路径原则，因此尽量将更加底层的service放在更先的位置优先加载依赖较为合适。

```xml
<project xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns="http://maven.apache.org/POM/4.0.0"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <packaging>pom</packaging>
  
  <groupId>cn.test</groupId>
  <artifactId>test-demo</artifactId>
  <name>test-demo</name>
  <version>0.0.1-SNAPSHOT</version>
  
  <modules>
    <module>test-demo-dao</module>
    <module>test-demo-service</module>
    <module>test-demo-web</module>
  </modules>  
</project>
```

> dependencies、dependency : 这两个是搭配使用，dependency 标签里面填写的是要依赖的项目的gav，通过dependencies标签把这些依赖管理起来。

```xml
<project xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns="http://maven.apache.org/POM/4.0.0"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <packaging>pom</packaging>
  
  <groupId>cn.test</groupId>
  <artifactId>test-demo</artifactId>
  <name>test-demo</name>
  <version>0.0.1-SNAPSHOT</version>
  
  <modules>
    <module>test-demo-dao</module>
    <module>test-demo-service</module>
    <module>test-demo-web</module>
  </modules>
  
  <dependencies>
    <dependency>
      <groupId>com.alibaba</groupId>
      <artifactId>fastjson</artifactId>
      <version>1.2.75</version>
    </dependency>
  </dependencies>
</project>
```

> dependencyManagement : 顾名思义，依赖管理者，可作为dependencies标签的更上一级标签，在dependencyManagement里面的依赖都只是**申明了版本**，并没有实际导入进项目。有这样一个场景，同一个父项目下面的两个子项目都想用fastjson这个依赖，于是都在自己的pom.xml中依赖了，并且版本还不一样，这样就会导致依赖冲突。dependencyManagement就是解决这样的场景，在父项目中申明一个fastjson的gav，子项目只需要依赖groupId、artifactId就可以使用了。

```xml
<project xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns="http://maven.apache.org/POM/4.0.0"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <packaging>pom</packaging>
  <!--这是一个父项目-->
  <groupId>cn.test</groupId>
  <artifactId>test-demo</artifactId>
  <name>test-demo</name>
  <version>0.0.1-SNAPSHOT</version>
  
  <modules>
    <module>test-demo-dao</module>
    <module>test-demo-service</module>
    <module>test-demo-web</module>
  </modules>
  
  <dependencyManagement>
    <!--只是申明了fastjson的版本-->
    <dependencies>
      <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>fastjson</artifactId>
        <version>1.2.75</version>
      </dependency>
    </dependencies>
  </dependencyManagement>
</project>
```

```xml
<project xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns="http://maven.apache.org/POM/4.0.0"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  
	<!--通过parent标签继承父项目-->
  <parent>
    <groupId>cn.test</groupId>
    <artifactId>test-demo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <relativePath>../pom.xml</relativePath>
  </parent>
  
  <!--这是子项目-->
  <artifactId>test-demo-web</artifactId>
  <name>test-demo-web</name>
  
  <!--因为继承了父项目，同时父项目申明了fastjson的版本，子项目用只需要不带版本依赖一下就行-->
  <dependencies>
    <dependency>
      <groupId>com.alibaba</groupId>
      <artifactId>fastjson</artifactId>
    </dependency>
  </dependencies>
 </project> 
```

> parent、relativePath : maven和java一样是有继承机制，同样是单继承，一个子pom只能继承一个父pom。如上test-demo-web子模块所示，通过parent标签来继承父pom，同时父pom的依赖也会继承过来。relativePath是指定父pom.xml文件所在的地址。子模块默认继承父模块的groupId和version，如果一些子模块需要当中二方包发布的话，同时不想跟随父模块的版本，可以显示自定义version。

> properties ： 用来集中归类依赖的版本，方便查找和修改，如下所示自定义版本的别称，通过 **${版本号别称}** 来修饰，同时把版本号别称当做标签放入properties标签中管理。

```xml
<project xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns="http://maven.apache.org/POM/4.0.0"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <packaging>pom</packaging>
  <!--这是一个父项目-->
  <groupId>cn.test</groupId>
  <artifactId>test-demo</artifactId>
  <name>test-demo</name>
  <version>0.0.1-SNAPSHOT</version>
  
  <modules>
    <module>test-demo-dao</module>
    <module>test-demo-service</module>
    <module>test-demo-web</module>
  </modules>
  
  <properties>
  	<fastjson.version>1.2.75</fastjson.version>
  </properties>
  
  <dependencyManagement>
    <dependencies>
      <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>fastjson</artifactId>
        <version>${fastjson.version}</version>
      </dependency>
    </dependencies>
  </dependencyManagement>
</project>
```

> scope : 作用范围，代表当前依赖的使用范围，有compile、test、runntime、provided、system、import。默认依赖不打scope标签时，依赖的作用范围是compile。test一般像测试相关的依赖( junit 等)会用到。这里要着重说一下 import ，上面说了maven项目是单继承，那么如果我想多继承呢？还有就是比方说我想导入springboot相关的依赖，但是springboot相关的依赖有成百上千个，难道一个一个通过<dependency>来依赖进我们的pom文件？显然不现实，所以就有了<scope>import</scope>这个东西，如果当前这个要依赖的项目是pom的类型(<packaging>类型为pom),我们只需要给这个依赖加上<scope>import</scope>，那个这个项目的pom.xml文件里面<dependencyManagement>标签下所有的依赖都会被依赖进你的项目中，有点类似于继承了一个父项目，把父项目的所有依赖都继承过来。也就相当于解决了多继承的问题。**特别重要import只能用在dependencyManagement里面的依赖中**

```xml
<project xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns="http://maven.apache.org/POM/4.0.0"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <packaging>pom</packaging>
  <!--这是一个父项目-->
  <groupId>cn.test</groupId>
  <artifactId>test-demo</artifactId>
  <name>test-demo</name>
  <version>0.0.1-SNAPSHOT</version>

  <dependencyManagement>
    <dependencies>
      <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-dependencies</artifactId>
        <version>2.1.13.RELEASE</version>
        <type>pom</type>
        <!--只能用在dependencyManagement里面的依赖上，相当于把spring-boot-dependencies的pom.xml里面的dependencyManagement标签下的所有依赖全部依赖进这个项目中，省去手动一个一个依赖进来-->
        <scope>import</scope>
      </dependency>
    </dependencies>
  </dependencyManagement>
</project>
```

