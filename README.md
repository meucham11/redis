# redis
파이썬3으로 redis 연결, 조회하기

```python3
import redis
import json

model_num=’MDL20210143‘
HOST_URL = “~~”
USERNAME = “~~”
PASSWORD = “~~”
#커넥팅
r=redis.Redis(host = HOST_URL,
	port = ~,
	encoding=“utf-8”,
	decode_responses=True,
	ssl=True,
	username=USERNAME,
	password=PASSWORD)


# 모델 안에 들어있는 테이블 조회
allkey=[allkey for allkey in r.scan_iter()]
 -> 모든 목록들이 쭉 나온다. ex HSTR:MDL20210183
## HSTR: 으로 시작하는 테이블을 검색하기 위해서는
hstr_keys=[keys for keys in r.scan_iter(match=’HSTR:*’)]
## 찾은 목록에 대해서 테이터 조회.
## get 방식과 zrange 방식이 있다.

```
