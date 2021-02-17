# settings.py 设置
```
USER_AGENT = 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/86.0.4240.111 Safari/537.36 Edg/86.0.622.58'

DUPEFILTER_CLASS = "scrapy_redis.dupefilter.RFPDupeFilter"
SCHEDULER = "scrapy_redis.scheduler.Scheduler"
SCHEDULER_PERSIST = True

ITEM_PIPELINES = {
    # 'example.pipelines.ExamplePipeline': 300,
    'scrapy_redis.pipelines.RedisPipeline': 400,
}

# REDIS_HOST='192.168.10.132'
REDIS_HOST = '127.0.0.1'
REDIS_PORT = 6379
REDIS_PARAMS = {
    'password': '',
}

SPIDER_MIDDLEWARES = {
    'scrapy.spidermiddlewares.offsite.OffsiteMiddleware': None,
}
```


# 爬取页面设置
```
class BlogSpider(RedisCrawlSpider):
    name = 'blog'
    # allowed_domains = ['blog.sina.com.cn']
    # start_urls = ['http://blog.sina.com.cn/s/articlelist_1525875683_0_1.html']

    redis_key = 'sina:start_urls'


    # 翻页
    next_url = LinkExtractor(allow=(r"articlelist_.*?.html"))

    # 详情
    content_url = LinkExtractor(restrict_xpaths=(".//span[@class='atc_title']/a"))
    rules = [
        Rule(next_url, follow=True),
        Rule(content_url, callback="parse_content")
    ]

	def __init__(self, *args, **kwargs):
		# Dynamically define the allowed domains list.
		
		# 向redis 中写入起始url
		self.r = redis.StrictRedis(host='localhost', port=6379)
		self.r.lpush("sina:start_urls", "http://blog.sina.com.cn/s/articlelist_1525875683_0_1.html")
		
		# 修改super中的类名
		domain = kwargs.pop('domain', '')
		self.allowed_domains = filter(None, domain.split(','))
		super(BlogSpider, self).__init__(*args, **kwargs)
		
	def parse_content(self, response):
		pass

```