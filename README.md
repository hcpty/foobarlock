# Readme
Locks that can be used to solve Compound Readers-Writers Problem in database-based apps.

### Foobarlock

几乎所有的database都实现了record-level lock，解决了record-level的Readers-Writers Problem，record-level lock的实现一般是Readers-Writer Lock。

考虑并发地读写一个shared resource。当这个shared resource只对应一个record时，record-level lock可以完全保护这个shared resource。但是当一个shared resource对应多个record时，每个record-level lock都只能保护其对应的record，因此，写者和写者之间对这个shared resource的访问不是完全互斥的，写者和读者之间对这个shared resource的访问也不是完全互斥的。

一个解决方案是在app中使用一把互斥锁来控制写者们对这个shared resource的访问而对读者们不施加额外的约束。如下：

```c
sem_t foobarlock;

void reader(void)
{
  while (1) {
    /* read the records */
  }
}

void writer(void)
{
  while (1) {
    P(&foobarlock);
    /* create/read/update/delete the records */
    V(&foobarlock);
  }
}
```

Foobar是一种惯用语，在此处指代一组相关的record。Foobarlock代表一组隐含的record-level lock和一把显式的shared-resource-level lock，分别施加在每个record上以及这个shared resource上，foobarlock由此得名。

由于允许读者读写者正在写的shared resource，虽然每个record都有Readers-Writer Lock的保护，但是有时候读者读出来的shared resource从整体上来看可能会存在逻辑上的歧义，可以通过逻辑检查和重试等机制进行消除。

### Credits
- [Readers–writers problem - Wikipedia](https://en.wikipedia.org/wiki/Readers-writers_problem)
