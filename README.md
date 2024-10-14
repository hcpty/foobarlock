# Readme
Locks that can be used to solve Compound Readers-Writers Problem in database-based apps.

几乎所有database都实现了record-level lock，解决了record-level的Readers-Writers Problem，但是record-level lock经常只在database内部使用，不向app提供接口。

当一个shared resource只包含一个record时，这个record-level lock可以完全保护这个shared resource。但是当一个shared resource包含多个record时，每个record-level lock都只能保护其对应的record，因此，写者和写者之间对shared resource的访问不是完全互斥的，写者和读者之间对shared resource的访问也不是完全互斥的。

一个解决方案是在app中使用一把互斥锁来控制写者们对这个shared resource的访问而对读者们不施加额外的约束。如下：

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

该方案要求app使用的database必须实现了record-level lock，但是不强制要求database向app提供显式的record-level lock接口。

该方案的缺点是读者可能会读出写者正在写的shared resource，在特定情况下会导致数据逻辑错误，但是这种错误可以通过逻辑检查、重试机制进行避免。该方案只适用于读的频次远高于写的频次的场景。

Foobarlock旨在替代数据库事务，因为数据库事务是一种“黑魔法”，而且数据库事务并非到处可用。

### Credits
- [Readers–writers problem - Wikipedia](https://www.wikipedia.org/wiki/Readers-writers_problem)
- [Database transaction - Wikipedia](https://www.wikipedia.org/wiki/Database_transaction)
