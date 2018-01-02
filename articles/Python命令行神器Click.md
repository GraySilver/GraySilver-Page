## Python命令行神器Click

![](http://graysliver.oss-cn-shenzhen.aliyuncs.com/click.JPG)

### 简单入门

```python
# hello.py
import click

@click.command()
def hello():
  click.echo('Hello World!')

if __name__ == '__main__':
    hello()
```

运行 python hello.py

> Hello World!

运行 python hello.py --help 

> Usage: hello.py [OPTIONS]
>
> Options:
>   --help  Show this message and exit.



### Option

简单来说就是用option构造命令行参数。

```python
#hello.py
import click
@click.command()
@click.option('--name', prompt='enter your name here:',
            help='greet to given name')
def hello(name):
    click.echo('Hello World! hello %s' % name)

if __name__ == '__main__':
    hello()
```

以上代码的意思是在控制台输入name参数变量，会给hello传参输出结果。

运行 python hello.py

> enter your name here: : Yang
> Hello World! hello Yang

运行 python hello.py --name Yang

> Hello World! hello Yang

运行 python hello.py --help

>Usage: hello.py [OPTIONS]
>
>Options:
>  --name TEXT  greet to given name
>  --help       Show this message and exit



### 使用Group

使用group可以将多个子函数组合成子命令。

有两种方式：

>通过click.group()装饰器定义一个主函数A，通过A.add_command(子函数B)把别的函数加入到 group函数中去。

> 通过@click.group()装饰器定义一个主函数A，在定义子命令行函数时，用@A.command()来替代@click.command()装饰子函数B。

```python
# run.py
import click

@click.group()
def run():
    pass

@click.command()
def up():
    click.echo("1")

@click.command()
def down():
	 click.echo("-1")

if __name__ == '__main__':
    run()
```

运行 run.py up

> 1

运行 run.py down

> -1

Click的其他用法可参考官方教程，主要用上面几种简单的情况。



### Reference

http://blog.csdn.net/eagle5063/article/details/53929645

http://click.pocoo.org/5/options/#basic-value-options



