1. 对于接口的方法，实现这个接口的普通类，这些方法都得重写

2. 对于接口的方法，实现接口的抽象类，这些方法可以选择性重写

3. 如果普通类对于接口的方法有相同的实现，那这些方法可以在二者之间加一个抽象类去重写相同的实现，这样普通类再继承抽象类，接口方法相同的实现就不用写很多遍。

4. 泛型方法的使用

   ```java
   //1.泛型方法需要在返回值前面加一个<T>来声明这是一个泛型方法，然后不管返回值还是方法入参里面就可以使用这个T
   public <T extends BaseSchema> T getObject(Class<T> tClass){
     return tClass.newInstance();
   }
   //方法调用
   getObject(TestSchema.class);
   ```

5. 泛型接口与泛型类的使用

   ```java
   //泛型接口和泛型类都是需要在类名或者接口名后面加<T>来申明
   public interface IBase<T> {
     void test(lass<T> tClass);
   }
   
   public class TestService<T> implements IBase<T>{
     @Override
     public void test() {
       //重写
     }
   }
   
   TestService<TestSchema> test = new TestService<TestSchema>();
   ```

   

6. 接口在jdk1.8开始也可以用有方法的实现了,由default修饰，但是接口和抽象类都不能实例化，所以方法的使用可以用匿名内部类

   ```java
   public interface IBase<T extends BaseSchema> {
     		//接口方法的实现
   	    default T getObject(Class<T> tClass){
               return tClass.newInstance();
      		}	
   }
   //方法调用
   T t = new IBase<TestSchema>(){}.getObject(TestSchema.class);
   ```

   

7. 

8. 