# Readme
A lock that can be used to solve Readers-Writers Problem in database-based apps.

几乎所有database都实现了record-level lock，从而解决了record-level的Readers-Writers Problem，但是这种record-level lock经常只供database内部使用，不向用户提供。

当一个shared resource只包含一个record时，这个record-level lock可以完全保护这个shared resource。但是当一个shared resource包含多个record时，每个record-level lock都只能保护其对应的record，因此，写者和写者之间对shared resource的访问不是完全互斥的，写者和读者之间对shared resource的访问也不是完全互斥的。

一个解决方案是在app中使用一把互斥锁foobarlock来控制写者们对这个shared resource的访问而对读者们不施加额外的约束。如下：

```c
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

该方案的缺点是读者有可能会读出写者正在写的shared resource，该缺点在特定情况下会导致app发生逻辑上的错误，但是可以通过重试等机制进行避免。可见该方案只适用于读的频次远高于写的频次的场景。

该方案要求所使用的database必须实现了record-level lock，但是不要求database向用户提供显式的record-level lock接口。

Foobarlock旨在替代数据库事务，数据库事务是一种“黑魔法”，而且数据库事务并非随处可用。

### Credits
- [Readers–writers problem - Wikipedia](https://www.wikipedia.org/wiki/Readers-writers_problem)
- [Database transaction - Wikipedia](https://www.wikipedia.org/wiki/Database_transaction)
