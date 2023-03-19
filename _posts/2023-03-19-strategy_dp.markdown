---
layout: post
title: "Strategy Pattern"
date: 2023-03-19
last_modified_at: 2023-03-19
categories: [Design Pattern]
tags: [Design Pattern, Strategy Pattern]
---

### 사용 이유
런타임에 알고리즘 동작을 선택할 수 있도록 한다.
Strategy를 바꾸는 것만으로 행위를 유연하게 수정할 수 있도록 한다.

```java
public class Duck {
  public void quack() {
    System.out.println("Quack!");
  }
  
  public void swim() {
    System.out.println("I am Swimming");
  }
  
  public void display() {
    System.out.println("I am Duck");
  }
}
```

```java
public class MallardDuck extends Duck
{
    public void display () {
        System.out.println("I am Mallard Duck");
    }
}
```
```java
public class RedheadDuck extends Duck
{
    public void display () {
        System.out.println("I am RedheadDuck Duck");
    }
}
```

위와 같은 코드가 있다고 하자. 
이 코드에서 모든 오리가 날 수 있도록 Duck Class에 fly() 라는 method를 추가했다. 
```java
public class Duck {
  public void quack() {
    System.out.println("Quack!");
  }
  
  public void swim() {
    System.out.println("I am Swimming");
  }
  
  public void display() {
    System.out.println("I am Duck");
  }
  
  public void fly() {
    System.out.println("I am Flying!");
  }
}
```

그런데 몇몇 서브클래스만 날아야했기 때문에, 날지 못하는 Duck도 날 수 있게 되었다. 
그래서 날지 못하는 Duck 들에 대해 아래와 같이 method overriding 했다.
```java
public class DecoyDuck extends Duck
{
  public void quack() {
    // Do nothing
  }

  public void display () {
      System.out.println("Decoy Duck");
  }
    
  public void fly() {
    // Do nothing
  }
}
```
위와 같은 상황일 때 fly(), quack() method를 일일이 규정하고 override 해야 한다.
또한 fly(), quack()의 동작이 조금 바뀐다면 상위 클래스를 상속 받은 서브 클래스에서 모든 코드를 고쳐야 한다. 

### 디자인 원칙 1
애플리케이션에서 달라지는 부분과 달라지지 않는 부분을 분리한다.
달라지는 부분은 Encapsulate 한다. 
이렇게 한다면, 코드를 변경하는 과정에서 Side effect를 줄이고 시스템의 유연성을 향상시킬 수 있다. 

### 디자인 원칙 1에 따른 변경
fly(), quack() method가 변경되는 부분이므로 Duck class에서 이 method를 분리하여 새로운 클래스 집합을 만든다.

### 디자인 원칙 2
인터페이스에 맞추어 프로그래밍 한다.

### 디자인 원칙 2에 따른 변경
아래와 같이 각 행동을 인터페이스로 표현하고, 인터페이스를 사용해서 구현하도록 한다.
fly(), quack()을 Duck class에서 구현하지 않고 특정 행동들을 위한 클래스(아래에서 FlyBehavior, QuackBehavior)를 만든다.

### FlyBehavior
```java
public interface FlyBehavior {
  public void fly();
}
```
```java
public class FlyWithWings implements FlyBehavior {
  public void fly() {
    System.out.println("I can fly");
  }
}
```
```java
public class FlyNoWay implements FlyBehavior {
  public void fly() {
    System.out.println("I can not fly");
  }
}
```

### QuackBehavior
```java
public interface QuackBehavior {
  public void quack();
}
```
```java
public class Quack implements QuackBehavior {
  public void fly() {
    System.out.println("Quack!");
  }
}
```
```java
public class MuteQuack implements QuackBehavior {
  public void fly() {
    System.out.println("I can not quack!");
  }
}
```

### Duck
Quack, Fly 동작을 위임한 Duck Abstract Class는 아래와 같이 변경된다.
```java
public abstract class Duck {
  FlyBehavior flyBehavior;
  QuackBehavior quckBehavior;
  
  public abstract void display();
  
  public void performFly() {
    this.flyBehavior.fly(); // Fly 라는 행동을 직접 처리하지 않고 flyBehavior로 참조되는 객체에 행동을 위임한다.
  }
  public void performQuack() {
    this.quackBehavior.quack();
  }
  public void swim() {
  
  }
}
```

### 예: MallardDuck
MallardDuck instance가 생성될 때 Duck으로부터 받은 quackBehavior instance에 Quack instance를 대입하고,
flyBehavior instance에는 FlyWithWings instance를 대입한다. 
```java
public class MallardDuck extends Duck {
  public MallardDuck() {
    flyBehavior = new FlyWithWings();
    quackBehavior = new Quack();
  }
  
  @Override
  public void display() {
    System.out.println("I am Mallard duck");
  }
}
```

