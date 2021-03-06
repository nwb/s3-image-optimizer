# TinyPng S3 image optimizer

## Function
This is revised from original optim at https://github.com/sourcey/s3-image-optimizer. This version use https://tinypng.com/ service, which have a much highger optimization rate than the imagemin package.
Besides parameters in optim, this package needs the the Tynipng api key as TYNIPNG_KEY.
This package will also skip the GIT images as it is not handled by tinypng.

## one possible bug fix
Some how the original version is having issue with 'Handler is not a function'.
I dropped the 'module.' from index.js, so it looks like 'exports = require('./optimizer');' now.

Lambda does not allow file writing in local directory. so I set the log files in /tmp/ folders. so .marker, processe.log and skipped.log will be in /tmp.

##Intro from original
Automagitically optimize your images on S3 with the magic of AWS Lambda.

Optim is a super-simple [Lambda][l] function that can listen to an S3 bucket for uploads, and runs everything it can through [imagemin][imagemin].


## Setup

 * Clone this repo

 * Run `npm install`

 * Fill in `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY` in `.env` to a set of credentials that can create Lambda functions (alternatively have these already in your environment)

 * Create an IAM role for Optim to use. It needs the following permissions on all the S3 buckets you want to use (allowing these operations on ARN `*` is easiest to start with):
   * `getObject`
   * `putObject`
   * `putObjectAcl`


 * Find the ARN for this role. It looks something like `arn:aws:iam::1234567890:role/rolename`.

 * Fill in `AWS_ROLE_ARN` in `.env`

 * Run `npm run deploy`

 * Hurrah, your Lambda function is now deployed! It'll be created with the name `optim-production` unless you changed values in `.env`

 * You can now hook this function up to any S3 bucket you like in the management console. Easiest way is to follow [AWS's guide][s3-evt-setup]


## Configuration

There are two sets of configuration here. The `.env` file contains configuration related to setup and deployment, and `runtime.env` is for configuration of how Optim behaves.

In `.env`:
 * `TYNIPNG_KEY`: the api key from tynipng.com
 * `AWS_ACCESS_KEY_ID`: the AWS access key used to deploy the Lambda function
 * `AWS_SECRET_ACCESS_KEY`: the corresponding secret access key
 * `AWS_ROLE_ARN`: role with which the lambda function will be executed
 * `AWS_REGION`: which region to deploy to
 * `AWS_FUNCTION_NAME` and `AWS_ENVIRONMENT` control naming of the lambda function created
 * `AWS_MEMORY_SIZE` is the amount of memory given to your Lambda. It's also related to how much CPU share it gets. Since optimizing images is fairly intensive, probably best to keep this high
 * `AWS_TIMEOUT` runtime timeout for the lambda in seconds up to 5 minutes. Again, image optimization is fairly intensive so you'll probably want to leave this at the maximum of 300.

In `runtime.env`:

 * `UPLOAD_ACL`: finalised images will be uploaded with this permission level. Should be one of `private` `public-read` `public-read-write` `aws-exec-read` `authenticated-read` `bucket-owner-read` `bucket-owner-full-control`. Default is `public-read`.

[l]: https://aws.amazon.com/lambda/
[s3-evt-setup]: http://docs.aws.amazon.com/AmazonS3/latest/UG/SettingBucketNotifications.html
