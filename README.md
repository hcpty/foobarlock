# Readme
Locks that can be used to solve Compound Readers-Writers Problem in database-based apps.

几乎所有database都实现了record-level lock，解决了record-level的Readers-Writers Problem。

考虑并发地读写一个shared resource。当这个shared resource只对应一个record时，record-level lock可以完全保护这个shared resource。但是当一个shared resource对应多个record时，每个record-level lock都只能保护其对应的record，因此，写者和写者之间对这个shared resource的访问不是完全互斥的，写者和读者之间对这个shared resource的访问也不是完全互斥的。

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

Foobar是一种惯用语，在此处指代一组相关的record。foobarlock代表一组record-level lock和一把shared-resource-level lock，分别施加在每个record上以及这个shared resource上，foobarlock因此得名。该方案要求app使用的database必须实现了record-level lock。

由于允许读者读写者正在写的shared resource，所以有时候读者读出来的数据可能会存在逻辑上的歧义，这种歧义可以通过逻辑检查和重试机制进行消除。因此，该方案仅适用于读的频次远高于写的频次的场景。

Foobarlock旨在替代数据库事务，因为数据库事务是一种“黑魔法”，而且数据库事务并非到处可用。

### Credits
- [Readers–writers problem - Wikipedia](https://www.wikipedia.org/wiki/Readers-writers_problem)
