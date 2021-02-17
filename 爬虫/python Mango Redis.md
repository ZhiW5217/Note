# 创建MongoClient对象，建立和mongodb的链接
client = pymongo.MongoClient('localhost', 27017)

# 指定数据名
db = client.Papers

# 指定集合名
collec = db.PapersCollection

# 文档的插入
collec.insert_one(item)
	
	
r = redis.StrictRedis(host='localhost', port=6379,db=0)
data = json.dumps(dict(item), ensure_ascii=False)
r.lpush("blog", data)
print(f'存储成功{data}')