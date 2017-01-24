
![Middleware](https://dl.dropboxusercontent.com/u/3077539/_linked/f3-middleware.png)

## Middleware Router

This is a middleware router for PHP Fat-Free-Framework, version 3.x

It's based on the F3 core router, that can be called independently before or after the main routing cycle.
This can be useful if you want to hook into a group of other routes and want to do something right before processing the main route handler.


### Installation


- Method 1: use composer composer require ikkez/f3-middleware

- Method 2: copy the `middleware.php` file into your F3 `lib/` directory or another directory that is known to the [AUTOLOADER](https://fatfreeframework.com/quick-reference#AUTOLOAD)

### Usage Samples

```php
$f3 = require('lib/base.php');

// imagine you have some admin routes
$f3->route('GET|POST /admin','Controller\Admin->login');
// and these actions should be protected
$f3->route('GET|POST /admin/@action','Controller\Admin->@action');
$f3->route('GET|POST /admin/@action/@type','Controller\Admin->@action');
$f3->route('PUT /admin/upload','Controller\Files->upload');

// so just add a global pre-route to all at once
\Middleware::instance()->before('GET|POST /admin/*', function(\Base $f3, $params, $alias) {
	// do auth checks
});

\Middleware::instance()->run();
$f3->run();
```

Of course you could also use the `beforeroute` and `afterroute` events in your controller to add that auth check functionality. But in case your controller structure isn't ready yet for easy implementation or you have things you strictly want to separate from your controllers, like settings. Here the Middleware Router will aid you.

```php
// enable the CORS settings only for your API routes:
\Middleware::instance()->before('GET|HEAD|POST|PUT|OPTIONS /api/*', function(\Base $f3) {
	$f3->set('CORS.origin','*');
});
```

You can also create additional middleware wrappers on other events:

```php
$mw = \Middleware::instance();
$mw->on('limit',['GET @v1: /api/v1/*','GET @v2: /api/v2/*'], function($f3,$args,$alias) {
	// do api usage limit checks
	return false;
});

if ($mw->run('limit')) {
	// all good, continue
}else{
	// API limit reached
}
```

## License

You are allowed to use this plugin under the terms of the GNU General Public License version 3 or later.

Copyright (C) 2017 Christian Knuth (ikkez)