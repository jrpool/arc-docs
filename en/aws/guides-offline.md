# Offline

> Work offline to preview and test `.arc` defined code

You can run an `.arc` locally with `arc-sandox` helper found in `@architect/workflows`. The sandbox is also available as a module for writing tests.

## Preview vs Test

Following the [quickstart](/quickstart) you should have everything wired up so `npm start` kicks up a local web server and creates tables and indexes defined in `.arc` for previewing your work in the flow without deployment. If you want to write tests (you do) against the infra without deployment you'll need to setup the sandbox as a module.

This guide will use the following example `.arc` file:

```arc
@app
testapp

@html
get /

@json
get /api

@tables
cats
  pplID *String
  catID **String

ppl
  pplID *String

@indexes
ppl
  email *String
```

## Setup

Setup an offline test suite:

```bash
mkdir tests
npm i tape tap-spec --save-dev
touch tests/http-test.js
touch tests/db-test.js
```

Add the following to `package.json`:

```javascript
{
  "test": "NODE_ENV=testing AWS_PROFILE=xxx AWS_REGION=xxx tape test/**/*-test.js | tap-spec"
}
```

NOTE: you need to setup `AWS_PROFILE` and `AWS_REGION` per the quickstart.


> Tip: while you can use any test runner and reporter combo you want the TAP family is strongly reccomended; test suites that inject globals cause more bugs than they fix

Scaffold your two test files with an environment check; this a good practice to get the testing muscles warmed up.

```javascript
// tests/http-test.js
var test = require('tape')
var arc = require('@architect/workflows')

test('env', t=> {
  t.plan(1)
  t.ok(arc.sandbox.http, 'arc.sandbox.http exists in current scope')
})
```

```javascript
// tests/http-test.js
var test = require('tape')
var arc = require('@architect/workflows')

test('env', t=> {
  t.plan(1)
  t.ok(arc.sandbox.db, 'arc.sandbox.db exists in current scope')
})
```

Check the tests by running `npm t`. (If things fail that's ok! That's why we have tests!!)

## HTTP Testing

In order to test HTTP routes we will need an HTTP client. Lets use tiny-json-http; it is a small, dependency free, module with a straigtforward interface. Install by running `npm i tiny-json-http --save-dev` and edit the http test:

```javascript
// tests/http-test.js
var test = require('tape')
var tiny = require('tiny-json-http')
var arc = require('@architect/workflows')

test('env', t=> {
  t.plan(1)
  t.ok(arc.sandbox.http, 'arc.sandbox.http exists in current scope')
})

/**
 * first we need to start the local http server
 */
var server
test('arc.sandbox.http.start', t=> {
  t.plan(1)
  server = arc.sandbox.http.start(function _start() {
    t.ok(true, 'http server started on http://localhost:3333')
  })
})

/**
 * then we can make a request to it and check the result
 */
test('get /', t=> {
  t.plan(1)
  tiny.get({
    url: 'http://localhost:3333'
  }, 
  function _get(err, result) {
    if (err) throw err
    t.ok(result.body, 'got 200 response')
  })
})

/** 
 * finally close the server so we cleanly exit the test
 */
test('server.close', t=> {
  t.plan(1)
  server.close()
  t.ok(true, 'server closed')
})
```

As your app matures you will want to augment these tests with more elabarate response checks.

## DB Testing

In an `.arc` defined project `NODE_ENV` is used for knowing where the code is running. This way apps with `NODE_ENV` set to `staging` or `production` will load the correct DynamoDB endpoints correctly. Your test suite and any client wrappers you author should follow suit.

```javascript
var AWS = require('aws-sdk')
var endpoint = new AWS.Endpoint('http://localhost:5000')
var db = process.env.NODE_ENV === 'testing'? new AWS.DynamoDB({endpoint}) : new AWS.DynamoDB

var test = require('tape')
var arc = require('@architect/workflows')

/**
 * first we need to start the local db server and grab a reference to the client
 */
var client 
test('arc.sandbox.db.start', t=>{
  t.plan(1)
  client = arc.sandbox.db.start(xxx=> t.ok(true, 'started'))
})

/**
 * then we can work with the db using the vanilla `DynamoDB` client (or `DynamoDB.DocumentClient`)
 */
test('db', t=> {
  t.plan(1)
  // note: we do not need to create the tables the
  // sandbox detected the .arc and did that above
  db.listTables({}, function _list(err, result) {
    if (err) throw err
    t.ok(result, 'got result')
    console.log(result) 
  })
})

/** 
 * finally close the db client so we cleanly exit the test
 */
test('arc.sandbox.db.close', t=>{
  t.plan(1)
  // finally we'll use that client reference from above to close the sandbox
  client.close()
  t.ok(true, 'closed')
})
```
