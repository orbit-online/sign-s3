#!/usr/bin/env python

import urllib.parse
from collections import namedtuple

usage = """sign-s3 - Signs an S3 request
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
"""

AWSKey = namedtuple('AWSKey', ['id', 'secret'])
url_tpl = "https://s3-{region}.amazonaws.com/{path}?AWSAccessKeyId={access_key}&Expires={expires}&Signature={sig}"


def gen_signature(secret, sign_string):
	import base64
	import hmac
	import hashlib
	return urllib.parse.quote(base64.encodebytes(hmac.new(bytes(secret, 'ascii'), bytes(sign_string, 'ascii'), digestmod=hashlib.sha1).digest()).strip())


def sign_put(key, region, obj_path, expires, mime_type, public):
	amz_headers = {}
	if public:
		amz_headers['x-amz-acl'] = 'public-read'
	sign_string = ("PUT\n\n{mime_type}\n{expires}\n{amz_headers}/{path}"
	               .format(mime_type=mime_type,
	                       expires=expires,
	                       amz_headers=' ' + ''.join('%s:%s\n' % (k, v) for k, v in amz_headers.items()),
	                       path=obj_path))
	signature = gen_signature(key.secret, sign_string)
	url = url_tpl.format(region=region, path=obj_path, access_key=key.id, expires=expires, sig=signature)
	curl_req = ("curl -i -X PUT{amz_headers} -H 'Content-Type:{mime_type}' -d@FILEPATH '{url}'"
	            .format(url=url,
	                    amz_headers=' '.join("-H '%s:%s'" % (k, v) for k, v in amz_headers.items()),
	                    mime_type=mime_type))
	return curl_req, url


def sign_get(key, region, obj_path, expires):
	sign_string = "GET\n\n\n{expires}\n/{path}".format(expires=expires, path=obj_path)
	signature = gen_signature(key.secret, sign_string)
	url = url_tpl.format(region=region, path=obj_path, access_key=key.id, expires=expires, sig=signature)
	curl_req = ("curl -i '{url}'".format(url=url))
	return curl_req, url


def sign(opts):
	import time
	import os
	key = AWSKey(os.getenv('AWS_ACCESS_KEY_ID', opts.get('ACCESS_KEY')), os.getenv('AWS_SECRET_ACCESS_KEY', opts.get('SECRET_KEY')))
	obj_path = opts['BUCKET'] + '/' + urllib.parse.quote(opts['OBJECT'])
	expires = int(time.time() + int(opts['--valid']))
	if opts['put']:
		return sign_put(key, opts['--region'], obj_path, expires, opts['--mime-type'], opts['--public'])
	if opts['get']:
		return sign_get(key, opts['--region'], obj_path, expires)


def main():
	import docopt
	opts = docopt.docopt(usage)
	curl_req, url = sign(opts)
	if opts['--curl']:
		print(curl_req)
	elif opts['--url']:
		print(url)
	else:
		print("CURL Request: %s" % curl_req)
		print("URL: %s" % url)

main()
