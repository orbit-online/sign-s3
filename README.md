# sign-s3

A small cli and GitHub actions tool to sign S3 requests, requires Python 3.

## Installation

See [the latest release](https://github.com/orbit-online/sign-s3/releases/latest) for instructions.

## Usage (CLI)

```
sign-s3 - Signs an S3 request
Usage:
  sign-s3 [options] [-c|-u] put --mime-type=M BUCKET OBJECT [ACCESS_KEY] [SECRET_KEY]
  sign-s3 [options] [-c|-u] get BUCKET OBJECT [ACCESS_KEY] [SECRET_KEY]

Options:
  -h, --help      show this help
  -v, --valid S   validity in seconds [default: 3600]
  -r, --region R  S3 region [default: eu-west-1]
  -u, --url       Only output the URL
  -c, --curl      Only output the cURL command

PUT-Options:
  -m, --mime-type M  mime-type of the uploaded file [default: text/plain]
  -p, --public        make the file publicly accessible
```

## Usage (GitHub actions)

```
name: Sign URL

jobs:
  sign:
    runs-on: ubuntu-latest
    name: Sign an S3 URL
    steps:
    - uses: orbit-online/sign-s3@v1
      id: s3-url
      with:
        bucket: my-bucket
        object: secret-binaries/my-tool
        valid-for: '3600'
        access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
        secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
        region: ${{ vars.AWS_DEFAULT_REGION }}
    - run: echo ${{ steps.s3-url.outputs.url }}
```

### Inputs

None of the parameters have defaults.

| Name                | Description                                           |
| ------------------- | ----------------------------------------------------- |
| `bucket`            | Name of the bucket                                    |
| `object`            | Name of the object                                    |
| `valid-for`         | The region of the URL endpoint                        |
| `access-key-id`     | The access key ID to sign the URL with                |
| `secret-access-key` | The secret access key to sign the URL with            |
| `region`            | Amount of time in seconds the URL should be valid for |

### Outputs

| Name  | Description    |
| ----- | -------------- |
| `url` | The signed URL |
