---
published: true
key: 20180709
tags: YII
---
## 问题描述
项目后台要修改留存规则，然后统计任务就无效了
查看日志 module not inserted due to validation error .
大致意思是说插入验证失败
## 解决思路

<!--more-->

因为这个是定时任务，在console下面执行的，所以我们安装debugtoolbar扩展暂时发挥不了他的用处，那么只好在config/main.php里添加日志级别了
找到log 在levels里面添加 trace、info,如下

``` 
return [
    'id' => 'app-console',
    'basePath' => dirname(__DIR__),
    'bootstrap' => ['log'],
    'controllerNamespace' => 'console\controllers',
    'components' => [
        'log' => [
            'targets' => [
                [
                    'class' => 'yii\log\FileTarget',
                    'levels' => [ '**trace**' , '**info**' , 'error', 'warning'],
                ],
            ],
        ],
    ],
    'params' => $params,
];
```

然后就可以在application.log里面查看日志，找到对应语句进行修改。具体是修改数据、还是修改规则看自己需要。
## 输入验证
一般来说，程序猿永远不应该信任常最终用户直接接受到的数据，并且使用它们之前始终先验证其可靠性。

要给 model 填充其所需的用户输入数据，你可以调用 yii\base\Model::validate() 方法验证它们。该方法会返回一个布尔值，指明是否通过验证。若没有通过，你能通过 yii\base\Model::$errors 属性获取相应的报错信息。比如，

```
if ($model->validate())
{
      $model->save(false);//see the false
} else
{
      $errors = $model->errors;
}
```
        
## 声明规则（Rules）
要想让 validate() 方法生效，你需要声明与验证模型特性相关的验证规则，在 Model层添加 rules 规则，如果是用 gii 生成的 Model ,它会根据表结构自动生成相应的规则，你只要修改成对应需要的就可以。
