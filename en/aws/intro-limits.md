# Limits

The cloud has limits. And `arc` itself is an abstraction with deliberate constraints.

Whether we label them *constraints* or *limits*, they are **tradeoffs** you need to be aware of when designing your software architecture for cloud functions.

## Cloud limits

- Some things just fail, and you won't even always get a useful error 
  - This means: you _must_ consider retrys, including manual retrys, where you create / destory / recreate infra workflows (and, accordingly, `.arc` workflows are built to be run and re-run)
  - Yes, it's "kinda gross" but it does actually work. Sometimes we call this situation *immutable infrastructure* to feel better! &#128150;
- Regions go down, and there isn't necessarily a great way to move them without a DNS change (although this is by no means limited only to cloud functions)
- Lambda cold starts are vicious on large Lambdas; the best antidote is to author small as possible Lambda functions (rule of thumb: sub 5MB compressed, including modules, usually results in sub-second execution)
- API Gateway: whereby 403 it probably meant 404
- General sketchiness that is distributed systems (gray failures)
- API Gateway API limits can be brutal, so creating http routes can be slow (i.e. generating a large amount of routes can take minutes)
- Cloudwatch logs are not structured (so we search for `console.log` strings instead of querying structured data -- inspectability is hugely important for production systems)
- Lambdas cannot execute for longer than 5 minutes 
- Lambda functions require you to `npm install` to your project `node_modules` individually prior to deployment

## Arc constraints

Most of these constraints were borne out of necessity for speed; velocity aided by way of minimum essential capability.

These items will be great topics of community discussion and future contribution, so file them as: _maybe someday_.

- `PATCH`, `PUT`, `DELETE` are not supported, but maybe they shouldn't be (100% support everything browsers do: `GET` and `POST`)
- Routes must be unique (i.e. no overloading `Content-Type`); it's certainly possible to get around, but you probably don't want to do that anyhow
- An `application/x-form-urlencoded` `POST` must respond with a redirect
- Currently only `String` and `Number` types are supported for Partition and Sort keys in DynamoDB

## Next: [Check out the quickstart](/quickstart)
