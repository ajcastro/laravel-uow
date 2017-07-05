# laravel-uow
Laravel Unit of Work

### Introduction

Laravel developers are fond of using `$model->save()`, but when they have to deal with lots of models, see how many save queries which it will perform.
So I came up with the idea of creating  UOW, that will address this issue.

By using
```php
Uow::get()->user()->save($user);
Uow::get()->user()->save($anotherUser);
Uow::get()->user()->save($andAnotherUser);
```
this can result to only one insert (or update) query which is faster and more performance efficient

Same with:
```php
Uow::get()->user()->delete($user);
Uow::get()->user()->delete($anotherUser);
Uow::get()->user()->delete($andAnotherUser);
```

### Sample Usages

```php
$uow = Uow::get('default'); // or Uow::get(); defaults to 'default'

// By table
$uow->table('users')->insert($user);
$uow->table('users')->update($user);
$uow->table('users')->save($user);
$uow->table('users')->delete($user);

// By model
$uow->model(\App\User::class)->insert($user);
$uow->model(\App\User::class)->create($user);
$uow->model(\App\User::class)->save($user);
$uow->model(\App\User::class)->update($user);
$uow->model(\App\User::class)->delete($user);

$uow->commit(); // Always call commit() to flush and commit changes in database.
$uow->persist(); // alias of commit
// or 
Uow::get('default')->commit();

```


```php

class UsersController {

  function __construct() 
  {
    $this->user = Uow::get()->model(\App\User::class);
  }
  
  function store(Request $request)
  {
    $this->user->create($request->all());
    
    // can commit here in the controller, or create a middleware where you can commit it there
  }

}

```

### Register alias

```php
// Alias user can be registered like this
Uow::alias('user', \App\User::class);

// So it can be accessed like this
Uow::get()->user()->insert($user);
Uow::get()->user()->create($user);
Uow::get()->user()->update($user);
Uow::get()->user()->delete($user);

```

### Another uow name is another uow or database transaction.

```php

Uow::get('another_uow');
Uow::get('another_uow')->commit(); // only commits record changes happened in another_uow
```

### Using Model Helper Method

We can also attach a helper method to models, so we can do this:

```php
$user->uow()->save();
$user->uow()->delete();
```
