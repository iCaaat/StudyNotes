# 基础框架SSM学习

## Spring

Spring Framework系统架构图

![image-20241207021325761](https://gitee.com/jackqueen/pictures/raw/master/202412070213965.png)

### 1. Ioc与DI

**IoC控制反转**：

- 使用对象时，由主动new产生对象转换为由**外部提供**对象，此过程中对象的创建控制权由程序转移到外部。

- Spring技术对IoC思想进行了实现，提供了一个容器，成为**IoC容器**，用来充当IoC思想中的外部。

- IoC容器管理对象的**创建和初始化过程**，比如service、dao。被创建或被管理的对象在IoC容器中统称为**Bean**。

**DI依赖注入**：

- 在容器中**建立bean与bean之间的依赖关系**的整个过程

### 2.