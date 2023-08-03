# sign-s3

A small library to sign S3 requests, requires Python 3.

## Installation

With [μpkg](https://github.com/orbit-online/upkg)

```
upkg install -g orbit-online/sign-s3@<VERSION>
```

## Usage

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
