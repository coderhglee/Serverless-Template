# Chosun Serverless Template

## Pre-requisites

* [NodeJS](https://nodejs.org/en/) 12.x. It is recommended that you use [nvm](https://github.com/nvm-sh/nvm) to manage Node installations.

* An understanding of [Serverless Framework](https://www.serverless.com/framework/docs/providers/aws/guide/intro/). It will be installed as one of the npm packages in the setup below.

* AWS profile or key/secret environment variables for a user in Chosun's AWS account with programmatic access and attached AdministratorAccess policy (see [credentials docs](https://serverless.com/framework/docs/providers/aws/guide/credentials/) for more details).


## Setup and Installation

### Installation
Install the required npm packages including serverless with:
```
npm install
```
With your stages and variables configured and installation of serverless, you can validate your configured variables for a given stage with the following command:
```
npx sls print --stage local
```


## Building

The lambda JavaScript code has been written in Node using ES modules, and is transpiled into `/dist` with [babel](https://babeljs.io/). To build code from /src into /dist:

```
# Build once
npm run build

# Watch for local changes, and rebuild
npm run watch:build

# Clean the dist folder
npm run clean
```

## Running Unit Tests

Unit tests are written using the [Jest](https://jestjs.io/) framework. To run tests, execute:

```
# Run tests
$ npm test

# Run tests in watch mode
$ npm run watch:test

# Run only the Yonhap tests in watch mode
$ npx jest yonhap --coverage --watch
```

For consistent formatting and best practices, you can also check the js files using the linter with:

```
npm run lint
```


## Running Locally

To invoke a lambda locally, use [serverless invoke local](https://serverless.com/framework/docs/providers/aws/cli-reference/invoke-local/). The command will take the following form:

```
# With direct data input 
npx sls invoke local -s local -f <FunctionName> -d <event data>

# With file data input
npx sls invoke local -s local -f <FunctionName> -p <data file path>
```

### Other Useful Commands

To locally validate your serverless configuration for syntax errors prior to deploying in AWS, run the following:

```
npx sls deploy --noDeploy
```

To locally validate your serverless configuration's variable resolution:

```
npx sls print -s <stage>
```

Additional watch options are available. With the [serverless-offline](https://www.serverless.com/plugins/serverless-offline) plugin, you can start a local service to test API gateway-like endpoints (we only use API gateway for the send to print lambdas). There is also a combined watch mode that will execute the build, tests, and start the offline local API service. Warning -- this can get very noisy if you are making frequent updates.

```
# Local API service
$ npm run watch:offline

# Combined watch mode for tests, babel and local API service
$ npm run watch
```

## Deployment

[Serverless deploy](https://www.serverless.com/framework/docs/providers/aws/guide/deploying/) is used to deploy the entire CloudFormation stack including print and image wires lambdas and associated resources. Command line options for specifying the stage (aka environment), AWS region, and Arc org are available. Caution -- running these commands will deploy your code to AWS! For stable deployments, we recommend that you integrate this step into your CI/CD pipelines rather than ad-hoc manual deployments. 

```
SLS_DEBUG=* npx sls deploy --stage <<STAGE NAME>> --region <<AWS REGION>> --org_name <<ARC ORG>> --verbose
```

_Note: Once your print lambdas are deployed, you will need to configure the Websked webhook and Composer Settings for print integration to use the API Gateway URL created for the send to print lambdas._