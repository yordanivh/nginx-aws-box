# nginx-aws-box

# What is this repo for?

The code in this repo creates a packer image in AWS (ami image). Then it carries out a kitchen test to test if needs packages are installed as requested in the `provision.sh` script.

# Why do you need this repo?

This repo could provide a valuable practice on creating image in AWS using Packer and testing their fucntionality with Kitchen.

# How to use this repo?

### Pre-requisites

* You need to have packer installed on you workstation
   *  for MacOS
   
    ```
    brew install packer
    ```
  
   *  for any other OS click [here](https://packer.io/downloads.html).

* To be able to use AWS you require to have an account, with that account you should have these two keys

```
AWS_ACCESS_KEY_ID=*your key id*
AWS_SECRET_ACCESS_KEY=*your access key*
```
   
* Clone this repo locally to a folder of your choice
```
git clone git@github.com:yordanivh/nginx-aws-box.git
```
* Go inside the newly created folder of the repo

```
cd nginx-aws-box
```
   
* You need to have ruby installed on the system with all the dependencies

```
brew install ruby
```
* To be able to run kitchen test you need to make sure the environment is set up corectly.Run these commands to do so from the current directory.

```
echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bash_profile
source ~/.bash_profile
rbenv init
echo 'eval "$(rbenv init -)"' >> ~/.bash_profile
source ~/.bash_profile
rbenv install 2.6.3
rbenv local 2.6.3
rbenv versions
gem install bundler
bundle install
```

## How to use

* To be able to run Packer you need to have the AWS credentials defined. They can be defined either by environment varibales e.g. runnign these commands from command line

```
export  AWS_ACCESS_KEY_ID=*your key id here*
export  AWS_SECRET_ACCESS_KEY=*your key here*
```
or by creating a `$HOME/.aws/credentials` file where you store this credentials like this.

```
[default]
aws_access_key_id=*your key id here*
aws_secret_access_key=*your key here*
```

* The next step to take is build the AMI image.Run the following command

```
packer build template.json
```

* At the end of the packer log you will se a AMI ID. It will look something like this.

  * Make sure to go in the .kitchen.yml file and substitute the image_id with the id you receive from the packer log.

```
us-east-2: ami-0002d12f329b7aab4
```



* Now you can run the kitchen test

```
bundle exec kitchen converge
```
This will show the that the machine is being created

```
    ❯ bundle exec kitchen converge
-----> Starting Test Kitchen (v2.3.4)
-----> Creating <default-aws-nginx64>...
       If you are not using an account that qualifies under the AWS
free-tier, you may be charged to run these suites. The charge
should be minimal, but neither Test Kitchen nor its maintainers
are responsible for your incurred costs.

       Created automatic security group sg-0b02fa24b7093b4bc
       Created automatic key pair kitchen-defaultawsnginx64-yhalachev-YordansMacBookProloca-2020-02-19T15:26:56Z-3m4d62h9
       Instance <i-0e9e772f71bb618a9> requested.
       Polling AWS for existence, attempt 0...
       Attempting to tag the instance, 0 retries
       EC2 instance <i-0e9e772f71bb618a9> created.
       Waited 0/300s for instance <i-0e9e772f71bb618a9> volumes to be ready.
       Waited 0/300s for instance <i-0e9e772f71bb618a9> to become ready.
       Waited 5/300s for instance <i-0e9e772f71bb618a9> to become ready.
       Waited 10/300s for instance <i-0e9e772f71bb618a9> to become ready.
       Waited 15/300s for instance <i-0e9e772f71bb618a9> to become ready.
       Waited 20/300s for instance <i-0e9e772f71bb618a9> to become ready.
       Waited 25/300s for instance <i-0e9e772f71bb618a9> to become ready.
       EC2 instance <i-0e9e772f71bb618a9> ready (hostname: ec2-18-221-207-10.us-east-2.compute.amazonaws.com).
       Waiting for SSH service on ec2-18-221-207-10.us-east-2.compute.amazonaws.com:22, retrying in 3 seconds
       Waiting for SSH service on ec2-18-221-207-10.us-east-2.compute.amazonaws.com:22, retrying in 3 seconds
       [SSH] Established
       Finished creating <default-aws-nginx64> (0m49.09s).
-----> Converging <default-aws-nginx64>...
       Preparing files for transfer
       Preparing script
       No provisioner script file specified, skipping
       Transferring files to <default-aws-nginx64>
       Downloading files from <default-aws-nginx64>
       Finished converging <default-aws-nginx64> (0m0.53s).
-----> Test Kitchen is finished. (0m50.57s)
```

```
bundle exec kitchen verify
```
This will test if the packages are installed

```
    ❯ bundle exec kitchen verify
-----> Starting Test Kitchen (v2.3.4)
-----> Setting up <default-aws-nginx64>...
       Finished setting up <default-aws-nginx64> (0m0.00s).
-----> Verifying <default-aws-nginx64>...
       Loaded tests from {:path=>".Users.yhalachev.repos.nginx-aws-box.test.integration.default"} 

Profile: tests from {:path=>"/Users/yhalachev/repos/nginx-aws-box/test/integration/default"} (tests from {:path=>".Users.yhalachev.repos.nginx-aws-box.test.integration.default"})
Version: (not specified)
Target:  ssh://ubuntu@ec2-18-221-207-10.us-east-2.compute.amazonaws.com:22

  System Package wget
     ✔  is expected to be installed
  System Package nginx
     ✔  is expected to be installed
  Service nginx
     ✔  is expected to be enabled
     ✔  is expected to be running

Test Summary: 4 successful, 0 failures, 0 skipped
       Finished verifying <default-aws-nginx64> (0m7.57s).
-----> Test Kitchen is finished. (0m8.50s)
```


```
bundle exec kitchen destroy
```

This will destroy the created machine.

```
    bundle exec kitchen destroy
-----> Starting Test Kitchen (v2.3.4)
-----> Destroying <default-aws-nginx64>...
pass options to configure the waiter; yielding the waiter is deprecated
       Waited 0/300s for instance <i-0e9e772f71bb618a9> to terminate.
       Waited 5/300s for instance <i-0e9e772f71bb618a9> to terminate.
       Waited 10/300s for instance <i-0e9e772f71bb618a9> to terminate.
       Waited 15/300s for instance <i-0e9e772f71bb618a9> to terminate.
       Waited 20/300s for instance <i-0e9e772f71bb618a9> to terminate.
       EC2 instance <i-0e9e772f71bb618a9> destroyed.
       Removing automatic security group sg-0b02fa24b7093b4bc
       Removing automatic key pair kitchen-defaultawsnginx64-yhalachev-YordansMacBookProloca-2020-02-19T15:26:56Z-3m4d62h9
       Finished destroying <default-aws-nginx64> (0m24.37s).
-----> Test Kitchen is finished. (0m25.32s)
```


