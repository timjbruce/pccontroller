# pccontroller

This project contains source code and supporting files for a serverless application that you can deploy with the SAM CLI. It includes the following files and folders:

- statemachines - Definition for the state machine that orchestrates the stock trading workflow.
- template.yaml - A template that defines the application's AWS resources.

This application allows you to run a state machine and turn on or off all provisioned concurrency for Lambda functions. 

## No warranty provided

No warranty is provided or assumed via the use of this code or repository. Use at your own risk.

### Permissions Given to the State Machine

The state machine role has elevated permissions that you should be aware of. It can perform the following actions:

* Lambda ListFunctions
* Lambda ListAliases
* Lambda GetProvisionedConcurrencyConfig
* Lambda PutProvisionedConcurrencyConfig
* Lambda DeleteProvisionedConcurrencyConfig
* DynamoDB PutItem
* DynamoDB Scan
* DynamoDB DeleteItem

*** NOTE: *** This application only enables/disables provisioned concurrency for Function Aliases at this time.

### Costs

Costs for this Step Function and the resulting Provisioned Concurrency are the responsibility of the user. These cannot be estimated, since the charges for Provisioned Concurrency are based on the settings you provide.

## Disabling Provisioned Concurrency

When turning provisioned concurrency off, send the message `{ "mode" : "disable" }`. The state machine will save existing function provisioned concurrency settings to a DynamoDB table, which is used to turn on provisioned concurrency later.

## Enabling Provisioned Concurrency

To turn on provisioned concurrency, send the message `{ "mode": "enable }`. The state machine scans all the records from DynamoDB and sets the provisioned concurrency settings back on the function.

## Enabling Provisioned Concurrency via this Step Function

You can use this Step Function to enable / disable levels of provisioned concurrency that are not set via the application's CloudFormation stack, console, or cli. To do so, enter records in the format below in the provisionedConcurrency table created in the template.

* functionArn: S: the fully qualified ARN of the function
* alias: S:  the alias that you want to enable provisioned concurrency on
* provisionedConcurrency: N: The number of concurrency functions you wish to have for provisioned concurrency

After doing so, run the "Enabling Provisioned Concurrency" step above.

## Deploy the Step Function

The Serverless Application Model Command Line Interface (SAM CLI) is an extension of the AWS CLI that adds functionality for building and testing Lambda applications. It uses Docker to run your functions in an Amazon Linux environment that matches Lambda.

To use the SAM CLI, you need the following tools:

* SAM CLI - [Install the SAM CLI](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-sam-cli-install.html)
* Docker - [Install Docker community edition](https://hub.docker.com/search/?type=edition&offering=community)

To build and deploy your application for the first time, run the following in your shell:

```bash
sam build
sam deploy --guided
```

The first command will build the source of your application. The second command will package and deploy your application to AWS, with a series of prompts:

* **Stack Name**: The name of the stack to deploy to CloudFormation. This should be unique to your account and region, and a good starting point would be something matching your project name.
* **AWS Region**: The AWS region you want to deploy your app to.
* **Confirm changes before deploy**: If set to yes, any change sets will be shown to you before execution for manual review. If set to no, the AWS SAM CLI will automatically deploy application changes.
* **Allow SAM CLI IAM role creation**: Many AWS SAM templates, including this example, create AWS IAM roles required for the AWS Lambda function(s) included to access AWS services. By default, these are scoped down to minimum required permissions. To deploy an AWS CloudFormation stack which creates or modifies IAM roles, the `CAPABILITY_IAM` value for `capabilities` must be provided. If permission isn't provided through this prompt, to deploy this example you must explicitly pass `--capabilities CAPABILITY_IAM` to the `sam deploy` command.
* **Save arguments to samconfig.toml**: If set to yes, your choices will be saved to a configuration file inside the project, so that in the future you can just re-run `sam deploy` without parameters to deploy changes to your application.

## Cleanup

To delete the sample application that you created, use the AWS CLI. Assuming you used your project name for the stack name, you can run the following:

```bash
sam delete --stack-name pccontroller
```
