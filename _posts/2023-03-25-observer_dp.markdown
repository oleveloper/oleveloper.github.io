---
layout: post
title: "Observer Pattern"
date: 2023-03-25
last_modified_at: 2023-03-25
categories: [Design Pattern]
tags: [Design Pattern, Observer Pattern]
---
### 사용 이유
객체의 상태가 바뀌면 그 객체에 의존하는 다른 객체에게 연락이 가고, 자동으로 내용이 갱신되게끔 하기 위해서이다.
one-to-many 형식을 가진다.

```java
public class WeatherData {
  public void measurementsChanged() {
    float temp = getTemperature();
    float humidity = getHumidity();
    float pressure = getPressure();
    
    currentConditionsDisplay.update(temp, humidity, pressure);
    statisticsDisplay.update(temp, humidity, pressure);
    forecastDisplay.update(temp, humidity, pressure);
  }
}
```

### 옵저버 패턴 구현하기
#### 기상 스테이션 구현
```java
public interface Subject {
  public void registerObserver(Observer o);
  public void removeObserver(Observer o);
  public void notifyObservers();
}

public interface Observer {
  public void update(float temp, float humidity, float pressure);
}

public interface DisplayElement {
  public void display();
}
```

#### Subject Interface
```java
public class WeatherData implements Subject {
  private List<Observer> observers;
  private float temperature;
  private float humidity;
  private float pressure;
  
  public WeatherData() {
    observers = new ArrayList<Observer>();
  }
  
  public void registerObserver(Observer o) {
    observers.add(o);
  }
  
  public void removeObserver(Observer o) {
    observers.remove(o);
  }
  
  public void notifyObservers() {
    for (Observer observer : observers) {
      observer.update(temperature, humidity, pressure);
    }
  }
  
  public void measurementsChanged() {
    notifyObservers();
  }
  
  public void setMeasurements(float temperature, float humidity, float pressure) {
    this.temperature = temperature;
    this.humidity = humidity;
    this.pressure = pressure;
    measurementsChanged();
  }
}
```

#### 디스플레이 요소 구현
```java
public class CurrentConditionsDisplay implements Observer, DisplayElement {
  private float temperature;
  private float humidity;
  private WeatherData weatherData;
  
  public CurrnetConditionsDisplay(WeatherData weatherData) {
    this.weatherData = weatherData;
    weatherData.registerObserver(this);
  }
  
  public void update(float temperature, float humidity, float pressure) {
    this.temperature = temperature;
    this.humidity = humidity;
    display();
  }
  
  public void display() {
    System.out.println("temperature: " + temperature);
    System.out.println("humidity: " + humidity);
  }
}
```
