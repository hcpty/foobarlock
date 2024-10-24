# Readme
A solution that can be used to solve Compound Readers-Writers Problem in database-based apps.

### Foobarlock

几乎所有的database都实现了record-level lock，record-level lock的实现一般是Readers-Writer Lock。现在，考虑并发地读写一个shared resource：
- 当这个shared resource只对应一个record时，record-level lock可以完全保护这个shared resource，不存在问题。
- 当这个shared resource对应多个record时，每个record-level lock都只能保护其对应的record，因此导致了Compound Readers-Writers Problem：
  - 写者和写者之间对这个shared resource的访问不是完全互斥的，虽然有record-levle lock的保护，多个写者仍然可以破坏这个shared resource。
  - 读者和写者之间对这个shared resource的访问不是完全互斥的，当一个写者正在create/read/update/delete the records of the shared resource的时候，读者可以进入现场read the records of the shared resource，因此读者read出来的这个shared resource可能是不完整的。同时由于有record-level lock的保护，读者read出来的每一个record的内容都是完整的。

可以使用一个互斥锁来实现写者和写者之间对这个shared resource的互斥访问，而对于读者read出来的shared resource可能不完整的问题，可以通过在应用程序中进行逻辑判断并重试等机制进行解决。代码示例：
```c
sem_t foobarlock;

void reader(void)
{
  while (1) {
    /* read the records of the shared resource */
    /* may need to sovle incompletion problems */
  }
}

void writer(void)
{
  while (1) {
    P(&foobarlock);
    /* create/read/update/delete the records of the shared resource */
    V(&foobarlock);
  }
}
```

对于foobarlock解决方案的一些评价：
- Foobarlock只使用一个互斥锁，实现非常简单，但是作为代价，应用程序需要自行解决读者read出来的shared resource可能不完整的问题。
- Foobarlock的设计思想是读者不需要操作锁，只有写者需要操作锁。

### Credits
- Computer Systems: A Programmer's Perspective, Third Edition
- [Readers–writers problem - Wikipedia](https://en.wikipedia.org/wiki/Readers-writers_problem)
- [Readers–writer lock - Wikipedia](https://en.wikipedia.org/wiki/Readers–writer_lock)
