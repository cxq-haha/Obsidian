# 常用案例

直接使用

```shell
curl http://baidu.com
```

发送POST请求

```shell
curl -X POST http://example.com

curl -XPOST http://example.com

# 携带参数发送
curl -XPOST -d 'name=admin&shoesize=12' http://example.com/
# 发送JSON Body
curl -X POST -H "Content-Type: application/json"  -d '{"email":"web@myfreax.com","website":"myfreax.com"}' http://127.0.0.1:3000/site
# POST JSON上传文件
curl -i -X POST -H "Content-Type: multipart/mixed" -F "blob=@/Users/username/Documents/bio.jpg" -F "metadata={\"edipi\":123456789};type=application/json" http://localhost:8080/api/v1/user/
# 仅上传文件
# 使用 -F 选项指定文件时，需要在文件路径之前添加`@`符号
curl -X POST -F 'image=@/home/user/Pictures/wallpaper.jpg' http://example.com/upload

```

请求头操作

```shell
# 添加请求头
curl -H "Host: test.example" http://example.com/

# 获取请求头的所有首部
curl -I http://baidu.com
```

Cookie操作
```shell
# 使用-b添加一个cookie
curl -b non-existing http://example.com
```

密码验证操作
```shell
# 使用-u传递密码，用来访问ftp
curl -u alice:12345 ftp://example.com/
```

文件操作
```shell
# 上传文件
# -T 将文件上传到指定服务器
curl -u alice:12345 -T test ftp://example.com/

# 下载文件
# -o / --output可以用一个文件名来保存下载内容
#curl -o [filename] http://example.com/
curl -o output.html http://example.com/

# 下载已存在默认覆盖,可以使用`--no-clobber`来避免
curl --no-clobber -o output.html http://example.com/
```

# 常用选项
- -A
- 