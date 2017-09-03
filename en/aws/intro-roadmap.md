# Roadmap

`architect` is open source so you can help! Jump over to the [community page](/intro/community) to get more info about becoming a contributor.

## In progress

- `npm run dns` generates a `static.domainname.com` CNAME that points to an S3 CloudFront Distribution from `@domain` value 
- `@static` support added to `.arc` for universal render support 
- `arc-env` env variables and configuration management
- Generated API Gateway `staging` automatically adds a `GET /_logs` endpoint that pretty prints CloudWatch logs

## Recently shipped

- <strike>`arc-functions` first class `Error` support</strike>
- <strike>`arc-functions` consolidate req / res impl</strike>
- <strike>`arc-docs` guides for http, csrf and `req._url` helpers</strike> [http guide](/reference/http)
- <strike>`arc-dns` to paper over the API Gateway, AWS Certificate Manager, and Route53 setup</strike> [dns npm workflows](/reference/npm-run-scripts#arc-dns) 
- <strike>`@slack` section for generating Slack app endpoints with API Gateway and Lambda</strike> [`@slack`](/reference/slack)

## Help requested

- `arc-docs` translations
- Syntax highlighting for `.arc` in popular editors and Prism
