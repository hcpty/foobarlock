# Readme
A lock that can be used to solve Readers-Writers Problem in database-based apps.

几乎所有database都实现了record-level lock，从而解决了record-level的Readers-Writers Problem。但是这种record-level lock经常只供database内部使用，不向用户提供。

当一个shared resource只包含一个record时，这个record-level lock可以完全保护这个shared resource。但是当一个shared resource包含多个record时，每个record-level lock都只能保护其对应的record，因此，写者和写者之间对shared resource的访问不是完全互斥的，写者和读者之间对shared resource的访问也不是完全互斥的。

一个解决方案是在app中使用一把互斥锁foobarlock来控制写者们对这个shared resource的访问而对读者们不施加额外的约束。如下：

```
sem_t foobarlock;

void reader(void)
{
  while (1) {
    /* read records */
  }
}

void writer(void)
{
  while (1) {
    P(&foobarlock);
    /* read/write records */
    V(&foobarlock);
  }
}
```

### Credits
- [Readers–writers problem - Wikipedia](https://www.wikipedia.org/wiki/Readers-writers_problem)
