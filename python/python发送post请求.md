# Python 使用 requests 发送POST请求

1.以 form 表单形式发送 post 请求

将请求的参数构造成一个字典，然后传给 requests.post() 的 data 即可。
```python
url = 'http://httpbin.org/post'
d = {'key1': 'value1', 'key2': 'value2'}
rsp = requests.post(url, data=d)
print rsp.text
```

2.以 json 形式发送 post 请求

将 json 串传给 requests.post() 的 data 参数
```python
url = 'http://httpbin.org/post'
d = {'key1': 'value1', 'key2': 'value2'}
s = json.dumps(d)
rsp = requests.post(url, data=s)
print rsp.text
```

3.以 multipart 形式发送 post 请求
```python
url = 'http://httpbin.org/post'
files = {'file': open('report.txt', 'rb')}
rsp = requests.post(url, files=files)
print rsp.text
```

参考的详细教程：https://blog.csdn.net/junli_chen/article/details/53670887

