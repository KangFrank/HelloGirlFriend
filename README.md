# 用 Python写一个爬虫脚本每天定时给多人
#此项目抄袭github上自带项目，非原创，仅作为个人娱乐

### 使用库
- [itchat][2] - 微信个人号接口
- [requests][3] - 网络请求库
- [beautifulsoup4][4] - 解析网页
- [APScheduler][5] - 定时任务

### 功能
定时给朋友发送每日天气、提醒、每日一句。

### 数据来源
- 每日一句和上面的大佬一样也是来自 [ONE●一个][6]
- 天气信息来自 [SOJSON][7] 



## 代码说明

### 目录结构

- city_dict.py ：城市对应编码字典
- config.yaml ：设置定时时间，微信名称等参数
- Weather.py：核心代码
- requirements.txt：需要安装的库
- run.py：项目运行类

### 核心代码

#### 1.定时任务。
每天 9：30 给朋友们开始给朋友发送内容。
```
# 定时任务
scheduler = BlockingScheduler()
# 每天9：30给朋友发送每日一句
# scheduler.add_job(start_today_info, 'cron', hour=9, minute=30)
scheduler.start()
```
*start_today_info* 是方法处理类。

#### 2.获取每日一句。
数据来源： [ONE●一个][6]
```
def get_dictum_info(self):
    '''
    获取格言信息（从『一个。one』获取信息 http://wufazhuce.com/）
    :return: str 一句格言或者短语
    '''
    print('获取格言信息..')
    user_url = 'http://wufazhuce.com/'
    resp = requests.get(user_url, headers=self.headers)
    soup_texts = BeautifulSoup(resp.text, 'lxml')
    # 『one -个』 中的每日一句
    every_msg = soup_texts.find_all('div', class_='fp-one-cita')[0].find('a').text
    return every_msg
```
#### 3. 获取今日天气 。
天气数据来源：[SOJSON][7]

```
def get_weather_info(self, city_code=''）：
    weather_url = f'http://t.weather.sojson.com/api/weather/city/{city_code}'
    resp = requests.get(url=weather_url)
    if resp.status_code == 200 and resp.json().get('status') == 200:
        weatherJson = resp.json()
        # 今日天气
        today_weather = weatherJson.get('data').get('forecast')[1]
```
city_code 城市对应 id。
[http://cdn.sojson.com/_city.json](http://cdn.sojson.com/_city.json)

#### 4. 登录微信并发送内容。
```
itchat.auto_login()
itchat.send(today_msg, toUserName=name_uuid)
```


## 项目运行

### 安装依赖

使用 pip install -r requirements.txt 安装所有依赖

### 参数配置
config.yaml
```
#每天的几点开始发送信息
alarm_timed: '9:30'
girlfriend_infos:
  -
    #微信昵称
    wechat_name: '古典'
    #地址
    city_name: '桂林'
    # 认识时间
    start_date: '2017-11-11'
    # 谁给你发送的
    sweet_words: '来自真诚的我。'

  #如果你有多个朋友需要发送，则参照这个样式，复制即可


### 开始运行
```
python run.py
```

## 最后
https://github.com/KangFrank/HelloGirlFriend/

  [1]: https://juejin.im/post/5c77c6bef265da2de6611cff
  [2]: https://github.com/littlecodersh/ItChat
  [3]: http://docs.python-requests.org/en/master/
  [4]: https://www.crummy.com/software/BeautifulSoup/bs4/doc/index.zh.html#
  [5]: https://apscheduler.readthedocs.io/en/latest/
  [6]: http://wufazhuce.com/
  [7]: https://www.sojson.com/blog/305.html
