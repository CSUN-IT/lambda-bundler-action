# Lambda Bundler Action

> :warning: This action is mainly intended for internal CSUN use but if you find a use for this yourself, we won't stop you. Since we need to use this, we'll obey semantic versioning or else we'll break our own code.

The ZIP files we use to bundle Lambda function source code needs to be named in a specific manner and contain certain metadata. This action allows us to unify all of that behavior in a single action that we can copy/paste in our workflows instead of updating dozens of bash scripts.

## Usage

```yaml
- id: lambda
  name: Configure Lambda Bundle Metadata
  uses: CSUN-IT/lambda-bundler-action@v1
  with:
    account-id: 1111222333444
    region-name: us-west-2
    deployment-name: DeploymentName
    file-name: lambda

#
# Compile and install dependencies
#

# Bundle a Node.js Lambda file
- env:
    ZIP_FILE: ${{ steps.lambda.outputs.zip-file }}
    ZIP_FILE_S3: ${{ steps.lambda.outputs.zip-file-s3 }}
  run: |
    zip -vr $ZIP_FILE \
      .bundle-version.txt \
      node_modules/ \
      src/ \
      index.js \
      -x "*.DS_Store"
    aws s3 cp "$ZIP_FILE" "s3://some-lambda-bucket/$ZIP_FILE_S3"
```
