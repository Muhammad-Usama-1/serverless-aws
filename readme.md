# Serverless

## What is Serverless means ?

serverless means

1. No server to provision or manage
1. scale with usage
1. pay for value
1. Availability and fault tolerance built in

## Benifits

Agility , less overhead , better focus, increases scale , more flexibility , faster time to market
You should have npm installed on your machine

## LifeCycle

we upload code to lamda function

lamda will run on trigger

## Demo

we will be using serverless framework others options are AWS CDK , terrafrom etc

### Install serverless globaly in your system

```bash
npm install -g serverless
```

### configure a aws profile

you can go ahead on AWS console and create a user with Administrator access

```bash
serverless config credentials \
  --provider aws \
  --key AKIAIOSFODNN7EXAMPLE \
  --secret wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY \
  --profile serverless-user
```

```bash
serverless create --template aws-nodejs --path myServerlessProject
```

Now lets go into the folder of newly created project and make some changes in serverless.yaml file

take a look at provider block and add a profile ,region, after adding your provider will look like this

```yaml
provider:
  name: aws
  runtime: nodejs12.x
  region: us-east-1
  # The profile name we set up on config serlverless
  profile: serverless-user
```

now run

```bash
sls deploy
```

now see lamda in console (note we are in region us-east-1)
https://us-east-1.console.aws.amazon.com/lambda/

---

for put object

plugins:

- serverless-s3-sync

```yaml
custom:
    s3Sync: - BucketName: my-new-bucket-855
    localDir: uploadData
```

and install plugin in terminal

```bash
npm i --save serverless-s3-sync
```

your serverless.yaml file so far will look like this

```yaml
service: myserverlessproject
frameworkVersion: "3"

provider:
  name: aws
  runtime: nodejs12.x
  region: us-east-1
  # The profile name we set up on config serlverless
  profile: serverless-user

plugins:
  - serverless-s3-sync

custom:
  s3Sync:
    - bucketName: my-new-bucket-855
      localDir: uploadData

functions:
  hello:
    handler: handler.hello

resources:
  Resources:
    NewResource:
      Type: AWS::S3::Bucket
      Properties:
        BucketName: my-new-bucket-855
```

sls deploy

## Creating an API with Serverless - API Gateway and AWS Lambda

Now we will see how we can Creat an API with Serverless - API Gateway and AWS Lambda
using aws lamda and api gateway and deploy all using serverless
create a new directory lambdas inside project directory and two file inside its
project/lambdas/API_Responses.js

```js
const Responses = {
  _200(data = {}) {
    return {
      headers: {
        "Content-Type": "application/json",
        "Access-Control-Allow-Methods": "*",
        "Access-Control-Allow-Origin": "*",
      },
      statusCode: 200,
      body: JSON.stringify(data),
    };
  },

  _400(data = {}) {
    return {
      headers: {
        "Content-Type": "application/json",
        "Access-Control-Allow-Methods": "*",
        "Access-Control-Allow-Origin": "*",
      },
      statusCode: 400,
      body: JSON.stringify(data),
    };
  },
};

module.exports = Responses;
```

project/lambdas/getUser.js

```js
const Responses = require("./API_Responses");

exports.handler = async (event) => {
  console.log("event", event);

  if (!event.pathParameters || !event.pathParameters.ID) {
    // failed without an ID
    return Responses._400({ message: "missing the ID from the path" });
  }

  let ID = event.pathParameters.ID;

  if (data[ID]) {
    // return the data
    return Responses._200(data[ID]);
  }

  //failed as ID not in the data
  return Responses._400({ message: "no ID in data" });
};

const data = {
  1234: { name: "Anna Jones", age: 25, job: "journalist" },
  7893: { name: "Chris Smith", age: 52, job: "teacher" },
  5132: { name: "Tom Hague", age: 23, job: "plasterer" },
};
```

now we need make some changes in function block in serverless.yaml file

funcation should be look like this

```yaml
functions:
  getUser:
    handler: lambdas/getUser.handler
    events:
      - http:
          path: get-user/{ID}
          method: GET
          # If you want to hit url from browser
          cors: true
```

now you have created a simple API

```npm
sls deploy
```

it will give you a url simply go ahead in your browser and check it

## Adding Serverless Webpack to your Project - fix Lambda Upload Limits
