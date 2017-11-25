## 工厂模式

#### What

创建型设计模式处理对象创建相关的问题，目标是当直接创建对象（在Python中是通过__init__()函数实现的）不太方便时，提供更好的方式。

在工厂设计模式中，客户端可以请求一个对象，而无需知道这个对象来自哪里；也就是，使用哪个类来生成这个对象。工厂背后的思想是简化对象的创建。与客户端自己基于类实例化直接创建对象相比，基于一个中心化函数来实现，更易于追踪创建了哪些对象。通过将创建对象的代码和使用对象的代码解耦，工厂能够降低应用维护的复杂度。简单来说，就是抽象对象（父类）——具体对象（子类）。

工厂通常有两种形式：一种是**工厂方法（Factory Method）**，它是一个方法（或以地道的Python术语来说，是一个函数），对不同的输入参数返回不同的对象；第二种**是抽象工厂**，它是一组用于创建一系列相关事物对象的工厂方法。

有个简单的例子，制造塑料玩具的压塑粉都是一样的，但使用不同的塑料模具就能产出不同的外形。比如，有一个工厂方法，输入是目标外形（鸭子或小车）的名称，输出则是要求的塑料外形，正如同一份三文鱼，大佬和菜鸟做出来肯定不一样的。

![text](1.JPG)

Django框架是使用工厂方法来创建表单字段的，Django的forms模块支持不同种类字段(CharField、EmailField) 的 创 建 和 定 制(max_length、required)。

#### Why

如果你创建对象的代码分布在不同地方，而不是仅在一个函数/方法中，无法跟踪这些对象时，你可以考虑使用工厂方法。

**工厂方法集中在一个地方创建对象，使得对象跟踪起来容易。**

创建多个工厂方法也完全没有问题，实践中通常也这么做，对相似的对象创建进行逻辑分组，每个工厂方法负责一个分组。 例如，有一个工厂方法负责连接到不同的数据库（MySQL、SQLite），另一个工厂方法负责创建要求的
几何对象（圆形、三角形），等等 。

若需要将对象的创建和使用解耦，工厂方法也能派上用场。创建对象时，我们并没有与某个特定类耦合/绑定到一起，而只是通过调用某个函数来提供关于我们想要什么的部分信息。这意味着修改这个函数比较容易，不需要同时修改使用这个函数的代码 。

#### How

举个例子，我们有一些输入数据存储在一个XML文件和一个JSON文件中，要对这两个文件进行解析，获取一些信息。同时，希望能够对这些（以及将来涉及的所有）外部服务进行集中式的客户端连接。我们使用工厂方法来解决这个问题。虽然仅以XML和JSON为例，但为更多的服务添加支持也很简单 。

```python
import xml.etree.ElementTree as etree
import json

#类JSONConnector解析JSON文件，通过parsed_data()方法以一个字典（dict）的形式返回数据。修饰器property使parsed_data()显得更像一个常规的变量，而不是一个方法。
class JSONConnector:
    def __init__(self, filepath):
        self.data = dict()
        with open(filepath, mode='r', encoding='utf-8') as f:
        self.data = json.load(f)
	@property
	def parsed_data(self):
		return self.data
    
# 类XMLConnector解析 XML 文件，通过parsed_data()方法以xml.etree.Element列表的形式返回所有数据，如下所示。
class XMLConnector:
	def __init__(self, filepath):
		self.tree = etree.parse(filepath)
	@property
	def parsed_data(self):
		return self.tree
    
#函数connection_factory是一个工厂方法，基于输入文件路径的扩展名返回一个JSONConnector或XMLConnector的实例，如下所示。
def connector_factory(filepath):
	if filepath.endswith('json'):
		connector = JSONConnector
   	elif filepath.endswith('xml'):
		connector = XMLConnector
	else:
		raise ValueError('Cannot connect to {}'.format(filepath))
	return connector(filepath)

# 函数connect_to()对connection_factory()进行包装，添加了异常处理，如下所示。
def connect_to(filepath):
    factory = None
    try:
    	factory = connection_factory(filepath)
    except ValueError as ve:
    	print(ve)
    return factory

# 函数main()演示如何使用工厂方法设计模式。
def main():
    sqlite_factory = connect_to('data/person.sq3')
    print()
    xml_factory = connect_to('data/person.xml')
    xml_data = xml_factory.parsed_data
    liars = xml_data.findall(".//{}[{}='{}']".format('person',
    'lastName', 'Liar'))
    
    print('found: {} persons'.format(len(liars)))
    for liar in liars:
        print('first name: {}'.format(liar.find('firstName').text))
        print('last name: {}'.format(liar.find('lastName').text))
    	[print('phone number ({})'.format(p.attrib['type']),
    	p.text) for p in liar.find('phoneNumbers')]
    
    print()
    json_factory = connect_to('data/donut.json')
    json_data = json_factory.parsed_data
    print('found: {} donuts'.format(len(json_data)))
    
    for donut in json_data:
        print('name: {}'.format(donut['name']))
        print('price: ${}'.format(donut['ppu']))
        [print('topping: {} {}'.format(t['id'], t['type'])) for t in donut['topping']
```









