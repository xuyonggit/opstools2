# 运维工具库
### 安装
pip install git+https://github.com/xuyonggit/opstools2.git
---
### 封装mysql
```python
#!/usr/bin/env python3
from opstools2.Base.mysql_api import BaseMysql


class TestMysql(BaseMysql):
    def init_conf(self):
        self.MYSQL_HOST = ''
        self.MYSQL_USER = ''
        self.MYSQL_DATABASE = ''
        self.MYSQL_PW = ''


if __name__ == '__main__':
    Test = TestMysql()
    # 查询操作
    with Test as T:
        print(T.query_sql("select * from dag"))
    # 写入操作以及更新操作
    with Test as T:
        T.do_sql("update | insert ...")
```
---
### 封装企业微信接口
```python
from opstools2.wechat.wechat_api import WechatApi

GLOBAL_WECHAR_CONFIG = {
        'NAME': '金山小额',

        #企业的id，在管理端->"我的企业" 可以看到
        'CORP_ID': '',

        #应用列表 可以配置多个应用
        'APP_LIST': {
            'warning_robot': {           #报警机器人
                'APP_ID': '',           #APPID
                'APP_SECRET': '',       #APP密钥
                'switch': 'on',          #是否开启
            },
        }
    }

if __name__ == '__main__':
    we = WechatApi(GLOBAL_WECHAR_CONFIG)
    status, res = we.apps['warning_robot'].send(
        msg_type='text',
        to_users_list=['小明'],
        msg_string='报警test'
    )
    if not status:
        print(res)
```
---
### 封装发送邮件接口
```python
# -*- coding: utf-8 -*-
from opstools2.email.tools.Coreapi import EmailApi
CONFIG = {
    # 组
    'test1': {
        'name': '',         # 组名 即邮件主题
        'user': '',         # 发件人
        'alias': '',        # 发件人别名 可为空
        'password': '',     # 发件人密码
        'smtp': '',         # smtp服务器
        'port': 25,          # 端口，决定是否使用ssl加密发送25 非加密 465 加密，系统自动判定
        'to_list': ['xuyong1@wps.cn'],      # 收件人列表
        'cc_list': []       # 抄送列表
    }
}

if __name__ == '__main__':
    ea = EmailApi(CONFIG).apps['test1']
    # 添加文本
    ea.add_str('lalala')
    # 添加加粗文本
    ea.add_strong_str("我是加粗部分")
    # 添加图片
    ea.add_image("C:\\Users\\xu's\\PycharmProjects\\opstools2\\examples\\123.jpg")
    # 添加表格
    # 列表中得第一个列表为表头，不需要表头可留空，
    # 可选参数：title 表格标题
    ea.add_table([['姓名', '年龄', '性别'], ['xuyong1', 25, '男']], title='lalala')
    # 添加附件
    ea.add_attr("C:\\Users\\xu's\\PycharmProjects\\opstools2\\examples\\wechat.py")
    status, res = ea.send_email()
    print(res)
```
###### 效果图
![avatar](https://github.com/xuyonggit/opstools/blob/master/examples/image/emailapi.png)
---
### 获取随机密码
```python
# -*- coding: utf-8 -*-
from opstools2.Base.password import MakePassword


if __name__ == '__main__':
    # 生成简单密码
    print(MakePassword().getsamplepassword())
    # 生成普通密码
    print(MakePassword().getpassword())
    # 生成困难密码
    print(MakePassword().gethardpassword())

```
result:
```text
4kz709fo
r48FY7qs6JCo
ibq3]T1z<8I>zAv
```
---
### 封装redis批量操作keys
根据模糊条件批量查询或者删除redis中的key  
默认不允许查询：* 即所有key  
**注意：该工具不建议在生产环境实施，如自行在生产环境操作，造成任何损失，作者不承担任何责任**
```python
# -*- coding: utf-8 -*-
from opstools2.Redis.Api import redisops

if __name__ == '__main__':
    R = redisops(host='', port=6379, password='')   # password 可选
    
    # 增加用户自定义拒绝keys正则格式
    R.deny(['^[a-z]'])

    # 模糊批量查询keys
    _count, _data = R.getkeys('session-*')
    print(_count, _data)        # 数据量特别大时不建议操作返回值：_data
    
    # 模糊批量删除keys
    R.delkeys('session-*', deltime=0.2)     # deltime 可选，删除间隔（单位：s)，默认0.1s

```