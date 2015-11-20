
# cfn-api-gateway-integration


## Purpose

AWS CloudFormation does not support AWS API Gateway. This is a Lambda-backed custom resource to add the [AWS API Gateway's Integration](http://docs.aws.amazon.com/apigateway/api-reference/resource/integration/) to CloudFormation.

[This package on NPM](https://www.npmjs.com/package/cfn-api-gateway-integration)  
[This package on GitHub](https://www.github.com/andrew-templeton/cfn-api-gateway-integration)


## Implementation

This Lambda makes use of the Lambda-Backed CloudFormation Custom Resource flow module, `cfn-lambda` ([GitHub](https://github.com/andrew-templeton/cfn-lambda) / [NPM](https://www.npmjs.com/package/cfn-lambda)).


## Usage

  See [`./example.template.json`](./example.template.json) for a sample CloudFormation template. The example uses `Condition` statements, `Parameters`, and dynamic `ServiceToken` generation fully.


    "IntegrationLogicalIdInResourcesObject": {
      "Type": "Type": "Custom::ApiGatewayIntegration",
      "Properties": {
        "ServiceToken": "arn:aws:lambda:<cfn-region-id>:<your-account-id>:function:<this-deployed-lambda-name>",
        "Type": "AWS", // REQUIRED string enum: HTTP | AWS | MOCK
        "RestApiId": "zyxwvutsrq", // REQUIRED 10 char alphanum ID
        "ResourceId": "abcdefghij", // REQUIRED alphanum API Resource ID
        "HttpMethod": "GET", // REQUIRED Method request method to attach integration to
        "IntegrationHttpMethod":"POST", // Method on *backend* to hit
        // Can be AWS ARN URI... This is, for example, for a Lambda function...
        "Uri": "arn:aws:apigateway:us-east-1:lambda:path/2015-03-31/functions/arn:aws:lambda:us-east-1:<account-id>:function:UseMeForIntegrationTesting/invocations",
        // OR full http(s) URL URI
        // "https://google.com/search",
        // Static, assumed apigateway STS-assumed role
        "Credentials":"arn:aws:iam::<your-account-id>:role/<ApiGRoleForInvokingThing>",
        // OR Dynamic, caller-runtime-provided value (this literal value)
        // "Credentials": "arn:aws:iam::*:user/*",
        // OR use null for resource-based permissions (like S3 Public ACLed Buckets)
        "CacheNamespace": "cache-ns-string", // Optional, defaults to ResourceId
        "CacheKeyParameters": [ // Optional. Keys for caching, defined here or elsewhere.
          "integration.request.querystring.foo", // can be on this request...
          "method.request.header.bar"            // or on method request
        ],
        "RequestTemplates": { // Optional, content-type => Apache VTL hash
          "application/json": "{\"hello\": \"world\"}"
        },
        "RequestParameters": { // Optional, map into the integration request from method
          "integration.request.querystring.foo": "method.request.header.bar"
        }
      }
    }


#### Example Template Prerequisites

Though this resource works by itself, the example template in this repository also makes use of other resources in this custom family. You need to install these in your cloud to use the sample template.

 - `Custom::ApiGatewayRestApi` ([GitHub](https://github.com/andrew-templeton/cfn-api-gateway-restapi) / [NPM](https://www.npmjs.com/package/cfn-api-gateway-restapi))
 - `Custom::ApiGatewayMethod` ([GitHub](https://github.com/andrew-templeton/cfn-api-gateway-method) / [NPM](https://www.npmjs.com/package/cfn-api-gateway-method))
 - `Custom::ApiGatewayMethodResponse` ([GitHub](https://github.com/andrew-templeton/cfn-api-gateway-method-response) / [NPM](https://www.npmjs.com/package/cfn-api-gateway-method-response))

## Installation of the Resource Service Lambda

#### Using the Provided Instant Install Script

The way that takes 10 seconds...
    

    # Have aws CLI installed + permissions for IAM and Lamdba
    $ npm run cfn-lambda-deploy


You will have this resource installed in every supported Region globally!


#### Using the AWS Console

... And the way more difficult way.

*IMPORTANT*: With this method, you must install this custom service Lambda in each AWS Region in which you want CloudFormation to be able to access the `ApiGatewayIntegration` custom resource!

1. Go to the AWS Lambda Console Create Function view:
  - [`us-east-1` / N. Virginia](https://console.aws.amazon.com/lambda/home?region=us-east-1#/create?step=2)
  - [`us-west-2` / Oregon](https://console.aws.amazon.com/lambda/home?region=us-west-2#/create?step=2)
  - [`eu-west-1` / Ireland](https://console.aws.amazon.com/lambda/home?region=eu-west-1#/create?step=2)
  - [`ap-northeast-1` / Tokyo](https://console.aws.amazon.com/lambda/home?region=ap-northeast-1#/create?step=2)
2. Zip this repository into `/tmp/ApiGatewayIntegration.zip`

    `$ cd $REPO_ROOT && zip -r /tmp/ApiGatewayIntegration.zip;`

3. Enter a name in the Name blank. I suggest: `CfnLambdaResouce-ApiGatewayIntegration`
4. Enter a Description (optional).
5. Toggle Code Entry Type to "Upload a .ZIP file"
6. Click "Upload", navigate to and select `/tmp/ApiGatewayIntegration.zip`
7. Set the Timeout under Advanced Settings to 10 sec
8. Click the Role dropdown then click "Basic Execution Role". This will pop out a new window.
9. Select IAM Role, then select option "Create a new IAM Role"
10. Name the role `lambda_cfn_api_gateway_method` (or something descriptive)
11. Click "View Policy Document", click "Edit" on the right, then hit "OK"
12. Copy and paste the [`./execution-policy.json`](./execution-policy.json) document.
13. Hit "Allow". The window will close. Go back to the first window if you are not already there.
14. Click "Create Function". Finally, done! Now go to [Usage](#usage) or see [the example template](./example.template.json). Next time, stick to the instant deploy script.




#### Miscellaneous

##### Collaboration & Requests

Submit pull requests or Tweet [@ayetempleton](https://twitter.com/ayetempleton) if you want to get involved with roadmap as well, or if you want to do this for a living :)


##### License

[MIT](./License)


##### Want More CloudFormation or API Gateway?

Work is (extremely) active, published here:  
[Andrew's NPM Account](https://www.npmjs.com/~andrew-templeton)
