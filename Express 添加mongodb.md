## Express 添加mongodb

#### 封装代码

```
const MongoClient = require('mongodb').MongoClient;

const url = 'mongodb://localhost:27017';

const dbName = 'temp';

class DB {

    constructor() {
        DB.connect();
    }

    static getInstance() {
        if (!this.dbInstance) {
            this.dbInstance = new DB();
        }
        return this.dbInstance
    }

    static connect() {
        this.client = new MongoClient(url, {
            useNewUrlParser: true,
            useUnifiedTopology: true,
            authSource: "temp",
            auth: {
                user: 'appadmin',
                password: '409116'
            }
        })

        this.client.connect((err) => {
            if (err) {
                throw err
            }
            this.dbInstance = this.client.db(dbName)
            console.log('Connected success')
        })
    }

    static close() {
        this.client.close()
        this.client = null;
        this.dbInstance = null;
    }

    /**
     * 添加数据
     * @param {String} doc 集合
     * @param {Object|Array} insertData 添加数据
     */
    static insert(doc, insertData) {
        const db = this.getInstance();
        const collection = db.collection(doc);
        return new Promise((resolve, reject) => {
            collection.insert(insertData, (err, result) => {
                if (err) {
                    reject(err)
                }
                resolve(result)
            })
        })
    }

    /**
     * 查找数据
     * @param {String} doc 集合
     * @param {Object|Array} insertData 添加数据
     */
    static find(doc, query) {
        const db = this.getInstance();
        const collection = db.collection(doc);
        return new Promise((resolve, reject) => {
            collection.find(query).toArray((err, result) => {
                if (err) {
                    reject(err)
                }
                resolve(result)
            })
        })
    }

}

module.exports = DB;
```

#### 数据库使用的账户

```
> db.getUser('appadmin')
{
	"_id" : "temp.appadmin",
	"userId" : UUID("3fa630d6-6bfa-47d3-a224-4e8fb50e8371"),
	"user" : "appadmin",
	"db" : "temp",
	"roles" : [
		{
			"role" : "readWrite",
			"db" : "temp"
		}
	],
	"mechanisms" : [
		"SCRAM-SHA-1",
		"SCRAM-SHA-256"
	]
}
```

#### 注意事项：

1. 连接数据的时候，如果用的帐号仅仅是针对特定数据库的，记住配置`authSource`字段，说明是连接的是那个数据库，否则会报 `MongoError: Authentication failed` 错误
2. 将 `useNewUrlParser` 和 `useUnifiedTopology` 显示设置为 true，解决警报信息
3. 解决MongoDB出现`Warning: no saslprep library specified. Passwords will not be sanitized`问题出现的在于mongodb-core依赖包中缺少saslprep。通过`yarn add saslprep` 实现消除警报

