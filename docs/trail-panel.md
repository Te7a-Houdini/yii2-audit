---
layout: default
title: Trail Panel
permalink: /docs/trail-panel/
---

# Trail Panel

If you want database changes to be logged, you have to add the `AuditTrailBehavior` to the models you want to log.

## Basic Configuration

```php
<?php
/**
 * Post
 * @mixin AuditTrailBehavior
 */
class Post extends \yii\db\ActiveRecord
{
    public function behaviors()
    {
        return [
            'bedezign\yii2\audit\AuditTrailBehavior'
        ];
    }
}
```

## Advanced Configuration

```php
<?php
/**
 * Post
 * @mixin AuditTrailBehavior
 */
class Post extends \yii\db\ActiveRecord
{
    public function behaviors()
    {
        return [
            'AuditTrailBehavior' => [
                'class' => 'bedezign\yii2\audit\AuditTrailBehavior',
                // Array with fields to save. You don't need to configure both `allowed` and `ignored`
                'allowed' => ['some_field'],
                // Array with fields to ignore. You don't need to configure both `allowed` and `ignored`
                'ignored' => ['another_field'],
                // Array with classes to ignore
                'ignoredClasses' => ['common\models\Model'],
                // Is the behavior is active or not
                'active' => true,
                // Date format to use in stamp - set to "Y-m-d H:i:s" for datetime or "U" for timestamp
                'dateFormat' => 'Y-m-d H:i:s',
                //Change the oldValue and newValue values to the related model with custom return fields
                //For ex:- let's assume that tag have many posts and post belongs to one tag
                // so this will replace the ids with related names 
                'override' => [
                    'tag_id' => ['tableName' => 'tag' , 'searchField' => 'id' , 'returnField' => 'name']
                ]
            ]
        ];
    }
}
```


## Version Control

### Undeleting

```php
<?php
use bedezign\yii2\audit\components\Version;

$post_id = 1;
Post::findOne($post_id)->delete();
// ... time passes ...
$post = Version::find(Post::className(), $post_id);
$post->save();
```

### Rolling Back to Last Version

```php
<?php
use bedezign\yii2\audit\components\Version;

$post = Post::findOne(1);
$post->title = 'updated post title';
$post->save();
// ... time passes ...
$post = Version::find($post->className(), $post->id);
$post->save();
```

### Rolling Back a Single Field to Last Version

```php
<?php
use bedezign\yii2\audit\components\Version;

// load a post
$post = Post::findOne(1);

// load the last revision
$oldPost = Version::find($post->className(), $post->id);

// save the model
$post->title = $oldPost->title;
$post->save();
```

### Rolling Back to Any Version

```php
<?php
use bedezign\yii2\audit\components\Version;

// get all versions
$versions = Version::versions($post->className(), $post->id));
// get the last version
$version = Version::lastVersion($post->className(), $post->id));
// load model at any version
$post = Version::find($post->className(), $post->id, $version);
// save the model
$post->save();
```
