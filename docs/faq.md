# FAQ
- [What is restapiId and roleArn in package.json?](#what-is-restapiid-and-rolearn-in-packagejson)
- [How to prepare roleArn?](#how-to-prepare-rolearn)
- [How to configure AWS credentials?](#how-to-configure-aws-credentials)
- [How to change an action's method/path/content-type?](#how-to-change-an-actions-methodpathcontent-type)
- [How to change an action's behavior?](#how-to-change-an-actions-behavior)
- [How to access to HTTP request data?](#how-to-access-to-http-request-data)
- [Is there a demo application using fluct?](#is-there-a-demo-application-using-fluct)

## What is restapiId and roleArn in package.json?
In addition to typical information about npm,
fluct adds some fluct-specific metadata in `fluct` property such as `restapiId` and `roleArn`.
The `restapiId` is automatically set when you invoke `fluct deploy` at the 1st time.
This value is used to detect which Restapi is associated to the application.

The `roleArn` is also a fluct-specific property like `restapiId` but it's not automatically set,
so you need to manually prepare and configure it.
This value is attached to your AWS Lambda functions to allow them to be invoked from Amazon API Gateway.

```json
{
  "name": "myapp",
  "private": true,
  "fluct": {
    "restapiId": "b15rph8lh3",
    "roleArn": "arn:aws:iam::012345678912:role/myExampleRole"
  }
}
```

## How to prepare roleArn?
Head over to [AWS Console](https://console.aws.amazon.com) and create a new IAM role
that has `AWSLambdaBasicExecutionRole`, then copy its ARN value and paste it into package.json.
If you have installed aws-cli and you have a permission to create a new IAM role,
you can also create it by the following command (where `fluct-myapp` is the new role name):

```
$ aws iam create-role --role-name fluct-myapp --assume-role-policy-document arn:aws:iam::aws:policy/service-role/AWSLambdaBasicExecutionRole
```

## How to configure AWS credentials?
Fluct will automatically detect your AWS credentials from the shared credentials file in
`~/.aws/credentials` or environment variables such as `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`,
and `AWS_PROFILE`. Fluct internally uses AWS SDK for JavaScript, so please see
[Configuring the SDK in Node.js — AWS SDK for JavaScript](http://docs.aws.amazon.com/AWSJavaScriptSDK/guide/node-configuring.html)
for more details about AWS credentials settings.

## How to change an action's method/path/content-type?
Open your action's package.json and edit properties of `fluct` property like this:

```json
{
  "name": "list_users",
  "private": true,
  "fluct": {
    "arn": null,
    "contentType": "text/html",
    "httpMethod": "GET",
    "path": "/users",
    "statusCode": 200
  }
}
```

## How to change an action's behavior?
Open your action's index.js and edit export.handler function.
The 1st argument of `context.succeed` will be the endpoint's response body.

```js
export.handler = function (event, context) {
  context.succeed('Hello, world!');
};
```

## How to access to HTTP request data?
The `event` object provides some information about current HTTP request.
This is an event object example:

```js
{
  accountId: "",
  apiId: "os92ef4zmf",
  apiKey: "",
  caller: "",
  headerParams: {
    Accept: "text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8",
    Accept-Encoding: "gzip",
    Accept-Language: "ja,en-US;q=0.8,en;q=0.6",
    Cache-Control: "max-age=0",
    CloudFront-Forwarded-Proto: "https",
    CloudFront-Is-Desktop-Viewer: "true",
    CloudFront-Is-Mobile-Viewer: "false",
    CloudFront-Is-SmartTV-Viewer: "false",
    CloudFront-Is-Tablet-Viewer: "false",
    CloudFront-Viewer-Country: "JP",
    Upgrade-Insecure-Requests: "1",
    Via: "1.1 05d8760db688689d0f8fddf07721fa19.cloudfront.net (CloudFront)",
    X-Amz-Cf-Id: "zoSZldqelZepR20SaYFdce-4Wd0NMuqgJcEv7m_rIWpDhpDj1Nsqlg==",
    X-Forwarded-For: "218.42.217.86, 205.251.214.77",
    X-Forwarded-Port: "443",
    X-Forwarded-Proto: "https"
  },
  httpMethod: "GET",
  pathParams: { },
  queryParams: {
    foo: "bar"
  },
  requestId: "c2cee057-41e2-11e5-81c7-e9b9b0c6b555",
  resourceId: "2x33sn",
  resourcePath: "/echo",
  responseBody: { },
  sourceIp: "218.42.217.86",
  stage: "production",
  user: "",
  userAgent: "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10_3) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/44.0.2403.130 Safari/537.36",
  userArn: ""
}
```

## Is there a demo application using fluct?
I'm very pleased that you asked that! See https://github.com/r7kamura/fluct-example
