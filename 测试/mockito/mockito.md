```java
@Rule
public PowerMockRule rule PowerMockRule();
```

可解决jacoco和@PrepareFortest导致覆盖率为0的问题，但模拟静态方法仍有点问题



模拟final类可使用mockito-inline：3.3.3,但不能模拟包装类和String

