## 캡슐화

```
Tell, Don't Ask

데이터를 물어보지 않고, 기능을 실행해 달라고 말하라

Law of Demeter

메서드에서 생성한 객체의 메서드만 호출
파라미터로 받은 객체의 메서드만 호출
필드로 참조하는 객체의 메서만 호출
```

## DI와 서비스 로케이터

### 서비스 로케이터

#### - 사용할 객체를 제공하는 책임을 갖는 객체
#### - 제공하는 객체가 변경될 경우 서비스 로케이터도 변경된다는 단점 존재

1. Worker가 제대로 동작하기 위해선 JobQueue와 Transcoder의 콘크리트 객체가 필요. 
2. Locator 구현으로 Worker 클래스에 콘크리트 객체 제공.
3. Locator의 초기화는 main에서 처리.

```java
public class Locator {
  private static Locator instance;
  public static Locator getInstance() {
      return instance;
  }
  
  public static void init(Locator locator) {
      this.instance = locator;
  }
  
  private JobQueue jobQueue;
  private Transcoder transcoder;
  public Locator(JobQueue jobQueue, Transcoder transcoder) {
      this.jobQueue = jobQueue;
      this.transcoder = transcoder;
  }
  
  public JobQueue = jobQueue() { return jobQueue; }
  public Transcoder = getTranscoder() { return transcoder; }
  
}

```
### DI(Dependency Injection)

#### - 서비스 로케이터보다는 외부에서 사용할 객체를 주입해주는 DI

콘크리트 객체를 직접 사용해서 객체를 생성할 경우 의존 역전 원칙을 위반하게 된다.
###### ➡️ 결과적으로는 확장 폐쇄 원칙 위반

```java

public class Worker {
    public void run() {
        JobQueue jobQueue = new FileJobQueue(); // DIP 위반
    }
}
```

다음 코드와 같이 Worker 클래스에 사용할 객체를 전달받을 수 있는 생성자를 추가하는 것으로 DI를 적용할 수 있게 된다. 

```java
public class Worker {
  private JobQueue jobQueue;
  private Transcoder transcoder;
  
  public Worker(JobQueue jobQueue, Transcoder transcoder) {
      this.jobQueue = jobQueue;
      this.transcoder = transcoder;
  }
}
```

## 상속보단 조립

### 상속을 통한 재사용의 단점
#### 1. 상위 클래스 변경의 어려움
#### 상속은 상위 클래스의 변경을 어렵게 만든다. 최상위 클래스의 변경은 모든 하위 클래스들의 변경을 뜻한다.
#### 2. 클래스의 불필요한 증가
#### 유사한 기능을 확장하는 과정에서 클래스의 개수가 불필요하게 증가할 수 있다.

```java
public class Storage { ... }
public class CompressedStorage extends{ ... }
public class CompressedEncryptedStorage extends CompressedStorage { ... }

CompressedEncryptedStorage storage = new CompressedEncryptedStorage();
```
위와 같은 코드에서 런타임에 CompressedEncryptedStorage 객체가 사용하는 알고리즘을 변경할 수 없다.

```java
public Storage {
  private Compressor compressor = new Compressor();
  public void setCompressor(Compressor compressor) {
    this.compressor = compressor;
  }
  public void save(FileData fileData) {
    byte[] compressedByte = compressor.compress(fileData.getInputStream());
    ...
  }
}
```
위와 같이 객체를 조립하는 방식으로 클래스를 작성하면 런타임에 Compressor 객체를 바꿀 수 있게된다.
따라서 상속 기반의 재사용에서 발생했었던 문제들을 해소할 수 있고 상위 클래스에 의존하지 않기 때문에 유연하게 구조를 수정할 수 있게되었다.



###### reference by 
```
개발자가 반드시 정복해야할 객체지향과 디자인패턴
```
