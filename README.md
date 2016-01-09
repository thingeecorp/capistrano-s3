# capistrano-s3

> Enables static websites deployment to Amazon S3 website buckets using Capistrano.

[![Build Status](https://img.shields.io/travis/hooktstudios/capistrano-s3/master.svg)](https://travis-ci.org/hooktstudios/capistrano-s3)
[![Dependency Status](https://img.shields.io/gemnasium/hooktstudios/capistrano-s3.svg)](https://gemnasium.com/hooktstudios/capistrano-s3)
[![Code Climate](https://img.shields.io/codeclimate/github/hooktstudios/capistrano-s3.svg)](https://codeclimate.com/github/hooktstudios/capistrano-s3)
[![Gem Version](https://img.shields.io/gem/v/capistrano-s3.svg)](https://rubygems.org/gems/capistrano-s3)

## Hosting your website with Amazon S3

Amazon S3 provides special websites enabled buckets that allows you to serve web pages from S3.

To learn how to setup your website bucket, see [Amazon Documentation](http://docs.aws.amazon.com/AmazonS3/latest/dev/WebsiteHosting.html).

## Getting started

```ruby
# Gemfile
source 'https://rubygems.org'
gem 'capistrano-s3'
```

### Setup

Install gems with bundle and create your public folder that will be published :

    bundle install
    mkdir -p public

Gem supports both flavors of Capistrano (2/3). Configurations between versions differ a bit though.

#### Capistrano 2

First initialise Capistrano for given project - `bundle exec capify .`

Replace `deploy.rb` content generated by capify
with these simple Amazon S3 configurations :

    # config/deploy.rb
    require 'capistrano/s3'

    set :bucket,            "www.cool-website-bucket.com"
    set :access_key_id,     "CHANGETHIS"
    set :secret_access_key, "CHANGETHIS"

If you want to deploy to multiple buckets, have a look at
[Capistrano multistage](https://github.com/capistrano/capistrano/wiki/2.x-Multistage-Extension)
and configure a bucket per stage configuration.

#### Capistrano 3

Initialise Capistrano by running - `bundle exec cap install`

Next add `require "capistrano/s3"` to Capfile.

Finally, replace `deploy.rb` content generated by Capistrano with
this config:

    # config/deploy.rb
    set :bucket,            "www.cool-website-bucket.com"
    set :access_key_id,     "CHANGETHIS"
    set :secret_access_key, "CHANGETHIS"

### Deploying

Add content to your public folder and run deploy command:
 - `cap deploy` (Capistrano 2)

 or

 - `cap <stage> deploy` (Capistrano 3).

## Advanced options

### Custom endpoint

If your bucket is not in the default US Standard region,
set [endpoint](http://docs.aws.amazon.com/general/latest/gr/rande.html#s3_region)
with :

```ruby
set :s3_endpoint, 's3-eu-west-1.amazonaws.com'
```

### Write options

capistrano-s3 sets files `:content_type` and `:acl` to `:public_read`, add or override with :

```ruby
set :bucket_write_options, {
    cache_control: "max-age=94608000, public"
}
```

See aws-sdk [S3Client.put_object doc](http://docs.aws.amazon.com/AWSRubySDK/latest/AWS/S3/Client.html#put_object-instance_method) for all available options.

### Redirecting

Use `:redirect_options` to natively redirect (via HTTP 301 status code)
any hosted page. For example:
```ruby
set :redirect_options, {
  'index.html' => 'http://example.org',
  'another.html' => '/test.html',
}
```
Valid redirect destination should either start with `http` or `https` scheme,
or begin with leading slash `/`.

## Example of usage

Our Ruby stack for static websites :

- [sinatra](https://github.com/sinatra/sinatra) : awesome simple ruby web framework
- [sinatra-assetpack](https://github.com/rstacruz/sinatra-assetpack) : deals with assets management, build static files into `public/`
- [sinatra-export](https://github.com/hooktstudios/sinatra-export) : exports all sinatra routes into `public/` as html or other common formats (json, csv, etc)

Mixing it in a capistrano task :

```ruby
# config/deploy.rb
before 'deploy' do
  run_locally "bundle exec ruby sinatra:export"
  run_locally "bundle exec rake assetpack:build"
end
```

See our boilerplate
[sinatra-static-bp](https://github.com/hooktstudios/sinatra-static-bp)
for an example of the complete setup.

## Contributing

See [CONTRIBUTING.md](https://github.com/hooktstudios/capistrano-s3/blob/master/CONTRIBUTING.md) for more details on contributing and running test.

## Credits

![hooktstudios](http://hooktstudios.com/logo.png)

[capistrano-s3](https://rubygems.org/gems/capistrano-s3) is maintained and funded by [hooktstudios](http://github.com/hooktstudios)

Thanks & credits also to all other [contributors](https://github.com/hooktstudios/capistrano-s3/contributors).
