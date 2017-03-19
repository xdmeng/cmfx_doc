# sp_get_breadcrumb()

> X2.2.0新增

```php
sp_get_breadcrumb($term_id)
```

###### 功能：
获取面包屑数据

###### 参数：
`$term_id`: 当前文章所在分类id,或者当前分类的id

###### 返回：
类型array,面包屑数据

###### 使用：
```php
$breadcrumb = sp_get_breadcrumb(3);
```
/*获取面包屑数据*/
```php
<php>
######/*获取面包屑数据*/
$breadcrumb=sp_get_breadcrumb($term['term_id']);
</php> 
<section class="">
<div class="container">
<ul class="breadcrumb">
<li><a href="__ROOT__">首页</a></li>
<foreach name="breadcrumb" item="vo">
<li><a href="{:leuu('portal/list/index',array('id'=>$vo['term_id']))}">>{$vo.name}</a></li>
</foreach>
<li><a href="{:leuu('portal/list/index',array('id'=>$term['term_id']))}">>{$term.name}</a></li>
<li class="active1">>您现在的位置</li>
</ul>
</div>
</section>
```
