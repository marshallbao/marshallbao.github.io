# Xargs

输入输出

linux 命令可以从两个地方读取要处理的内容，一个是通过命令行参数，一个是标准输入;

```
# 标准输入
$ echo 'main' | cat

# 命令行参数
$ cat test.sh

# 大多数命令有一个参数  -  如果直接在命令的最后指定 -  则表示从标准输入中读取，例如：
$ echo 'main' | cat test.sh -

# 这条命令会输出 main 和 test.sh 内容，一些少数命令是不会处理标准标准输入的,例如：rm,kill,一下命令是不能执行的；
$ echo '516' | kill
$ echo 'test' | rm -f
```

xargs 

xargs 命令可以通过管道接受字符串，并将接收到的字符串通过空格分割成许多参数(默认情况下是通过空格分割) 然后将参数传递给其后面的命令，作为后面命令的命令行参数；

```
# 默认将前面字符串全放在最后面，例如
echo "- A ss" | xargs cat 等同于$ cat -A ss

# 使用 -i 参数默认的前面输出用{}代替，-I 参数可以指定其他代替代替字符，如例子中的[]
find . -name “file” | xargs -i cp {} ..
find . -name "file" | xargs -I [] cp [] ..

# 选项

```

