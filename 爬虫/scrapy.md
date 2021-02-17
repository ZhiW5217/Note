1.使用scrapy
ps:linux环境

安装：sudo install Scrapy

1.1创建工程
$ scrapy startproject Spider
就创建属于自己的项目
同时生成一个Spider目录：

Spider
    |- scrapy.cfg                        项目部署文件
    |- Spider                     该项目的python模块，可以在这里加入代码
        |- __init__.py                 
        |- items.py                       主要是将爬取的非结构性的数据源提取结构性数据
        |- middlewares.py          
        |- pipelines.py                  将爬取的数据进行持久化存储
        |-  __pycache__  
        |-  settings.py                  配置文件
        |-  spiders                       放置spider代码的目录
            |-  __init__.py 
            |-  __pycache__
1.2创建爬虫模块
import scrapy
class Spider(scrapy.Spider):                       #创建的Spider是继承scrapy.Spider类
    name = "Spider"                                      #爬虫的名称（必须是项目里面唯一的）
    allowed_domain = ["xxx.com"]                #允许的域名（就是这个爬虫爬取链接的范围）
    start_urls = ["http://xxx.com/xxx/xxx"]     #开始爬取的链接
    
    def parse(self, response):                     
        pass
在spiders文件夹创建出这份代码，除了以上解释外，里面的parse()是一个Spider方法，被调用时，每个初始url响应后返回的Response对象，将会作为唯一的参数传递给该方法。该方法负责解析返回的数据，提取数据以及生成需要进一步处理的url的Responst对象

1.3网页解析
创建完爬虫模块后就可以进行网页解析了，Scrapy有自己的一套数据提取机制，成为选择器（selector）,它们通过特定的XPath或者CSS表达式来选择HTML文件中的某个部分，Scrapy选择器构建与lxml库上，这意味着它们在速度和解析准确性上非常相似。当然，也可以使用自己的，比如BeautifulSoup进行解析

Selector对象有四个基本的方法

xpath(query)：传入XPath表达式query，返回该表达式所对应的所有节点的selector list列表
css(query)：传入CSS表达式query，返回该表达式所对应的所有节点的selector list列表
extract(): 序列化该节点为Unicode字符串并返回list列表。
re(regex)：根据传入的正则表达式对数据进行提取，返回Unicode字符串列表。regex可以是一个已编译的正则，也可以是一个将为re.compile(regex)编译为正则表达式的字符串。
在spider类的parse()方法中，其中一个参数是response。所以使用选择器有两种方法

将response传入Selector：Selector(response).xpath()
Selector(response).xpath('//span/text()').extract()
或者直接调用：response.xpath()
response.xpath('//span/text()').extract()
Scrapy提供了一个简便的方式来查看表达式是否正确
打开命令行窗口后输入：
$ scrapy shell "http://xxx.com"
或者响应后
>>>response.xpath('//span/text()').extract()
就可以抽取出响应的数据了，返回的是Unicode格式

示例代码中extract()是提取里面的文本，并返回一个 SelectorList实例
如果只想提取第一个匹配的元素，可以调用选择器 .extract_first()，如果获取不到则返回None
可以通过使用extract_first(default='xxx')来自定义返回数值

了解了这些后可以写出一个parse()的代码
假设这个网页我们要提取的数据有标题，时间，评论，链接，且他们在网页的xxx里面

def parse(self, response):
    papers = response.xpath(".//*[@class='xxx']") 
    for paper in papers:
        url = paper.xpath(".//*[@class='title']/a/@href").extract()[0]
        title = paper.xpath(".//*[@class='title']/a/text()").extract()[0]
        time = paper.xpath(".//*[@class='ttime']/a/text())".extract()[0]
        content = paper.xpath(".//*[@class='content']/a/text()").extract()[0]
        item = SpiderItem(url=url, title=title, time=time,title=title, content=content)
        yield item
        yield scrapy.Request(url=url, callnack=self.parse)
yield item是把所有获得的数据封装起来,存放在Spideritem
yield scrapy.Request(url=url, callnack=self.parse)则是把获取到的url利用request对象构造为请求再利用callback回调到自己指定的Spideritem

1.4 item
Scrapy提供Item类。 Item对象是用于收集所抓取的数据的简单容器。它们提供了一个类似字典的 API，具有用于声明其可用字段的方便的语法
在items.py文件输入

class SpiderItem(scrapt.Item):
    url = scrapy.Field()
    time = scrapy.Field()
    title = scrapy.Field()
    content = scrapy.Field()
就创建好了，如果要拓展item就在原始item上来拓展item

class newSpiderItem(SpiderItem):
    xxx = scrapy.Field()
1.5Item Pipeline
当item在Spider中被手机后，它将会传递到Item PipeLine，利用Item PipeLine来进行数据保存
先上一个示例代码

import json
from scrapy.exceptions import DropItem
class SpiderPipeline(object):
    def __init__(self):
        self.file = open('xxx.json','w')
    def process_item(self,item,spider):
        if item['title']:
            line = json.dumps(dict(item)) + "\n"
            self.file.write(line)
            return item
        else:
            raise DropItem("没有找到标题"+title)
之后在setting.py的ITEM_PIPELINES变了中进行激活

ITEM_PIPELINES = {
       'Spider.pipelines.spiderPipeline':300}
process_item()方法可以用来判断是来自于哪个爬虫，item是被爬取对象，spider是爬取该item的Spider
Dropitem是一个错误捕获
300是判断运行顺序，范围为0~1000

除此之外，Scrapy内置了一些简单的存储方式生成一个带有爬取数据的输出文件自带的类型有：

JSON
JSON lines
CSV
XMl
Pickle
Marshal
食用方法：将命令行切换到项目目录，比如想保存为csv格式，输入命令：
$ scrapy crawl Spider -o papers.csv
就可以让Spider采集到的数据保存为papers.csv文件了
1.6运行
$ scrapy crawl Spider
就可以运行爬虫了，其中Spider就是我们定义的name
更过命令这里

也可以在spider.py文件添加一下代码运行

if __name =='__main__':
    process = CrawlerProcess(get_project_settings())
    process.crawl('spider')
    process.start()
2.深入Scrapy
通过以上运行一个爬虫肯定会觉得还不如自己写代码来爬虫，但Scrapy框架还有很多厉害的地方

2.1Spider
2.1.1Spider
继承于Spider是最简单的spider，常用属性为：

name
定义此爬虫名称的字符串。爬虫名称是爬虫如何由Scrapy定位（和实例化），因此它必须是唯一的。但是，没有什么能阻止你实例化同一个爬虫的多个实例。这是最重要的爬虫属性，它是必需的。
allowed_domains
允许此爬虫抓取的域的字符串的可选列表，指定一个列表可以抓取。
start_urls
当没有指定特定网址时，爬虫将开始抓取的网址列表。
custom_settings
运行此爬虫时将从项目宽配置覆盖的设置字典。详情在这里
crawler
此属性from_crawler()在初始化类后由类方法设置，并链接Crawler到此爬虫实例绑定到的对象。
Crawlers在项目中封装了很多组件，用于单个条目访问（例如扩展，中间件，信号管理器等）
settings
运行此爬虫的配置。
logger
用Spider创建的Python记录器name。
start_requests（）
此方法必须返回一个可迭代对象，该方法只被调用一次。
主要用于在启动阶段需要post登录某个网站时才使用
示例代码：
import scrapy
class Spider(scrapy.Spider):                       #创建的Spider是继承scrapy.Spider类
    name = "Spider"                                      #爬虫的名称（必须是项目里面唯一的）
    allowed_domain = ["xxx.com"]                #允许的域名（就是这个爬虫爬取链接的范围）
    def start_requests(self):
        return [scrapy.FromRequest("httP://www.xxx.com/login",
                                                             formdata={'user':'xxx','pass':'xxx'},
                                                             callback=self.login)]
    def login(self, response):
        pass
2.1.2CrawlSpidel
CrawlCpider除了从Spider继承过来的属性外，还提供了一个新的属性rules，rules是一个包含一个或多个Rule对象的集合，每个Rule对爬取网站的动作定义了特定的规则。如果多个Rule匹配了相同的链接，则根据它们在rules属性中被定义的顺序，第一个会被使用。
Rule类的原型为：

scrapy.contrib,spiders.Rule(link_extractor,callback=None,cb_kwargs=None,follow=None,process_links=None, process_request=None)
参数说明

link_extractor 是一个LinkExtractor对象，定义了如何从爬取到的页面提取链接。
callback回调函数接受一个response作为一个参数，应避免使用parse作为回调函数
cb_kwargs包含传递给回调函数的参数的字典
follow是一个布尔值，指定了根据规则从respose提取的链接是否需要跟进
process_links是一个可调用的或一个字符串（在这种情况下，将使用具有该名称的爬虫对象的方法），将使用指定从每个响应提取的每个链接列表调用该方法link_extractor。这主要用于过滤目的。
process_request 是一个可调用的或一个字符串（在这种情况下，将使用具有该名称的爬虫对象的方法），它将被此规则提取的每个请求调用，并且必须返回一个请求或无（过滤出请求）

link_extractor里面也大有乾坤，参数有：

allow: 提取满足正则表达式的链接
deny: 排除正则表达式匹配的链接，优先级高于allow
allow_domains: 允许的域名，可以是str,list
deny_domains：排除的域名，同上
restrict_xpaths: 提取满足xpath选择条件的链接，可以是str,list
restrict_css: 提取满足css选择条件的链接，可以是str,list
tags: 提取指定标记下的链接，默认从a和area中提取，可以是str或list。
attrs: 提取满足拥有属性的链接，默认为href，类型为list
unique:链接是否去重，类型为boolean
process_value: 值处理函数（这个函数是对已经捕获的函数进行修补）
以上详细在这里
关于process_value可以看这两个函数示例（来自于stackoverflow）：

import scrapy
from scrapy.contrib.spiders import CrawlSpider, Rule
from scrapy.contrib.linkextractors import LinkExtractor

class dmozItem(scrapy.Item):
    basic_url = scrapy.Field()

class dmozSpider(CrawlSpider):
    name = "dmoz"
    allowed_domains = ["scrapy.org"]
    start_urls = [
        "http://scrapy.org/",
    ]

    def clean_url(value):
        return value.replace('/../', '/')

    rules = (
        Rule(
            LinkExtractor(
                allow=('\S+/'),
                restrict_xpaths=(['.//ul[@class="navigation"]/a[1]',
                                  './/ul[@class="navigation"]/a[2]',
                                  './/ul[@class="navigation"]/a[3]',
                                  './/ul[@class="navigation"]/a[4]',
                                  './/ul[@class="navigation"]/a[5]']),
                process_value=clean_url
            ),
            callback='first_level'),
    )

    def first_level(self, response):
        taco = dmozItem()
        taco['basic_url'] = response.url
        return taco
def process_value(value):
    unique_id = re.search(r"/item/(\d+)", value).group(1)
    if unique_id in already_crawled_site_ids:
        return None
    return value

rules = [Rule(SgmlLinkExtractor(allow=['/item/\d+']), 'parse_item', process_value=process_value)]
2.1.3 XMLFeedSpider
XMLFeedSpider设计用于通过以特定节点名称迭代XML订阅源来解析XML订阅源。迭代器可以选自：iternodes，xml和html。iternodes为了性能原因，建议使用迭代器，因为xml和迭代器html一次生成整个DOM为了解析它。但是，html当使用坏标记解析XML时，使用作为迭代器可能很有用。

要设置迭代器和标记名称，必须定义以下类属性：

iterator
定义要使用的迭代器的字符串。它可以是：

'iternodes' - 基于正则表达式的快速迭代器
'html'- 使用的迭代器Selector。请记住，这使用DOM解析，并且必须加载所有DOM在内存中，这可能是一个大饲料的问题
'xml'- 使用的迭代器Selector。请记住，这使用DOM解析，并且必须加载所有DOM在内存中，这可能是一个大饲料的问题
它默认为：'iternodes'。
itertag
一个具有要迭代的节点（或元素）的名称的字符串。示例：
itertag = 'product'

namespaces
定义该文档中将使用此爬虫处理的命名空间的元组列表。在 与将用于自动注册使用的命名空间 的方法。(prefix, uri)prefixuriregister_namespace()
示例代码：

class YourSpider(XMLFeedSpider):

    namespaces = [('n', 'http://www.sitemaps.org/schemas/sitemap/0.9')]
    itertag = 'n:url'
adapt_response(response)
一种在爬虫开始解析响应之前，在响应从爬虫中间件到达时立即接收的方法。它可以用于在解析之前修改响应主体。此方法接收响应并返回响应（它可以是相同的或另一个）。

parse_node(response, selector)
对于与提供的标记名称（itertag）匹配的节点，将调用此方法。接收Selector每个节点的响应和 。覆盖此方法是必需的。否则，你的爬虫将不工作。此方法必须返回一个Item对象，一个 Request对象或包含任何对象的迭代器。

process_results(response, results)
对于由爬虫返回的每个结果（Items or Requests），将调用此方法，并且它将在将结果返回到框架核心之前执行所需的任何最后处理，例如设置项目ID。它接收结果列表和产生那些结果的响应。它必须返回结果列表

2.2 Item Loader
Item Loader提供了一种便捷的方式填充抓取到的items。虽然items可以使用自带的类字典形式API填充，但是itemsLoader提供了更便捷的API可以分析原始数据并对item进行赋值。也就是Items提供保存捉取数据的容器，而Item Loader提供的是填充容器的机制，且可以方便的被拓展，重写不同字段的解析规则，这对大型的爬虫项目后期维护非常有利，拓展新的功能更加方便。
以下是Item和Item Loader的对比代码：

class SpiderItem(scrapt.Item):
    url = scrapy.Field()
    time = scrapy.Field()
    title = scrapy.Field()
    content = scrapy.Field()
def parse(self, response):
    i = ItemLoader(item=Product(), response=response)
    l.add_xpath('title', '//div[@class="title1"]')
    l.add_xpath('title', '//div[@class="title2"]')
    l.add_xpath('time', '//div[@class="time"]')
    l.add_xpath('content', '//div[@class="content"]')
    return l.load_item()
可以发现的是ItemsLoader里面的name字段可以从页面中两个不同的XPAth位置提取，之后分配给name字段。下面类似操作后把所有数据收集起来，再使用l.load_item将数据实际填充到Item中

2.2.1 输入与输出处理器
Item Loder实现数据的收集处理和填充的功能都是通过输入与输出处理器来实现的，他们的功能是：

Item Loder在每个字段中都包含了一个输入处理器和输出处理器
输入处理器收到response后时立刻通过add_xpath(),add_css()或者add_value()等方法提取数据，经过输入处理器的结果被手机起来并且保存在Item Loder内，
收集到所有的数据后，调用Item Loder.load_item()方法来填充并返回item对象。load_item()方法内部先调用输出处理器来处理收集到的数据，最后把结果存入Item中。
下面是2个栗子：

from scrapy.loader import ItemLoader
from scrapy.loader.processors import TakeFirst, MapCompose, Join

class ProductLoader(ItemLoader):

    default_output_processor = TakeFirst()

    name_in = MapCompose(unicode.title)
    name_out = Join()

    price_in = MapCompose(unicode.strip)

    # ...
可以看到，输入处理器使用_in后缀声明，而输出处理器使用_out后缀声明。您还可以使用ItemLoader.default_input_processor和 ItemLoader.default_output_processor属性声明默认输入/输出 处理器。
输入和输出处理器可以在Item Loader定义中声明，这种方式声明输入处理器是很常见的。但是，还可以在item中声明：

import scrapy
from scrapy.loader.processors import Join, MapCompose, TakeFirst
from w3lib.html import remove_tags

def filter_price(value):
    if value.isdigit():
        return value

class Product(scrapy.Item):
    name = scrapy.Field(
        input_processor=MapCompose(remove_tags),
        output_processor=Join(),
    )
    price = scrapy.Field(
        input_processor=MapCompose(remove_tags, filter_price),
        output_processor=TakeFirst(),
    )
以上说了三种处理器声明方式：

ItemLoader类中声明类似field_in和field_out的属性
itemd 的字段中声明
ItemLoader默认处理器ItemLoader.default_input_processor和 ItemLoader.default_output_processor
这三种方式响应优先级是从上到下依次递减
2.2.2 Item Loader Context
所有输入和输出处理器之间共享的任意键/值的dict。它可以在声明，实例化或使用Item Loader时传递。它们用于修改输入/输出处理器的行为。

例如，假设有一个parse_length接收文本值并从中提取长度的函数：

def  parse_length （text ， loader_context ）：
    unit  =  loader_context 。get （'unit' ， 'm' ）
    ＃...长度解析代码在这里... 
    return  parsed_length
通过接受一个loader_context参数，该函数显式地告诉Item Loader它能够接收一个Item Loader上下文，因此Item Loader在调用它时传递当前活动的上下文，因此处理器功能（parse_length在这种情况下）可以使用它们。
有几种方法可以修改Item Loader上下文值：

通过修改当前活动的Item Loader上下文（context属性）：
loader = ItemLoader(product)
loader.context['unit'] = 'cm'
On Item Loader实例化（Item Loader构造函数的关键字参数存储在Item Loader上下文中）：
loader = ItemLoader(product, unit='cm')
On Item Loader声明，对于那些支持使用Item Loader上下文实例化的输入/输出处理器。MapCompose是其中之一：
class ProductLoader(ItemLoader):
    length_out = MapCompose(parse_length, unit='cm')
2.2.3 重用和拓展 Item Loader
随着你的项目越来越大，越来越多的爬虫，维护成为一个根本的问题，特别是当你必须处理每个爬虫的许多不同的解析规则，有很多异常，但也想重用公共处理器。

项目加载器旨在减轻解析规则的维护负担，同时不会失去灵活性，同时提供了扩展和覆盖它们的方便的机制。因此，项目加载器支持传统的python类继承，以处理特定爬虫（或爬虫组）的差异。

例如，假设某个特定站点以三个短划线（例如）包含其产品名称，并且您不希望最终在最终产品名称中删除那些破折号。—Plasma TV—

以下是如何通过重用和扩展默认产品项目Loader（ProductLoader）来删除这些破折号：

from scrapy.loader.processors import MapCompose
from myproject.ItemLoaders import ProductLoader

def strip_dashes(x):
    return x.strip('-')

class SiteSpecificLoader(ProductLoader):
    name_in = MapCompose(strip_dashes, ProductLoader.name_in)
另一种扩展项目加载器可能非常有用的情况是，当您有多种源格式，例如XML和HTML。在XML版本中，您可能想要删除CDATA事件。下面是一个如何做的例子：

from scrapy.loader.processors import MapCompose
from myproject.ItemLoaders import ProductLoader
from myproject.utils.xml import remove_cdata

class XmlProductLoader(ProductLoader):
    name_in = MapCompose(remove_cdata, ProductLoader.name_in)
对于输出处理器，更常用的方式是在item字段元数据里声明，因为通常它们依赖于具体的字段而不是网站

2.2.4 内置的处理器
除了可以使用可调用的函数作为输入输出处理器，Scrapy提供了一些常用的处理器，下面是一些内置的处理器

Identity
类原型： class scrapy.loader.processors.Identity
最简单的处理器，不进行任何处理，直接返回原来的数据,无参数
TakeFirst
类原型： class scrapy.loader.processors.TakeFirst
从接收到的值中返回第一个非空值/非空值，因此它通常用作单值字段的输出处理器。它不接收任何构造函数参数，也不接受Loader上下文
>>> from scrapy.loader.processors import TakeFirst
>>> proc = TakeFirst()
>>> proc(['', 'one', 'two', 'three'])
'one'
Join
类原型： class scrapy.loader.processors.Join(separator=u'')
返回用分隔符separatoe连续后的值，分隔符seeparator默认为空格。不接受Loader contexts。当使用默认分隔符的时候，这个处理器等同于Python字符串对象中join方法：''.join
>>> from scrapy.loader.processors import Join
>>> proc = Join()
>>> proc(['one', 'two', 'three'])
u'one two three'
>>> proc = Join('<br>')
>>> proc(['one', 'two', 'three'])
u'one<br>two<br>three'
Compose
类原型： class scrapy.loader.processors.Compose(functions,*default_loader_context)
由给定函数的组合构成的处理器。这意味着该处理器的每个输入值都被传递给第一个函数，并且该函数的结果被传递给第二个函数，依此类推，直到最后一个函数返回该处理器的输出值。
默认情况下，停止进程None值。可以通过传递关键字参数来更改此行为stop_on_none=False
>>> from scrapy.loader.processors import Compose
>>> proc = Compose(lambda v: v[0], str.upper)
>>> proc(['hello', 'world'])
'HELLO'
MapCompose
类原型： class scrapy.loader.processors.MapCompose(functions,*default_loader_context)
和Compose类似，也是用给定的多个方法的组合来构造处理器，不同的是内部结果在方法见传递的方式：
该处理器的输入值被迭代，并且第一函数被应用于每个元素。这些函数调用的结果（每个元素一个）被连接以构造新的迭代，然后用于应用第二个函数，等等，直到最后一个函数被应用于收集的值列表的每个值远。最后一个函数的输出值被连接在一起以产生该处理器的输出。
每个特定函数可以返回值或值列表，这些值通过应用于其他输入值的相同函数返回的值列表展平。函数也可以返回None，在这种情况下，该函数的输出将被忽略，以便在链上进行进一步处理。
此处理器提供了一种方便的方法来组合只使用单个值（而不是iterables）的函数。由于这个原因， MapCompose处理器通常用作输入处理器，因为数据通常使用选择器的 extract()方法提取，选择器返回unicode字符串的列表。
>>> def filter_world(x):
...     return None if x == 'world' else x
...
>>> from scrapy.loader.processors import MapCompose
>>> proc = MapCompose(filter_world, unicode.upper)
>>> proc([u'hello', u'world', u'this', u'is', u'scrapy'])
[u'HELLO, u'THIS', u'IS', u'SCRAPY']
SelectJmes
类原型： class scrapy.loader.processors.SelectJmes(json_path)
使用提供给构造函数的json路径查询值，并返回输出。需要运行jmespath。该处理器一次只需要一个输入。
>>> from scrapy.loader.processors import SelectJmes, Compose, MapCompose
>>> proc = SelectJmes("foo") #for direct use on lists and dictionaries
>>> proc({'foo': 'bar'})
'bar'
>>> proc({'foo': {'bar': 'baz'}})
{'bar': 'baz'}
和json一起使用

>>> import json
>>> proc_single_json_str = Compose(json.loads, SelectJmes("foo"))
>>> proc_single_json_str('{"foo": "bar"}')
u'bar'
>>> proc_json_list = Compose(json.loads, MapCompose(SelectJmes('foo')))
>>> proc_json_list('[{"foo":"bar"}, {"baz":"tar"}]')
[u'bar']
2.3 Item Pipeline
前面已经说了Item Pipeline的基本用法了，现在看的是如何利用item Pipeline来进行数据存储

2.3.1内置图片和文件下载
item Pipeline提供了一些可以重用的Pipeline，其中有filesPipeline和imagesPipeline，他们都实现了以下特性：

避免重新下载最近已经下载过的数据
指定存储的位置和方式
此外，imagesPipeline还提供了二位的特性：

将所有下载的图片转换成通用的格式（jpg）和模式（rgb）
粗略图生成
检测图像的宽/高，确保他们满足最小限制
使用FIlesPipeline方法如下：
1）在setting.py的ITEM_PIPELINES中添加一条'scrapy.pipelines.files.FilesPipeline':1
2）在item中添加两个字段，比如：
file_urls = scrapy.Field()
files = scrapy.Field()
3）在setting.py文件中添加下载路径，文件url所在的item字段，和文件结果信息所在item字段，比如：
FILES_STORE = '/HOME/XXX/SPIDER'
FILES_URLS_FIELD ='file_urls'
FILES_RESULT_FIELD = 'files'
使用files_expires设置文件过期时间，示例如下：
FILES_EXPIRES = 30 #30天过期

使用ImagesPIpeline方法如下：
1）在setting.py的ITEM_PIPELINES中添加一条'scrapy.pipelines.images.ImagesPipeline':1
2）在item中添加两个字段，比如：
image_urls = scrapy.Field()
images = scrapy.Field()
3 )在setting.py文件中添加下载路径，文件url所在的item字段，和文件结果信息所在item字段，比如：
IMAGES_STORE = '/HOME/XXX/SPIDER'
IMAGES_URLS_FIELD ='file_urls'
IMAGES_RESULT_FIELD = 'files'
除此之外，使用IMAGES_THUMS制作缩略图，并这是缩略图尺寸大小，并使用IMAGES_EXPIRES设置文件过期时间，示例如下：
IMAGES_THUMBS = {
'small':(50, 50),
'big': (270,270),
}
IMAGES_EXPIRES = 30 #30天过期

除此之外，Item Pipeline还有三个方法非常常用

open_spider(self, spider)
当spider被开启时，这个方法被调用
close_spider(self, spider)
当spider被关闭是，这个方法被调用
from_crawler(cls, crawler)
这个类方法从Crawles属性中创建一个pipeline实例，Crawle对象能够接触所有Scrapy的核心组件，比如setting
以下是一个存储到MongoDB的示例代码：

import pymongo



class ZhihuspiderPipeline(object):

    def __init__(self, mongo_url, mongo_db):
        self.mongo_url = mongo_url
        self.mongo_db = mongo_db

    @classmethod
    def from_crawler(cls, crawler):
        return cls(
            mongo_url=crawler.settings.get('MONGO_URL'),
            mongo_db=crawler.settings.get('MONGO_DATABASE', 'items')
        )

    def open_spider(self, spider):
        self.client = pymongo.MongoClient(self.mongo_url)
        self.db = self.client[self.mongo_db]

    def close_spider(self, spider):
        self.client.close()
    
    def process_item(self, item, spider):
        self.db.UserInfo.insert(dict(item))
        return item
代码中首先通过from_crawler方法，获取setting中的MongoDB的URL和数据库名称，从而创建一个MongoPIpeline实例，在SPider开始运行是，在open_spider方法中建立数据库连接，当Spider关闭是，在close_spider方法中关闭数据库连接。
当是，这里配置的Pipeline会作用于所有的Spider，假如项目中有很多Spider在运行，处理起来就会很麻烦。所以通过process_item(self,item,spider)中的SPider参数判断是来自于哪个爬虫，不过还有更好的方法：
就是配置SPider类中的suctom_settings对象，为每一个Spider配置不同的pipeline

class MySpider(CrawlSpider):
    custom_settings = {
        'ITEM_PIPELINES':{
            'test.pipelines.TestPipeline.TestPipeline':1,
        }
    }
2.4 请求与响应
在编写Spider的模块中接触最紧密的是请求和响应。

2.4.1Request对象
一个Request对象代表着一个HTTP请求，通常在Spider类中产生，然后传递给下载器，最后返回一个响应
类原型：class scrapy.http.Request(url[, callback, method='GET', headers, body, cookies, meta, encoding='utf-8', priority=0, dont_filter=False, errback])
构造参数说明：

url（string） - 此请求的网址
callback（callable） - 将使用此请求的响应（一旦下载）作为其第一个参数调用的函数。如果请求没有指定回调，parse()将使用spider的 方法。请注意，如果在处理期间引发异常，则会调用errback。
method（string） - 此请求的HTTP方法。默认为’GET’。
meta（dict） - 属性的初始值Request.meta。如果给定，在此参数中传递的dict将被浅复制。
body（str或unicode） - 请求体。如果unicode传递了a，那么它被编码为 str使用传递的编码（默认为utf-8）。- 如果 body没有给出，则存储一个空字符串。不管这个参数的类型，存储的最终值将是一个str（不会是unicode或None）。
headers（dict） - 这个请求的头。dict值可以是字符串（对于单值标头）或列表（对于多值标头）。如果 None作为值传递，则不会发送HTTP头。
cookie（dict或list） - 请求cookie。这些可以以两种形式发送。
encoding（string） - 此请求的编码（默认为’utf-8’）。此编码将用于对URL进行百分比编码，并将正文转换为str（如果给定unicode）。
priority（int） - 此请求的优先级（默认为0）。调度器使用优先级来定义用于处理请求的顺序。具有较高优先级值的请求将较早执行。允许负值以指示相对低优先级。
dont_filter（boolean） - 表示此请求不应由调度程序过滤。当您想要多次执行相同的请求时忽略重复过滤器时使用。小心使用它，或者你会进入爬行循环。默认为False。
errback（callable） - 如果在处理请求时引发任何异常，将调用的函数。这包括失败的404 HTTP错误等页面。
cookies参数设置的两种方法
使用字典

request_with_cookies = Request(url="http://www.example.com",
                            cookies={'currency': 'USD', 'country': 'UY'})
使用字典列表发送

request_with_cookies = Request(url="http://www.example.com",
                              cookies=[{'name': 'currency',
                                       'value': 'USD',
                                       'domain': 'example.com',
                                       'path': '/currency'}]) 
当某些网站返回Cookie（在响应中）时，这些Cookie会存储在该域的Cookie中，并在将来的请求中再次发送。这是任何常规网络浏览器的典型行为。但是，如果由于某种原因，想要避免与现有Cookie合并，可以设置Requ.meta中dont_merge_cookies字段的值。示例如下

request_with_cookies = Request(url="http://www.example.com",
                              cookies={'currency': 'USD', 'country': 'UY'},
                              meta={'dont_merge_cookies': True})
errback的使用方法示例：

import scrapy

from scrapy.spidermiddlewares.httperror import HttpError
from twisted.internet.error import DNSLookupError
from twisted.internet.error import TimeoutError, TCPTimedOutError

class ErrbackSpider(scrapy.Spider):
   name = "errback_example"
   start_urls = [
       "http://www.httpbin.org/",              # HTTP 200 expected
       "http://www.httpbin.org/status/404",    # Not found error
       "http://www.httpbin.org/status/500",    # server issue
       "http://www.httpbin.org:12345/",        # non-responding host, timeout expected
       "http://www.httphttpbinbin.org/",       # DNS error expected
   ]

   def start_requests(self):
       for u in self.start_urls:
           yield scrapy.Request(u, callback=self.parse_httpbin,
                                   errback=self.errback_httpbin,
                                   dont_filter=True)

   def parse_httpbin(self, response):
       self.logger.info('Got successful response from {}'.format(response.url))
       # do something useful here...

   def errback_httpbin(self, failure):
       # log all failures
       self.logger.error(repr(failure))

       # in case you want to do something special for some errors,
       # you may need the failure's type:

       if failure.check(HttpError):
           # these exceptions come from HttpError spider middleware
           # you can get the non-200 response
           response = failure.value.response
           self.logger.error('HttpError on %s', response.url)

       elif failure.check(DNSLookupError):
           # this is the original request
           request = failure.request
           self.logger.error('DNSLookupError on %s', request.url)

       elif failure.check(TimeoutError, TCPTimedOutError):
           request = failure.request
           self.logger.error('TimeoutError on %s', request.url)
下面写的是Request的子类 FormRquest类，专门用来处理html表单，尤其对隐藏表单的处理非常方便，适合用来完成登录操作
类原型：class scrapy.http.FormRequest(url[, formdata, ...])
其中构造参数formdata可以是字典，也可以是可迭代的（key,value）元祖，代表着需要提交的表单数据。
示例如下：

return [FormRequest(url="http://www.example.com/post/action",
                    formdata={'name': 'John Doe', 'age': '27'},
                    callback=self.after_post)]
通常网站通过<input type="hidden">实现对某些表单字段的预填充，就像知乎的隐藏表单的_xfv数值，Scray在FromRequest类提供了一个类方法from_reponse来解决隐藏表单这个问题。方法原型如下：

classmethod from_response(response[, formname=None, formid=None, formnumber=0, formdata=None, formxpath=None, formcss=None, clickdata=None, dont_click=False, ...])
参数说明：

response（Responseobject） - 包含将用于预填充表单字段的HTML表单的响应
formname（string） - 如果给定，将使用name属性设置为此值的形式。
formid（string） - 如果给定，将使用id属性设置为此值的形式。
formxpath（string） - 如果给定，将使用匹配xpath的第一个表单。
formcss（string） - 如果给定，将使用匹配css选择器的第一个形式。
formnumber（integer） - 当响应包含多个表单时要使用的表单的数量。第一个（也是默认）是0。
formdata（dict） - 要在表单数据中覆盖的字段。如果响应元素中已存在字段，则其值将被在此参数中传递的值覆盖。
clickdata（dict） - 查找控件被点击的属性。如果没有提供，表单数据将被提交，模拟第一个可点击元素的点击。除了html属性，控件可以通过其相对于表单中其他提交表输入的基于零的索引，通过nr属性来标识。
dont_click（boolean） - 如果为True，表单数据将在不点击任何元素的情况下提交。
下面是一个实现登录功能的示例代码：

import scrapy

class LoginSpider(scrapy.Spider):
    name = 'example.com'
    start_urls = ['http://www.example.com/users/login.php']

    def parse(self, response):
        return scrapy.FormRequest.from_response(
            response,
            formdata={'username': 'john', 'password': 'secret'},
            callback=self.after_login
        )

    def after_login(self, response):
        # check login succeed before going on
        if "authentication failed" in response.body:
            self.logger.error("Login failed")
            return
2.4.2 Response对象
Response对象代表着HTTP响应，Response通常是从下载器获取然后交给Spider处理：
类原型：class scrapy.http.Response(url[, status=200, headers=None, body=b'', flags=None, request=None])
构造参数说明：

url（string） - 此响应的URL
status（integer） - 响应的HTTP状态。默认为200。
headers（dict） - 这个响应的头。dict值可以是字符串（对于单值标头）或列表（对于多值标头）。
body（str） - 响应体。它必须是str，而不是unicode，除非你使用一个编码感知响应子类，如 TextResponse。
flags（list） - 是一个包含属性初始值的 Response.flags列表。如果给定，列表将被浅复制。
request（Requestobject） - 属性的初始值Response.request。这代表Request生成此响应。
meta(dict)：用来初始化Response.meta
关于参数的其他补充：

request
Request生成此响应的对象。在响应和请求通过所有下载中间件后，此属性在Scrapy引擎中分配。特别地，这意味着：
HTTP重定向将导致将原始请求（重定向之前的URL）分配给重定向响应（重定向后具有最终URL）。
Response.request.url并不总是等于Response.url
此属性仅在爬虫程序代码和 Spider Middleware中可用，但不能在Downloader Middleware中使用（尽管您有通过其他方式可用的请求）和处理程序response_downloaded。
meta
的快捷方式Request.meta的属性 Response.request对象（即self.request.meta）。
与Response.request属性不同，Response.meta 属性沿重定向和重试传播，因此您将获得Request.meta从您的爬虫发送的原始属性。
也可以看看
Request.meta 属性
flags
包含此响应的标志的列表。标志是用于标记响应的标签。例如：’cached’，’redirected ‘等等。它们显示在Response（_ str_ 方法）的字符串表示上，它被引擎用于日志记录。
copy（）
返回一个新的响应，它是此响应的副本。
replace（[ url，status，headers，body，request，flags，cls ] ）
返回具有相同成员的Response对象，但通过指定的任何关键字参数赋予新值的成员除外。该属性Response.meta是默认复制。
urljoin（url ）
通过将响应url与可能的相对URL 组合构造绝对url。
这是一个包装在urlparse.urljoin，它只是一个别名，使这个调用：
urlparse.urljoin(response.url, url)
同样的，Response有一个子类TextResponse,多了一个智能编码的功能。
类原型：class scrapy.http.TextResponse(url[, encoding[, ...]])
构造参数encoding是一个包含编码的字符串，如果使用一个unicode编码的body构造出TextResponse实例，那body属性会使用encoding进行编码。
TextResponse类除了具有Response属性，还拥有自己的属性：

encoding：包含编码的字符串。编码的优先级由高到低如下所示：
1）首先选用构造器中传入的encoding
2）选用http头中Content-Type字段的编码。如果编码无效，则被忽略，继续尝试下面的规则
3）选用响应body中的编码
4）最后猜测响应的编码，这种方式是比较脆弱
selector:以当前响应为目标的选择器实例。
TextResponse类除了支持Response中的方法，还支持以下方法response
body_as_unicode():返回unicode编码的响应body内容。等价于：
response.body.decode(response.encoding)
xpath(query):等价于TextResponse.selector.xpath(query).示例如下：
response.xpath('//p')
css(query):等价于TextResponse.selector.css(query).示例如下：
response.css('//p')
2.5下载器中间件
下载器中间件是介于Scrapy的request/response处理的钩子框架，是用于全局修改Scrapy的request和response，可以帮助我们定制自己的爬虫系统
激活哦下载器中间件的方法如下
在setting.py里面的DOWNLOADER_MIDDLEWARES修改，示例如下：
DOWNLOADER_MIDDLEWARES = {
‘myproject.middlewares.CustomDownloaderMiddleware': 543,
}
后面数值的设置要看自己的需求，越接近0越靠近引擎，越接近1000越靠近下载器，数字设置一般看自己需要先用到哪些中间件，就把数字设定得比哪个中间件大
中间件组建定义了以下python类，可以根据这些类来编写自己的中间件

process_request(request,spider)
process_response(request,response,spider)
process_exception(request,exception,spider)
2.5.1process_request(request,spider)
当每个request通过下载中间件时，该方法被调用，返回值必须为none，response对象，request对象中的一个或raise IgnoreRequest异常
参数说明：
Request：处理的request
Spider：该Request对应的Spider
返回值：如果返回None,Scrapy将继续处理该Request，执行其他中间件的相应方法，直到合适的下载器处理函数被调用，该Request被执行（其Response下载）
如果返回Response对象，SCrapy不会调用下面2个方法，或相应的下载方法，将返回该response。已安装的中间件的process_response()方法则会在每个respon返回时被调用
如果返回request对象，Scrapy则停止调用process_request方法并重新调度放回的Request。当新返回的Request被执行后，相应地中间件链将会根据下载的Response被调用
如果是Raise IgnoreRequest异常，则安装的下载中间件的process_exception()方法会被调用。如果没有任何一个方法处理该异常，则Request的errback方法会被调用。如果没有代码处理抛出的异常，则该异常被忽略且不记录

2.5.2process_request(request,response,spider)
该方法主要用来处理产生的Response返回值必须为none，response对象，request对象中的一个或raise IgnoreRequest异常
参数说明：
Request：处理的request
response:处理的response
Spider：该Request对应的Spider
返回值：
如果返回Response对象，可以与传入的respon相同，也可以是新的对象，该Response会被链中的其他中间见的process_reponse()方法处理
如果返回request对象，Scrapy则停止调用process_request方法并重新调度放回的Request。当新返回的Request被执行后，相应地中间件链将会根据下载的Response被调用
如果是Raise IgnoreRequest异常，则安装的下载中间件的process_exception()方法会被调用。如果没有任何一个方法处理该异常，则Request的errback方法会被调用。如果没有代码处理抛出的异常，则该异常被忽略且不记录

2.5.3process_request(request,exceptionmspider)
当下载处理器或process_request()抛出异常，比如IgnoreRequest异常时，Scrapy调用process_exception().process_exception()应该返回none，response对象，request对象中的一个
参数说明：
Request：处理的request
exception:抛出的异常
Spider：该Request对应的Spider
返回值：
如果返回None,Scrapy将会继续处理该异常，接着调用已安装的其他中间件的process_exception()方法，知道所有中间件都被调用完毕，则调用默认的异常处理
如果返回response对象，则已安装的中间级链的process_response()方法被调用。Scrapy将不会调用任何其他中间件的process_exception()方法
如果返回Request对象，则返回的request将会被重新调用下载，这将停止中间级的process_exception()方法执行，类似于返回respon对象的处理

下面是自定义下载器中间件的两个栗子：

设置User-Agent字段，需要自己在setting,py中设置User-Agent列表

class RandomUserAgent(object):
    def __init__(self,agents):
        self.agents =agents
        
    @classmethod
    def from_crawler(cls,crawler):
        return cls(crawler.settings.getlist('USER_AGENTS'))
    
    def process_request(self,request,spider):
        request.headers.setdefault('User-Agent', random.choice(self.agents))
动态设置request代理ip,需要自己在setting,py中设置IPlist列表

class RandomProxy(object):
    def __init__(self,iplist):
        self.iplist=iplist

    @classmethod
    def from_crawler(cls,crawler):
        return cls(crawler.settings.getlist('IPLIST'))

    def process_request(self, request, spider):
        proxy = random.choice(self.iplist)
        request.meta['proxy'] =proxy
2.6Spider中间件
这个看官方文档就好了 ——这里

3.部署
这里都是编写完爬虫代码后的其他一些应用，不完全属于部署

3.1分布式下载器:Crawlea
官方提供了一个分布式下载器，用来帮助我们躲避反爬虫的措施。首先在官网注册。之后拿到APIKEY用作验证
不过是需要钱的
官网：https://app.scrapinghub.com/account/login/?next=/next=/account/login
安装：pip install scrapy-crawlera
之后修改setting.py
DOWNLOADER_MIDDLEWARES ={'scrapy_crawlera.CrawleraMiddleware':300}
CRAWLERA_ENABLED = True
CRAWLERA_APIKEY = '<API key>'

3.2Scrapyd
是的。官方又提供了一个部署爬虫非常有用的工具，Scrapyd是运行Scrapy爬虫的服务程序，它吃屎以http命令方式通过json api进行发布、删除、启动、停止爬虫程序的操作，而且它可以同时管理多个爬虫，每个爬虫还可以有多个版本，也是部署分布式爬虫有效手段
安装：pipi install scrapyd
使用：输入scrapyd启动，在浏览器输入：http://127.0.0.1:6800/就可以看到界面了
API使用示例如下：

# -*- coding: utf-8 -*-

import requests
import json 

baseUrl ='http://127.0.0.1:6800/'
daemUrl ='http://127.0.0.1:6800/daemonstatus.json'
listproUrl ='http://127.0.0.1:6800/listprojects.json'
listspdUrl ='http://127.0.0.1:6800/listspiders.json?project=%s'
listspdvUrl= 'http://127.0.0.1:6800/listversions.json?project=%s'
listjobUrl ='http://127.0.0.1:6800/listjobs.json?project=%s'
delspdvUrl= 'http://127.0.0.1:6800/delversion.json'

#http://127.0.0.1:6800/daemonstatus.json
#查看scrapyd服务器运行状态
r= requests.get(daemUrl)
print '1.stats :\n %s \n\n'  %r.text  

#http://127.0.0.1:6800/listprojects.json
#获取scrapyd服务器上已经发布的工程列表
r= requests.get(listproUrl)
print '1.1.listprojects : [%s]\n\n'  %r.text
if len(json.loads(r.text)["projects"])>0 :
    project = json.loads(r.text)["projects"][0]

#http://127.0.0.1:6800/listspiders.json?project=myproject
#获取scrapyd服务器上名为myproject的工程下的爬虫清单
listspd=listspd % project
r= requests.get(listspdUrl)
print '2.listspiders : [%s]\n\n'  %r.text 
if json.loads(r.text).has_key("spiders")>0 :
    spider =json.loads(r.text)["spiders"][0]


#http://127.0.0.1:6800/listversions.json?project=myproject
##获取scrapyd服务器上名为myproject的工程下的各爬虫的版本
listspdvUrl=listspdvUrl % project
r = requests.get(listspdvUrl)
print '3.listversions : [%s]\n\n'  %rtext 
if len(json.loads(r.text)["versions"])>0 :
    version = json.loads(r.text)["versions"][0]

#http://127.0.0.1:6800/listjobs.json?project=myproject
#获取scrapyd服务器上的所有任务清单，包括已结束，正在运行的，准备启动的。
listjobUrl=listjobUrl % proName
r=requests.get(listjobUrl)
print '4.listjobs : [%s]\n\n'  %r.text 


#schedule.json
#http://127.0.0.1:6800/schedule.json -d project=myproject -d spider=myspider
#启动scrapyd服务器上myproject工程下的myspider爬虫，使myspider立刻开始运行，注意必须以post方式
schUrl = baseurl + 'schedule.json'
dictdata ={ "project":project,"spider":spider}
r= reqeusts.post(schUrl, json= dictdata)
print '5.1.delversion : [%s]\n\n'  %r.text 


#http://127.0.0.1:6800/delversion.json -d project=myproject -d version=r99'
#删除scrapyd服务器上myproject的工程下的版本名为version的爬虫，注意必须以post方式
delverUrl = baseurl + 'delversion.json'
dictdata={"project":project ,"version": version }
r= reqeusts.post(delverUrl, json= dictdata)
print '6.1.delversion : [%s]\n\n'  %r.text 

#http://127.0.0.1:6800/delproject.json -d project=myproject
#删除scrapyd服务器上myproject工程，注意该命令会自动删除该工程下所有的spider，注意必须以post方式
delProUrl = baseurl + 'delproject.json'
dictdata={"project":project  }
r= reqeusts.post(delverUrl, json= dictdata)
print '6.2.delproject : [%s]\n\n'  %r.text 
3.3Scrapyd-client
懒得截图访问这里查看

作者：陈思煜
链接：https://www.jianshu.com/p/1e669c17c7ad#1%E4%BD%BF%E7%94%A8scrapy
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。