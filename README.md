# Redis TEST
Redis test

▪️クライアント用ラッシュテストコード
```python
import redis

redisServer = 'redistest01'
redisPort = '6379'

conn = redis.StrictRedis(host=redisServer, port=redisPort, db=0, decode_responses=True)

for i in range(1000000):
    try:
        conn.set('nextkey'+str(i), 'val'+str(i))
    except:
        print(i)
        break
```

▪️構成
client --- redistest01(master) --- redistest02(slave)

OS: AmazonLinux2
インスタンスタイプ: t2.micro

▪️redis設定
デフォルトにslaveof設定のみ。

▪️テスト１(性能)
1,000,000件/約５分３０秒 --> 3,100件/秒

▪️テスト２（障害）
ラッシュ中 master redisのプロセスkill 
```shell
kill <pid>
```
master、slave共に欠損なし。

▪️テスト３（障害）
ラッシュ中 master OS shutdown
```shell
shutdown -h now
```
master、slave共に欠損なし。

▪️テスト４(障害)
ラッシュ中 master AWSコンソールで停止
master、slave共に欠損なし。

▪️テスト5(障害）
ラッシュ中 master KernelPanic
```shell
echo o > /proc/sysrq-trigger; halt -f
```
masterで欠損。

client: 9928 (9929の時にexception)
master: 222
slave: 9928
