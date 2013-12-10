# Seismo Node.js / Express.js client

## Create client and post events

This is a small example of `seismo-client` usage:

```js
var seismo = require('seismo-client');

// create analytics client, by providing app id
var events = seismo('my-web-app');

// call function, with the name of event
events('application started');
```

```js
// provide event id (optional, but suitable for fetching data)
events({id: 'app-start', event: 'application started'});
```

```js
// provide additional payload (optional, but usefull for sophisticated analysis)
events('application stated', {environment: process.env.NODE_ENV});
```

```jsby
// or ..
events({id: 'app-start', event: 'application started'}, {environment: process.env.NODE_ENV});

// provide callback (optional)
events('application started', function (err) {
	console.log('event posted on server');
});
```

Scenarios for web (express.js) application:

```js
var express = require('express');
var seismo = require('seismo');
var app = express();

// create analytics client, by providing app id
var events = seismo('my-web-app');

var eventsMiddleware = function (event, data) {
	return function (req, res, next) {
		events(event, data, next);
	}
}

app.get('/login',
	eventsMiddleware('user login request'),
	renderLogin
);

app.post('/login',
	checkCredentials,
	eventsMiddleware('user logged on'),
	redirectToApp
);

app.get('/search', function(req, res) {
	var query = req.query['q'];
	search.run(query, function (err, results) {
		events('search executed', {query: query, time: results.timeTakes});
		res.json(results.data);
	});
})
```

## Querying for results

You can query server for data you application collected.

```js
var seismo = require('seismo');
var events = seismo('my-web-app');

// query all collected events
events.query(function (err, results) {
	console.log(results);
});
```

```js
// query by event name
events.query('search executed', function (err, results) {
	console.log(results);
});
```

```js
// query by event type
events.query({id: 'app-start'}, function (err, results) {
	console.log(results);
});
```


For convenience, you can request data by certain date.

```js
var analytics = require('analytics');
var events = analytics('my-web-app');

// query all collected events for today
events.query({date: 'today'}, function (err, results) {
	console.log(results);
});
```

```js
// query all collected events for particular day
events.query({date: '2014-09-26'}, function (err, results) {
	console.log(results);
});
```

```js
// query all collected events for particular, for event name
events.query({event: 'search executed', date: '2014-09-26'}, function (err, results) {
	console.log(results);
});
```

```js
// query all collected events for particular, for event type
events.query({id: 'app-start', date: '2014-09-26'}, function (err, results) {
	console.log(results);
});
```

## Reports

In order, to build dashboard application, there are number of ready to use reports.

```js
// report all events by hour
events.report({event: 'application started', report: 'hour', date: '2013-09-29', hour: 6}, function (err, summary) {
	console.log(summary);
});
```

```js
// report all events by day
events.report({event: 'application started', report: 'day', date: '2013-09-29'}, function (err, summary) {
	console.log(summary);
});
```

```js
// report all events by week
events.report({event: 'application started', report: 'week', date: '2013-09-29'}, function (err, summary) {
	console.log(summary);
});
```

```js
// report all events by month
events.report({event: 'application started', report: 'month', date: '2013-09-29'}, function (err, summary) {
	console.log(summary);
});
```

```js
// report all events by period
events.report({event: 'application started', report: 'period', from: '2013-09-10', to: '2013-09-13'}, function (err, summary) {
	console.log(summary);
});
```

Summary object contains totals,

```js
{
	id: 'app-started',
	event: 'application started',
	total: 224
}
```

## License

MIT
