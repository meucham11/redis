# redis
파이썬3으로 redis 연결, 조회하기


## 필요한 패키지
```python3
import redis
import json
import pandas as pd
import numpy as np
```

## 기본 세팅 및 커넥팅
```python3
model_num = "~~"
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
```

## 키 조회 및 데이터 조회
```python3
# 모델 안에 들어있는 전체 키 조회
# 출력 결과는 리스트
allkey=[allkey for allkey in r.scan_iter()]
 
# ABCD: 으로 시작하는 모든 키를 검색하기 위해서는
hstr_keys=[keys for keys in r.scan_iter(match='ABCD:*')]

#######################################################
```


## 찾은 키에 대해서 테이터 조회.
```
## get 방식과 zrange 방식이 있다. 여기서는 zrange만 다룬다.
## 출력 결과는 딕셔너리가 ''으로 감싸져 있는 json형태이며 리스트로 여러 '딕셔너리'가 들어있다. '' 때문에 json.loads(x)를 적절히 이용한다.
''' 
[
 '{"data":
 	{"rersult": "{\\"Time\\":{\\"0\\":\\"2021-12-08 10:30:30\\",\\"1\\":\\"2021-12-08 10:30:00\\"},
		     {"item" : "{\\"0\\":\\"A\\",\\"1\\":\\"B\\"}},
		     {"value" : "{\\"0\\":\\"123\\",\\"1\\":\\"456\\"}},
	}
   "request_time":"2021*-12-08 09:25:00"
 }',
 '{"data":
 	{"result": "{\\"Time\\":{\\"0\\":\\"2021-12-08 10:40:00\\",\\"1\\":\\"2021-12-08 10:40:00\\"},
		     {"item" : "{\\"0\\":\\"A\\",\\"1\\":\\"B\\"}},
		     {"value" : "{\\"0\\":\\"789\\",\\"1\\":\\"159\\"}},
	}
   "request_time":"2021-12-08 09:25:00"
 }',
 .
 .
 .
]
'''
```

```python3
# zrange로 데이터 열어보기

## 아래 코드를 실행하면 위에 예시처럼 출력이 되기 때문에 json.loads 처리를 해주어야 한다.
## 적재되어있는 구조에 따라서 섞어주면 된다.
# r.zrange("ABCD:~~",start_index,end_index)
data_redis = r.zrange("ABCD:~~",-1800,-1)  ## 목록 뒤쪽 1800개


####
## data 속에 result 속에 Time과 item과 value를 얻고자 한다. 
def cleanser(x):
	try:  ## try문을 쓴 이유는 에러가 담겨져 있을 때는 json.loads가 작동하지 않기 때문에. 에러 데이터는 버리기로 했기 때문에.
		return json.loads(json.loads(x)["data"]["result"])
	except:
		pass


## 하나의 리스트 안에 여러개의 json형식의 데이터가 들어 있기 때문에
result = list(map(cleanser,data_redis))

# result 결과
'''
[
{'Time':{'0':'2021-12-08 10:30:00,
         '1':'2021-12-08 10:30:00},
 'Item':{'0':'A',
 	 '1':'B'}
 'value':{'0':'123',
 	  '1':'456'
 }
},
{'Time':{'0':'2021-12-08 10:40:00,
         '1':'2021-12-08 10:40:00},
 'Item':{'0':'A',
 	 '1':'B'},
 'value':{'0':'789',
 	  '1':'159'
},
.
.
.
]
'''
```


## 데이터 프레임으로 변환
```python3
newlist = [x for x in result if pd.isnull(x)==False]
dfnewlist = pd.DataFrame(newlist)
## 결과는 Time, Item, value가 필드명으로 올라가고  각 

```













































































































