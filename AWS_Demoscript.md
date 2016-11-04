## Chef Manage Account

http://manage.chef.io

## ChefDK Installation

https://downloads.chef.io/chef-dk/mac/

## AWS Commandline tools Installation

(MAC)
*brew install ec2-api-tools*

*brew install awscli*

## Create your SSH Key
I assume you already have an ssh key pair in your ~/.ssh folder. If you want to
use that key for AWS you can skip this next step. The following step is if you
want a unique key for your AWS work. Open a terminal and run the following
command (substitute your passphrase or leave it blank) and feel free to use a
different file name:

*cd ~/.ssh*

*ssh-keygen -N ‘aPassword’ -f xxx_key*

*ssh-add xxx_key*

## Import your public key to EC2
Importing your own public key is a great feature of AWS, but this has to be
done for each region you want to use it in. In the following step I will import
to eu-west-1

*ec2ikey xxx_key --region eu-west-1 --public-key-file ~/.ssh/***xxx_key.pub** --aws-secret-key ***XXX*** --aws-access-key ***XXX***

## AWS Configure

*aws configure*

Access Key ID:
Use your AWS credentials
Secret Access Key:
Use your AWS credentials
Default region name
eu-west-1
Default output format
json

## Chef Starter Kit Installation

Download chef-repo/Chef Starter Kit from the Chef Server

## Install Knife EC2 gem

*chef gem install knife-ec2*

## AWS Credentials in knife.rb
```
knife[:aws_config_file] = "/Users/cjo/.aws/config"
knife[:aws_credential_file] = "/Users/cjo/.aws/credentials"
```
## Editor in knife.rb
```
knife[:editor]="vi"
```
## Write a WebServer (Apache) cookbook

*mkdir chef-repo/cookbooks*
*cd chef-repo/cookbooks*
*chef generate cookbook webserver*
*cd webserver*
*vi recipes/default.rb*

```
package 'httpd'

template '/var/www/html/index.html' do
  source 'index.html.erb'
end

template '/etc/httpd/conf/httpd.conf' do
  action :create
  source 'httpd.conf.erb'
  notifies :restart, 'service[httpd]'
end

service 'httpd' do
  action [:enable, :start]
end
```
chef generate attribute default
vi attributes/default.rb

add into the file:
```
default['apache']['port'] = 8080
```
chef generate template index.html
vi templates/index.html.erb

* add into the file a copy from index.html file template

*chef generate template httpd.conf*
*vi templates/httpd.conf.erb*

* add into the file a copy from httpd.conf file template

## Upload cookbook

*knife cookbook upload webserver*

## Create instance and add cookbook

*knife ec2 server create --node-name ***chef-node-xxx*** -r 'recipe[webserver]' --ssh-key ***xxx_key*** --identity-file ~/.ssh/***xxx_key*** --ssh-user ec2-user --image ami-c39604b0 --security-group-ids 'sg-48ddcd2c' --subnet subnet-b79e83d2 --flavor t2.micro --region eu-west-1 --associate-public-ip*
