# Monitoring CircleCI with Amazon EventBridge

Example of building a simple solution to monitor React App CI/CD status using CircleCI, CloudWatch and EventBridge.

## Prerequisites

The following must be done before following this guide:

- Setup an AWS account
- Install the AWS CLI
- Configure the AWS CLI with user credentials
- Setup an CircleCI account

## File Structure

- /lambda - Lambda function with Servelress Framework
- /my-app - React frontend
- /.circleci - CircleCI Config

## Creating an EventBridge Rule with custom source

Create a rule with custom source circleci.myapp

```
$aws events put-rule --name "circleci.myapp" --event-pattern "{\"source\":[\"circleci.myapp\"]}"
```

Next, get Arn of lambda function circleci-log-dev-circleci created in previous step,

```
$aws lambda get-function  --function-name circleci-log-dev-circleciLog --query Configuration.FunctionArn
```

Copy Arn, then add target to rule circleci.myapp.

```
$aws events put-targets --rule circleci.myapp --targets "Id"="1","Arn"="arn:aws:lambda:REGION:ACCOUNTID:function:circleci-log-dev-circleciLog"
```

## Test Event Rule

```
aws events put-events --entries '[{ "Source": "circleci.myapp", "DetailType": "CircleCI state change", "Detail": "{ \"app\": \"myapp\", \"type\": \"fail\" }"}]'
```
