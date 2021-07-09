---
title: python_logging模块
date: 2020-01-12 10:33:03
tags:
  - python_logging模块
categories:
  - python
---
## logging -- 日志
### logging日志低配版
{% codeblock %}
'''import logging
logging.debug('我是调试')
logging.info('我是信息')
logging.warning('我是警告')
logging.error('我是错误')
logging.critical('我是危险')

默认是从warning开始记录
{% endcodeblock %}

### logging日志常用版
{% codeblock %}
import logging
logging.basicConfig(level=logging.DEBUG,
                    format='%(asctime)s %(filename)s [line:%(lineno)d] %(levelname)s %(message)s',
                    datefmt='%Y-%m-%d %H:%M:%S',
                    filename='test.log',
                    filemode='w')

dic = {"key":123}
logging.debug(dic)

num = 100
logging.info(f"用户当前余额:{num - 50}")

try:
    num = int(input("请输入数字:"))
except Exception as e:
    logging.warning("int将字符串转换报错了")
print("12334")

logging.error('我是错误')
logging.critical('我是危险')'''
import logging
logger = logging.getLogger() 
'''创建一个logger'''
fh = logging.FileHandler('test.log',mode="a",encoding='utf-8')   # 文件
ch = logging.StreamHandler()   # 屏幕
formatter = logging.Formatter('%(asctime)s - %(name)s - %(filename)s - [line:%(lineno)d] -  %(levelname)s - %(message)s')

logger.setLevel(logging.DEBUG) # 将屏幕和文件都是用以上格式

fh.setFormatter(formatter) # 设置记录级别

ch.setFormatter(formatter) # 使用自定义的格式化内容
logger.addHandler(fh) #logger对象可以添加多个fh和ch对象
logger.addHandler(ch)


logger.debug('logger debug message')
logger.info('logger info message')
logger.warning('logger warning message')
logger.error('logger error message')
logger.critical('logger critical message')
{% endcodeblock %}

### logging日志高配版
{% codeblock %}
"""
logging配置
"""

import os
import logging.config
"""定义三种日志输出格式 开始"""

standard_format = '[%(asctime)s][%(threadName)s:%(thread)d][task_id:%(name)s][%(filename)s:%(lineno)d]' \
                  '[%(levelname)s][%(message)s]' #其中name为getlogger指定的名字

simple_format = '在 %(asctime)s %(message)s'

id_simple_format = '[%(levelname)s][%(asctime)s] %(message)s'


"""log文件的全路径"""
logfile_path = 'all2.log'

"""log配置字典"""
LOGGING_DIC = {
    'version': 1,
    'disable_existing_loggers': False,
    'formatters': {
        'standard': {
            'format': standard_format
        },
        'simple': {
            'format': simple_format
        },
    },
    'filters': {},
    'handlers': {
        #打印到终端的日志
        'stream': {
            'level': 'DEBUG',
            'class': 'logging.StreamHandler',  # 打印到屏幕
            'formatter': 'simple'
        },
        #打印到文件的日志,收集info及以上的日志
        'file': {
            'level': 'DEBUG',
            'class': 'logging.handlers.RotatingFileHandler',  # 保存到文件
            'formatter': 'standard',
            'filename': None,  # 日志文件
            'maxBytes': 1024*1024*1024,  # 日志大小 5M
            'backupCount': 5,
            'encoding': 'utf-8',  # 日志文件的编码，再也不用担心中文log乱码了
        },
    },
    'loggers': {
        #logging.getLogger(__name__)拿到的logger配置
        '': {
            'handlers': ['stream', 'file'],  # 这里把上面定义的两个handler都加上，即log数据既写入文件又打印到屏幕
            'level': 'DEBUG',
            'propagate': True,  # 向上（更高level的logger）传递
        },
    },
}

def get_logger():
    path = r'F:\s24\day21\liye.log'
    LOGGING_DIC['handlers']['file']['filename'] = path
    logging.config.dictConfig(LOGGING_DIC)  # 导入上面定义的logging配置
    logger = logging.getLogger(__name__)  # 生成一个log实例
    return logger

def save():
    logger = get_logger()
    logger.info(f'{} 存入300元')  # 记录该文件的运行状态

save()
{% endcodeblock %}

