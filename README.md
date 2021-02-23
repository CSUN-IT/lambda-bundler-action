# Lambda Bundler Action

> :warning: **Warning** :warning:
>
> This action is mainly intended for internal CSUN use but if you find a use for this yourself, we won't stop you. Since we need to use this, we'll obey semantic versioning or else we'll break our own workflows.

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

# Bundle a Node.js Lambda function
- env:
    ZIP_FILE: ${{ steps.lambda.outputs.zip-file }}
    ZIP_FILE_S3: ${{ steps.lambda.outputs.zip-file-s3 }}
  run: |
    zip -vr "$ZIP_FILE" node_modules/ .bundle-version.txt -x "*.DS_Store"
    cd dist; zip -vur "../$ZIP_FILE" * -x "*.DS_Store"

    aws s3 cp "$ZIP_FILE" "s3://some-lambda-bucket/$ZIP_FILE_S3"
```

### Outputs

These are the outputs created by this action:

- `build-date`: The timestamp of when this ZIP file was made (in the format of `YYYYmmDDHHMMSS`)
- `commit-hash`: The short hash of the commit being bundled
- `zip-file`: The name of the ZIP file was built (with the extension)
- `zip-file-wo-ext`: The name of the ZIP file was built (without the extension)
- `zip-file-s3`: The name of the ZIP file as it'd belong in S3 (with the extension)
- `zip-file-s3-wo-ext`: The name of the ZIP file as it'd belong in S3 (without the extension)

### Generated Files

This action generates some metadata files that should be included in your Lambda ZIP files.

#### `.bundle-version.txt`

This action additionally creates a `.bundle-version.txt` file in `$GITHUB_WORKSPACE` that __should__ be included in your Lambda ZIP files. This file contains the time this action was run and what SHA1 (i.e. which commit) was bundled. If there is ever any doubt about which commit a Lambda ZIP contains, this file will be the truth and with a commit, you can also determine _who_ triggered this build.

## Attention CSUN Staff

This repository is public by design, it **must** remain public due to [GitHub Actions only supporting pulling actions from public repositories](https://github.com/github/roadmap/issues/74). Please do **not** change the visibility of this repository and break all of our workflows; this is not the security risk you're looking for.
