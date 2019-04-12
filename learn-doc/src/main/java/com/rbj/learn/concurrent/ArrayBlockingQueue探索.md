#ArrayBlockingQueue
#### **一、原理**
>* ArrayBlockingQueue实现了BlockingQueue
>* 数据是保存在一个Object类型的数组中
>* 创建时可以通过参数指定数组的初始化大小，以及是否适用公平锁，默认适用非公平锁

```java
public class ArrayBlockingQueue<E> extends AbstractQueue<E>
        implements BlockingQueue<E>, java.io.Serializable {

  /** The queued items */
  final Object[] items;

  /** items index for next take, poll, peek or remove */
  int takeIndex;

  /** items index for next put, offer, or add */
  int putIndex;

  /** Number of elements in the queue */
  int count;

  /** Main lock guarding all access */
  final ReentrantLock lock;

  /** Condition for waiting takes */
  private final Condition notEmpty;

  /** Condition for waiting puts */
  private final Condition notFull;
  
  /**
   * Inserts the specified element at the tail of this queue, waiting
   * up to the specified wait time for space to become available if
   * the queue is full.
   *
   * @throws InterruptedException {@inheritDoc}
   * @throws NullPointerException {@inheritDoc}
   */
  public boolean offer(E e, long timeout, TimeUnit unit)
    throws InterruptedException {
    checkNotNull(e);
    long nanos = unit.toNanos(timeout);
    final ReentrantLock lock = this.lock;
    lock.lockInterruptibly();
    try {
        while (count == items.length) {
            if (nanos <= 0)
                return false;
            nanos = notFull.awaitNanos(nanos);
        }
        enqueue(e);
        return true;
    } finally {
        lock.unlock();
    }
  }
  
  private void enqueue(E x) {
    // assert lock.getHoldCount() == 1;
    // assert items[putIndex] == null;
    final Object[] items = this.items;
    items[putIndex] = x;
    if (++putIndex == items.length)
        putIndex = 0;
    count++;
    notEmpty.signal();
  }
  
  public E poll(long timeout, TimeUnit unit) throws InterruptedException {
    long nanos = unit.toNanos(timeout);
    final ReentrantLock lock = this.lock;
    lock.lockInterruptibly();
    try {
        while (count == 0) {
            if (nanos <= 0)
                return null;
            nanos = notEmpty.awaitNanos(nanos);
        }
        return dequeue();
    } finally {
        lock.unlock();
    }
  }
  
  /**
   * Extracts element at current take position, advances, and signals.
   * Call only when holding lock.
   */
  private E dequeue() {
    // assert lock.getHoldCount() == 1;
    // assert items[takeIndex] != null;
    final Object[] items = this.items;
    @SuppressWarnings("unchecked")
    E x = (E) items[takeIndex];
    items[takeIndex] = null;
    if (++takeIndex == items.length)
        takeIndex = 0;
    count--;
    if (itrs != null)
        itrs.elementDequeued();
    notFull.signal();
    return x;
  }
  
}
```



#### **二、优点**
#### **三、缺点**
#### **四、适用场景**