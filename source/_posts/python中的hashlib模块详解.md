---
title: python中的hashlib模块详解
date: 2020-09-11 22:31:36
tags:
  - python_hashlib模块
categories:
  - python
---
###  Python中hashlib模块详解

####  文章目录
1. hashlib的简介
2. hashlib的使用
   + 常用属性
   + 常用方法
   + 使用示例
3. hashlib的特点
4. 代码实操
   + 举例子
   + 应用场景案例

#### 一、hashlib的简介
hashlib 是一个提供了一些流行的hash(摘要)算法的Python标准库．其中所包括的算法有 md5, sha1, sha224, sha256, sha384, 
sha512等什么是摘要算法呢？摘要算法又称哈希算法、散列算法。它通过一个函数，把任意长度的数据转换为一个
长度固定的数据串（通常用16进制的字符串表示）。更多请看：hashlib — 安全哈希与消息摘要

####  二、hashlib的使用
本文以hashlib中MD5算法为例，其他的sha224、sha256算法用法和MD5基本一致。如果想看其他
案例可以参考我的博文：用python实现MD5、sha256、sha384、sha512、base64加密

##### 1、常用属性
{% codeblock %}
hashlib.algorithms
#列出所有加密算法

h.digest_size
#产生的散列字节大小。

h.block_size
#哈希内部块的大小
{% endcodeblock %}

##### 2、常用方法
{% codeblock %}
hash.new([arg]) # 创建指定加密模式的hash对象

hash.update(arg)  # 更新哈希对象以字符串参数。如果同一个hash对象重复调用该方法，m.update(a); m.update(b) 等价于 m.update(a+b)

hash.digest()  # 返回摘要，作为二进制数据字符串值。

hash.hexdigest() # 返回摘要，作为十六进制数据字符串值

hash.copy() # 复制
{% endcodeblock %}

##### 3、使用示例
{% codeblock %}
import hashlib # MD5 的使用

def jm_md5(password):
    m = hashlib.md5()  # 构建MD5对象
    m.update(password.encode(encoding='utf-8')) #设置编码格式 并将字符串添加到MD5对象中
    password_md5 = m.hexdigest()  # hexdigest()将加密字符串 生成十六进制数据字符串值
    return password, password_md5
    
g = jm_md5('123456')
print(g)
{% endcodeblock %}

#### 三、hashlib的特点

+ 1、摘要算法在很多地方都有广泛的应用。
+ 2、要注意摘要算法不是加密算法，不能用于加密（因为无法通过摘要反推明文），只能用于防篡改。
+ 3、它的单向计算特性决定了可以在不存储明文口令的情况下验证用户口令。
+ 注意：一个优秀的 hash 算法，将能实现：
正向快速：给定明文和 hash 算法，在有限时间和有限资源内能计算出 hash 值。
逆向困难：给定（若干） hash 值，在有限时间内很难（基本不可能）逆推出明文。
输入敏感：原始输入信息修改一点信息，产生的 hash 值看起来应该都有很大不同。
冲突避免：很难找到两段内容不同的明文，使得它们的 hash 值一致（发生冲突）。即对于任意两个不同的数据块，
其hash值相同的可能性极小；对于一个给定的数据块，找到和它hash值相同的数据块极为困难

#### 四、代码实操

可以找找其他篇博文：用python实现MD5、sha256、sha384、sha512、base64加密
+ 1.举例子
{% codeblock %}
import hashlib

"""一、在构建对象直接插入加密字符串"""
m1 = hashlib.md5('hello python'.encode(encoding='utf-8'))  # 构建MD5对象
print(m1.hexdigest())   # 结果为： e53024684c9be1dd3f6114ecc8bbdddc


"""二、通过update方法 往MD5对象中增加字符串参数"""
m2 = hashlib.md5()  # 构建MD5对象
m2.update('hello python'.encode(encoding='utf-8')) # 设置编码格式 并将字符串添加到MD5对象中
password_md5 = m2.hexdigest()
print(m2.hexdigest())   # 结果为 e53024684c9be1dd3f6114ecc8bbdddc


"""三、当数据量过过大时，可以分块摘要，例如："""
m3 = hashlib.md5()
m3.update("hello ".encode("utf-8"))  # 注意：分块是空格也要保持一致
m3.update("python".encode("utf-8"))
print(m3.hexdigest())  # 结果为：e53024684c9be1dd3f6114ecc8bbdddc

"""MD5是最常见的摘要算法，速度很快，生成结果是固定的128 bit字节，通常用一个32位的16进制字符串表示。"""
{% endcodeblock %}
三种方式，往构造的MD5对象中传参，只要传参的字符串一致，最后生成的结果是一样的。
这说明hash算法就像一座工厂，工厂接收你送来的原材料（可以用m.update()为工厂运送原材料），
经过加工返回的产品就是hash值。这也是摘要算法的一个特点，它不是加密算法，不能用于
加密（因为无法通过摘要反推明文），只能用于防篡改
+ 2、应用场景案例
{% codeblock %}
import hashlib

USER_LIST = []
def pwd_Md5(password):
    password = password+'hello python'  # 字符串混淆加盐，可以设置更复杂一点
    return hashlib.md5(password.encode("utf-8")).hexdigest()


def register():
    print('**************用户注册**************')
    while True:
        user = input('请输入用户名:')
        if user.isalpha():
            break
    while True:
        password1 = input('请输入密码>>>:').strip()
        passwprd2 = input('请重复密码>>>：').strip()
        if password1 == passwprd2:
            password = pwd_Md5(password1)  # 将密码进行Md5加密
            break
        else:
            print('密码不正确，重新输入！')
    temp = {'username':user,'password':password}
    USER_LIST.append(temp)


def login():
    print('**************用户登陆**************')
    user = input('请输入用户名:')
    pwd = input('请输入密码:')

    for item in USER_LIST:  
        if item['username'] == user and item['password'] == pwd_Md5(pwd):
            return True

if __name__=='__main__':

    register()
    if login():
        print('登陆成功')
    else:
        print('登陆失败')


结果：
**************用户注册**************
请输入用户名:wuhan
请输入密码:123456
**************用户登陆**************
请输入用户名:小马过河
请输入密码:123456
登陆成功

{% endcodeblock %}

代码分析：
1、用户登录需要使用密码，密码一定要加密，保证用户的信息安全。
　　1）加密可以使用hashlib模块进行加密。
　　2）加密可以写成加密函数，方便多处调用
　　3）提高密码解密的复杂性，代码中多加字符串。（加密算法虽然依然非常厉害，但是也存在缺陷，
即：通过撞库可以反解。所以，有必要对加密密码进行加盐。）
