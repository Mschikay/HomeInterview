
[Java NIO](http://tutorials.jenkov.com/java-nio/overview.html)

[阿里 NIO](https://camo.githubusercontent.com/725bb9953df54438fadb74a1bea180ed817b9e27/68747470733a2f2f757365722d676f6c642d63646e2e786974752e696f2f323031382f392f322f313635393862663735386337343765363f773d39393926683d36373926663d706e6726733d3534343836)
## FileChannel
you are not guaranteed that data written to the channel is actually written to disk, until you call the force() method.
## SocketChannel
When the write is non-blocking. 
    Notice how the SocketChannel.write() method is called inside a while-loop. There is no guarantee of how many bytes the write() method writes to the SocketChannel. Therefore we repeat the write() call until the Buffer has no further bytes to write.


## selector
Selector must be non-blocking
    you cannot use FileChannel's with a Selector since FileChannel's cannot be switched into non-blocking mode. 

对线程的创建、切换、销毁很消耗内存。一个选择器由一个线程控制；一个选择器可以控制多个通道。
## flip
The flip() method switches a Buffer from writing mode to reading mode. Calling flip() sets the position back to 0, and sets the limit to where position just was.