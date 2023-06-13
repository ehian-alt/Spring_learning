## Ioc 控制反转

使用对象时，由主动new产生对象转换为由外部提供对象，此过程中对象创建控制权由程序转移到外部，此思想称为控制反转
Spring技术对IoC思想进行了实现
* Spring提供了一个容器，称为IoC容器，用来充当IoC思想中的**外部**
* IoC容器负责对象的创建、初始化等一系列工作，被创建或被管理的对象在IoC容器中统称为`Bean`
* Ioc创建bean是通过类的无参构造方法创建的

## DI 依赖注入
在容器中建立bean与bean之间的依赖关系的整个过程，称为依赖注入，即当用到一个bean，这个bean跟另一个bean有关系，那么把这两个bean绑定

![image](https://github.com/ehian-alt/Spring_learning/assets/79576798/0ef2a10b-f771-43e8-a12f-e045dc58a51a)


## AOP

![image](https://github.com/ehian-alt/Spring_learning/assets/79576798/27ec02cf-e253-4416-9290-2babc92e65c5)
