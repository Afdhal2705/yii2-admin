Yii2 Admin
===============
Simple RBAC Manager for Yii 2.0. Modified of [yii2-admin](https://github.com/dickyermawan/yii2-admin)

Note: This extension is modified according to my needs

[![Latest Stable Version](https://poser.pugx.org/dickyermawan/yii2-admin/v/stable)](https://packagist.org/packages/dickyermawan/yii2-admin) [![Total Downloads](https://poser.pugx.org/dickyermawan/yii2-admin/downloads)](https://packagist.org/packages/dickyermawan/yii2-admin) [![Latest Unstable Version](https://poser.pugx.org/dickyermawan/yii2-admin/v/unstable)](https://packagist.org/packages/dickyermawan/yii2-admin) [![License](https://poser.pugx.org/dickyermawan/yii2-admin/license)](https://packagist.org/packages/dickyermawan/yii2-admin)

Attention
---------
Before you install and use this extension, then make sure that your application has been using the login authentication to the database. especially for yii basic template. Because without it, this extension will produce error and useless.

Installation
------------

The preferred way to install this extension is through [composer](http://getcomposer.org/download/).

Either run

```
php composer.phar require --prefer-dist dickyermawan/yii2-admin "*"
```

or add

```
"dickyermawan/yii2-admin": "*"
```

to the require section of your `composer.json` file.


Configuration
-------------

Once the extension is installed, simply use it in your code by  :

in config
```php
'as access' => [
     'class' => '\dickyermawan\admin\components\AccessControl',
	 'allowActions' => [
		// add wildcard allowed action here!
		'site/*',
		'debug/*',
		'admin/*', // only in dev mode
	],
],
...,
'modules' => [
	'admin' => [
		'class' => '\dickyermawan\admin\Module',
	],
	...
],
'components' => [
	'authManager' => [
		'class' => 'yii\rbac\DbManager', // only support DbManager
	],
],
```

You should migrate :

```yii migrate --migrationPath=@yii/rbac/migrations```

```yii migrate --migrationPath=@dickyermawan/admin/migrations```


Usage
-----

This RBAC manager have three main page, they are:

### Route
```
http://localhost/path/to/index.php?r=admin/route
```

### Role
```
http://localhost/path/to/index.php?r=admin/role
```

### User
```
http://localhost/path/to/index.php?r=admin/user
```

Implementation on Widgets
-------------------------

### Example dynamic button
It is used for checking if route right to access
```php
if ((Admin::checkRoute($this->context->id.'/create'))){
    echo Html::a('Create Note', ['create'], ['class' => 'btn btn-success']);
}

// in other case maybe You want ensure same of route so You can add parameter strict true
if ((Admin::checkRoute($this->context->id.'/create',true))){
```

### Example dynamic menu
It is is used for filtering right access menu
```php
use dickyermawan\admin\components\Admin;
$menuItems = [
    ['label' => 'Home', 'url' => ['/site/index']],
    ['label' => 'About', 'url' => ['/site/about']],
    ['label' => 'Contact', 'url' => ['/site/contact']],
];

if (\Yii::$app->user->isGuest){
    $menuItems[] = ['label' => 'Login', 'url' => ['/site/login']];
}
else{
    $menuItems[] = ['label' => 'App', 'items' => [
        ['label' => 'Category', 'url' => ['/category/index']],
        ['label' => 'Product', 'url' => ['/product/index']],
        ['label' => 'Cart', 'url' => ['/cart/index']],
    ]];
    $menuItems[] = [
        'label' => 'Logout (' . \Yii::$app->user->identity->username . ')',
        'url' => ['/site/logout'],
        'linkOptions' => ['data-method' => 'post']
    ];
}

$menuItems = Admin::filterMenu($menuItems);
// in other case maybe You want ensure same of route so You can add parameter strict true
// $menuItems = Admin::filterMenu($menuItems,true); 

echo Nav::widget([
    'options' => ['class' => 'navbar-nav navbar-right'],
    'items' => $menuItems,
]);
```
### Example dynamic action column template
It is used for filtering template of Gridview Action Column
```php
use dickyermawan\admin\components\Admin;
echo GridView::widget([
    'dataProvider' => $dataProvider,
    'filterModel' => $searchModel,
    'columns' => [
        ...,
        [
          'class' => 'yii\grid\ActionColumn',
          'template' => Admin::filterActionColumn([
              'update','delete','download'
          ],$this->context->route),
          ...
        ]
    ]
]);
```
