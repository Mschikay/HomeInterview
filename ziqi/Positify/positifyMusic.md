# aliyun oss
#### 怎么样推荐最流行的音乐
在数据库里有music collection，向用户推荐热度排名前五个。music collection 里面有音乐的url，可以通过url访问到音乐。

音乐的图片 文件都在oss云端，用url访问即可

我们使用APLayer来播放音乐。

#### token
我们首先用到了passport，使得每一个请求都提供一个凭证，而不是用session

使用jwt，在post的时候，放在请求中
