# laravel-searchable

[![Software License](https://img.shields.io/badge/license-MIT-brightgreen.svg?style=flat-square)](LICENSE.md)
[![Latest Version on Packagist](https://img.shields.io/packagist/v/fynduck/laravel-searchable.svg?style=flat-square)](https://packagist.org/packages/fynduck/laravel-searchable)
[![Total Downloads](https://img.shields.io/packagist/dt/fynduck/laravel-searchable.svg?style=flat-square)](https://packagist.org/packages/fynduck/laravel-searchable)

## Install
`composer require fynduck/laravel-searchable`

## Usage
```php
use Fynduck\LaravelSearchable\src\Searchable;

class User extends \Eloquent
{
    use Searchable;

     /**
     * Searchable rules.
     * Columns and their priority in search results.
     * Columns with higher values are more important.
     * Columns with equal values have equal importance.
     * @var array
     * @return array
     */
    protected function toSearchableArray()
    {
        return [
            'columns' => [
                'name'  => 10,
                'email' => 5,
            ],
             'joins' => [
                 'posts' => ['users.id','posts.user_id'],
             ],
        ];
    }

    /**
     * Select fields
     * @return array
     */
    public function selectFields()
    {
        return [
            'users.name',
            'users.email'
        ];
    }
    
    public function posts()
    {
        return $this->hasMany('Post');
    }

}
```

Now you can search your model.

```php
// Simple search
$users = User::search($query)->get();

// Search and get relations
// It will not get the relations if you don't do this
$users = User::search($query)
            ->with('posts')
            ->get();
```


## Search Paginated

As easy as laravel default queries

```php
// Search with relations and paginate
$users = User::search($query)
            ->with('posts')
            ->paginate(20);
```

## Mix queries

Search method is compatible with any eloquent method. You can do things like this:

```php
// Search only active users
$users = User::where('status', 'active')
            ->search($query)
            ->paginate(20);
```

## Custom Threshold

The default threshold for accepted relevance is the sum of all attribute relevance divided by 4.
To change this value you can pass in a second parameter to search() like so:

```php
// Search with lower relevance threshold
$users = User::where('status', 'active')
            ->search($query, 0)
            ->paginate(20);
```
The above, will return all users in order of relevance.

## Entire Text search

By default, multi-word search terms are split and Searchable searches for each word individually. Relevance plays a role in prioritizing matches that matched on multiple words. If you want to prioritize matches that include the multi-word search (thus, without splitting into words) you can enable full text search by setting the third value to true. Example:

```php
// Prioritize matches containing "John Doe" above matches containing only "John" or "Doe".
$users = User::search("John Doe", null, true)->get();
```

If you explicitly want to search for full text matches only, you can disable multi-word splitting by setting the fourth parameter to true.

```php
// Do not include matches that only matched "John" OR "Doe".
$users = User::search("John Doe", null, true, true)->get();
```

## Contributing
Please see [CONTRIBUTING](CONTRIBUTING.md) for details.

## Security
If you discover any security-related issues, please email DummyAuthorEmail instead of using the issue tracker.

## License
The MIT License (MIT). Please see [License File](/LICENSE.md) for more information.
