# Python利用ThreadPoolExecutor实现对多线程的超时自动终止

```python
from concurrent.futures import ThreadPoolExecutor, TimeoutError
import time


def return_future_result(message):
    time.sleep(2)
    return message


pool = ThreadPoolExecutor(max_workers=2)  # 创建一个最大可容纳2个task的线程池
future1 = pool.submit(return_future_result, "hello")  # 往线程池里面加入一个task
future2 = pool.submit(return_future_result, "world")  # 往线程池里面加入一个task
print(future1.done())  # 判断task1是否结束
# time.sleep(3)
try:
    print(future1.result(timeout=1))  # 查看task1返回的结果
except TimeoutError as err:
    pool.shutdown()
    print(f'线程{future1}因超时已终止')
print(future1)
print(future2)
# print(future2.result())  # 查看task2返回的结果
print(future2.done())  # 判断task2是否结束
```