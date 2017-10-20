## Provision and deploy cloud infrastructure with a simple plaintext manifest

Event driven programming with cloud functions can be tricky to set up and maintain. `architect` uses a simple plaintext manifest and `npm` script based workflows for provisioning cloud infrastructure in minutes and deploying in seconds. 

Currently, `architect` supports the following Amazon Web Services:

- [Lambda](https://aws.amazon.com/lambda/) for compute functions
- [API Gateway](https://aws.amazon.com/api-gateway/) for HTTP routing
- [Route53](https://aws.amazon.com/route53) for DNS
- [CloudFront](https://aws.amazon.com/cloudfront/) for CDN
- [S3](https://aws.amazon.com/s3/) for static assets
- [Simple Notification Service](https://aws.amazon.com/sns/) for event pub/sub functions
- [CloudWatch Events](https://docs.aws.amazon.com/lambda/latest/dg/with-scheduled-events.html) for scheduling functions
- [DynamoDB](https://aws.amazon.com/dynamodb/) for data persistence, querying and trigger functions

Everything you do with `architect` starts with a `.arc` file:

```arc
# this is an .arc file
@app
testapp

@html
get /
get /hellos
post /hello
```

Running `npm run create` generates cloud function code locally:

```bash
/
|-- src
|   `-- html
|       |-- get-index/
|       |-- get-hellos/
|       `-- post-hello/
|-- .arc
`-- package.json

```

And `npm run deploy` ships this code to the cloud. <span class=cloud>&#x1f329;</span>

## Infra primitives 

- HTTP route handler functions for `application/json` and `text/html`
- Subscribe functions to events (and publish events from any other function)
- Scheduled functions
- Database tables, indexes, and trigger functions

## Workflows

- **Create infra** from an `.arc` manifest, enabling deletion and re-creation of infrastructure trivial (i.e. change availability zones in minutes)
- **Deploy in seconds** with first class support for `staging` and `production` with a proper `NODE_ENV` environment variable
- **Work locally** while completely offline with a speedy in-memory database

## Next steps

- [Follow the quickstart](/quickstart)
- [Read the introduction](/intro)
- [Read the reference](/reference)
