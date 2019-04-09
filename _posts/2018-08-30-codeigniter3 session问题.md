---
published: true
tags: PHP
key: 20180830
---
近期负责维护一个16年的老项目，需要迁移到我负责的服务上，PHP版本7.1。移植完成后打开看到登录页面，正高兴着发现系统登录不进去，看起来像是session问题，在本地环境PHP7.2测试是没问题的 。  


## 问题描述

观察http请求，发现http请求带的sessionID和相应的ID不匹配，每次服务都response一个新的sessionID。在框架入口文件加入 session_start(); 观察返回的sessionID并没有问题，但是框架内置的session就会出现问题，这是为什么呢?  
查看框架封装的session类，在 system/libraries/Session/Session.php:129 有如下代码  
```
if (isset($_COOKIE[$this->_config['cookie_name']])
    && (
        ! is_string($_COOKIE[$this->_config['cookie_name']])
        OR ! preg_match('/^[0-9a-f]{40}$/', $_COOKIE[$this->_config['cookie_name']])
    )
)
{
    unset($_COOKIE[$this->_config['cookie_name']]);
}
```  
这个会检查cookie的sessionID格式是否正确，刚好我现在的格式不正确（可以在看cookie值或者是session_id();查看）所以这个cookie的值被清理掉了，导致session值也取不出来
<!--more-->
## 解决方案
找到问题了就好解决了，查看官网上有关于session长度的设置  

`
** session.sid_length **  
session.sid_length allows you to specify the length of session ID string. Session ID length can be between 22 to 256. The default is 32. If you need compatibility you may specify 32, 40, etc.  
Longer session ID is harder to guess. At least 32 chars is recommended.Compatibility Note: Use 32 for session.hash_func=0 (MD5) and session.hash_bits_per_character=4,session.hash_func=1 (SHA1) and session.hash_bits_per_character=6. Use 26 for session.hash_func=0 (MD5) and session.hash_bits_per_character=5. Use 22 for session.hash_func=0 (MD5) andsession.hash_bits_per_character=6. You must configure INI values to have at least 128 bits in session ID. Do not forget set appropriate value to session.sid_bits_per_character, otherwise you will have weaker session ID.
`  

还有一个问题，如果有a-f以外的字符也是不行的（参考下面把正则改成自己相应的）  
  
`
session.sid_bits_per_character  
session.sid_per_character allows you to specify the number of bits in encoded session ID character. The possible values are '4' (0-9, a-f), '5' (0-9, a-v), and '6' (0-9, a-z, A-Z, "-", ","). The default is 4. The more bits results in stronger session ID. 5 is recommended value for most environments.
`  

回到codeigniter的GitHub查，发现这个问题是在一些早期版本的codeigniter 3，这是一个bug已经在他们的网站上报道。bug已经被修复，在最新版本的codeigniter 3中，这个问题不会发生。如果你已经在一个错误的版本codeigniter考虑替换系统文件夹与最新版本的一个  
参考连接 ：[Codeigniter 3 Session not working With PHP 7.1.4](https://stackoverflow.com/questions/43718961/codeigniter-3-session-not-working-with-php-7-1-4)
