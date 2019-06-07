
### Questions
* breadth vs depth recursive functions
* wording may have been switched in descriptions

### Objectives
YWBAT 
* parse a json for various informatoin
* parse jsons and load them into dataframes
* build a recursive function

### Outline
* questions
* solve some recursive stuff
* load in a json from a tweet
* parse the json
* use some tools online to see the structure
* use a loop to parse jsons and load them into a df


```python
import pandas as pd
import numpy as np

from textblob import TextBlob

from pprint import pprint

from pymongo import MongoClient

import matplotlib.pyplot as plt
```


```python
# triangle numbers
# t1 = 1
# t2 = 1 + 2 = 3
# t3 = 1 + 2 + 3 = 6
# t4 = 1 + 2 + 3 + 4 = 10
# t5 = 1 + ... + 5 = 15
# t6 = 1 + ... + 6 = 21

def triangle(n):
    s = 0
    for i in range(1, n + 1):
        s += i
    return s


def triangle_rec(n):
    if n == 1: 
        return 1
    return n + triangle_rec(n - 1)
```


```python
%%time
triangle_rec(1000)
```

    CPU times: user 375 µs, sys: 1e+03 ns, total: 376 µs
    Wall time: 385 µs





    500500




```python
%%time
triangle(1000)
```

    CPU times: user 55 µs, sys: 1 µs, total: 56 µs
    Wall time: 60.1 µs





    500500




```python
# Fibonacci sequence
# F0 = 0
# F1 = 1
# F2 = 1
# F3 = 2
# F4 = 3
# F5 = 5
# 0, 1, 1, 2, 3, 5, 8, 13, 21, ...

def fib(n):
    if n == 0:
        return 0
    if n == 1:
        return 1
    if n == 2:
        return 1
    if n == 3:
        return 2
    return fib(n - 1) + fib(n - 2)
```


```python
fib(10)
```




    55




```python
# load in some tweets using mongodb
```


```python
client = MongoClient(host='localhost', port=27017)
```


```python
client.list_database_names()
```




    ['admin',
     'config',
     'local',
     'marchmadness',
     'music_tweets',
     'mydb',
     'new_db',
     'tweets']




```python
music_tweets = client["music_tweets"]
```


```python
music_tweets.list_collection_names()
```




    ['kendrickLamar']




```python
kdot = music_tweets["kendrickLamar"]
```


```python
# load this data into a dataframe
dlist = []

for res in kdot.find({}): # equivalent of a select * from kendrickLamar;
    d = {}
    d["retweet_count"] = res["retweet_count"]
    d["text"] = res["text"]
    d["reply_count"] = res["reply_count"]
    d["user"] = res["user"]
    d["user_friends"] = user["friends_count"]
    d["user_followers"] = user["followers_count"]
    d["user_screenname"] = user["screen_name"]
    d["user_description"] = user["description"]
    dlist.append(d)
```


```python
df = pd.DataFrame(dlist)
df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>reply_count</th>
      <th>retweet_count</th>
      <th>text</th>
      <th>user</th>
      <th>user_description</th>
      <th>user_followers</th>
      <th>user_friends</th>
      <th>user_screenname</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>0</td>
      <td>Now Playing: Tech N9ne f. Kendall Morgan, Kend...</td>
      <td>{'id': 776128881107558401, 'id_str': '77612888...</td>
      <td>7Six5 Live is a mobile streaming radio station...</td>
      <td>526</td>
      <td>201</td>
      <td>7Six5Live</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0</td>
      <td>0</td>
      <td>RT @Bj532x: امبي وايد حلوه 💔 https://t.co/2oDR...</td>
      <td>{'id': 2459509949, 'id_str': '2459509949', 'na...</td>
      <td>7Six5 Live is a mobile streaming radio station...</td>
      <td>526</td>
      <td>201</td>
      <td>7Six5Live</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0</td>
      <td>0</td>
      <td>@JayStephMD @kendricklamar Bitch can we win a ...</td>
      <td>{'id': 805611051169566720, 'id_str': '80561105...</td>
      <td>7Six5 Live is a mobile streaming radio station...</td>
      <td>526</td>
      <td>201</td>
      <td>7Six5Live</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0</td>
      <td>0</td>
      <td>Well this is trash cause 4:44 &amp;amp; u missed W...</td>
      <td>{'id': 4705257927, 'id_str': '4705257927', 'na...</td>
      <td>7Six5 Live is a mobile streaming radio station...</td>
      <td>526</td>
      <td>201</td>
      <td>7Six5Live</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0</td>
      <td>0</td>
      <td>RT @archivelyric: pray for me - the weeknd ft....</td>
      <td>{'id': 817135604605587456, 'id_str': '81713560...</td>
      <td>7Six5 Live is a mobile streaming radio station...</td>
      <td>526</td>
      <td>201</td>
      <td>7Six5Live</td>
    </tr>
  </tbody>
</table>
</div>




```python
polarity_scores = []
for text in df.text:
    blob = TextBlob(text)
    polarity, subjectivity = blob.sentiment
    polarity_scores.append(polarity)
```


```python
df["tweet_polarity"] = polarity_scores
df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>reply_count</th>
      <th>retweet_count</th>
      <th>text</th>
      <th>user</th>
      <th>user_description</th>
      <th>user_followers</th>
      <th>user_friends</th>
      <th>user_screenname</th>
      <th>tweet_polarity</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>0</td>
      <td>Now Playing: Tech N9ne f. Kendall Morgan, Kend...</td>
      <td>{'id': 776128881107558401, 'id_str': '77612888...</td>
      <td>7Six5 Live is a mobile streaming radio station...</td>
      <td>526</td>
      <td>201</td>
      <td>7Six5Live</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0</td>
      <td>0</td>
      <td>RT @Bj532x: امبي وايد حلوه 💔 https://t.co/2oDR...</td>
      <td>{'id': 2459509949, 'id_str': '2459509949', 'na...</td>
      <td>7Six5 Live is a mobile streaming radio station...</td>
      <td>526</td>
      <td>201</td>
      <td>7Six5Live</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0</td>
      <td>0</td>
      <td>@JayStephMD @kendricklamar Bitch can we win a ...</td>
      <td>{'id': 805611051169566720, 'id_str': '80561105...</td>
      <td>7Six5 Live is a mobile streaming radio station...</td>
      <td>526</td>
      <td>201</td>
      <td>7Six5Live</td>
      <td>0.2</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0</td>
      <td>0</td>
      <td>Well this is trash cause 4:44 &amp;amp; u missed W...</td>
      <td>{'id': 4705257927, 'id_str': '4705257927', 'na...</td>
      <td>7Six5 Live is a mobile streaming radio station...</td>
      <td>526</td>
      <td>201</td>
      <td>7Six5Live</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0</td>
      <td>0</td>
      <td>RT @archivelyric: pray for me - the weeknd ft....</td>
      <td>{'id': 817135604605587456, 'id_str': '81713560...</td>
      <td>7Six5 Live is a mobile streaming radio station...</td>
      <td>526</td>
      <td>201</td>
      <td>7Six5Live</td>
      <td>0.0</td>
    </tr>
  </tbody>
</table>
</div>




```python

```

### Assessment
