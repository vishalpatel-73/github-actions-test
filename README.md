# Prerequisites

To use this action, you first need to configure AWS credentials and set the AWS Region in your GitHub environment by using the configure-aws-credentials step.

## Configure AWS Credentials for GitHub Actions

Configure your AWS credentials and region environment variables for use in other GitHub Actions. This action implements the AWS SDK credential resolution chain and exports environment variables for your other Actions to use. Environment variable exports are detected by both the AWS SDKs and the AWS CLI for AWS API calls.

The IAM role the action assumes must have the following permissions:

    * GetParameters on the secrets you want to retrieve.

```yaml
- name: Configure AWS credentials
  id: aws-credentials
  uses: aws-actions/configure-aws-credentials@v1
  with:
    # This is passing the Keys values for AWs configuration.
    aws-access-key-id: ${{ secrets.PROD_AWS_ACCESS_KEY_ID }}
    aws-secret-access-key: ${{ secrets.PROD_AWS_SECRET_KEY }}
    aws-region: us-east-1
```

# Get AWS SSM Parameter

A GitHub action centered on AWS Systems Manager Parameter Store GetParameters call, and placing the results into environment variables.

This action is optimized to use the least possible number of API calls to Parameter Store, to avoid the low rate limits.

## Usage

```yaml
- uses: dkershner6/aws-ssm-getparameters-action@v1
  with:
    parameterPairs: "
      /my-app/dev/db-url = database_url,
      /my-app/dev/db-password = database_password"
    # The part before equals is the ssm parameterName, and after is the ENV Variable name for the workflow.
    # No limit on number of parameters. You can put new lines and spaces in as desired, they get trimmed out.
    withDecryption: "true" # defaults to true
```

The parameter store has the following parameter values.

```
/my-app/dev/db-url
/my-app/dev/db-password
```

These Environment variables will be created:

```
database_url
database_password
```

# Get AWS SSM Secret

To use a secret in a GitHub job, you can use a GitHub action to retrieve secrets from AWS Secrets Manager and add them as masked Environment variables in your GitHub workflow.

The IAM role the action assumes must have the following permissions:

- GetSecretValue on the secrets you want to retrieve.

## Usage

To use the action, add a step to your workflow that uses the following syntax.

```yaml
- name: Step name
  uses: aws-actions/aws-secretsmanager-get-secrets@v1
  with:
    secret-ids: /prod/secret
    parse-json-secrets: (Optional) true|false
```

The following example creates environment variables by parsing the JSON in the secret.

The secret /prod/secret has the following secret value.

```json
{
  "api_user": "user",
  "api_key": "key",
  "config": {
    "active": "true"
  }
}
```

These Environment variables will be created:

```
_PROD_SECRET_API_USER: "user"
_PROD_SECRET_API_KEY: "key"
_PROD_SECRET_CONFIG_ACTIVE: "true"
```
