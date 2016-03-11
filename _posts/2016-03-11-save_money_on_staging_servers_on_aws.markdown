---
layout: post
title:  "Save money on staging servers hosted on AWS"
date:   2016-03-11 19:35:35 +0530
categories: AWS
---
So we wanted reduce the AWS bill, first thing we did was to reduce the number of RDS instances and create different databases in the same instance. Next we could save by moving from on-demand to reserved for ec2-instance, which would at the least reduce 30% of per hour charge. But it made sense to move into reserved instances for production servers, but not for staging servers which were used only for internal testing and development, it did not make much sense because if we check for working hours per week its average say at 10-12 hours for 5 days a weeks, which is effectively about 2.5 to 3 days a week, so we are paying 60% extra for the staging servers. To optimize this we need to build a way to remove these servers when not in use.

AWS gives us an option to stop ec2-instances which are EBS backed and will charge only for the storage, which is minimal. But each time to stop instance we cannot go to the aws console, for which aws provides varias sdks and command line tools, we choose to use aws-ec2-cli. Just by saying `ec2-stop-instances <instance-id> --region <region>` we can stop instance and `ec2-start-instances <intance-id> --region <region>` we can start instance. So what we did was in the existing jenkins server, created a job with parameter `ACTION` with possible values `stop/start` and passing it to a custom script which loops over the instances ids and stops/starts the instances. First we need to setup the jenkins server with ec2-cli. Following are the steps:

{% highlight ruby %}
  cd /user/src
  wget http://s3.amazonaws.com/ec2-downloads/ec2-api-tools.zip
  mkdir /usr/local/ec2
  yum install unzip #if unzip not present
  uzip ec2-api-tools.zip -d /usr/local/ec2/
  ## in jenkins:jenkins user bashrc add uncommented stuff
  ## file $(which java)
  ## file /etc/alternatives/java
  # copy output from previous command. For me it was "/usr/lib/jvm/jre-1.8.0-openjdk.x86_64"
  export JAVA_HOME="output from file /etc/alternatives/java binary"
  export EC2_HOME=/usr/local/ec2/<unziped_ec2-cli_file_path> ## for me its was /usr/local/ec2/ec2-api-tools-1.7.5.1
  export AWS_ACCESS_KEY=<aws key>
  export AWS_SECRET_KEY=<aws secret key>
{%endhighlight%}

Above is the brief from the [AWS documentation][aws-ec2-cli-documentation]

Now your cli in the jenkins server is set, next is to create a job in jenkins which accepts a choice parameter `stop/start` action and stop/starts the servers. Following is the shell script which might help:

{% highlight ruby %}
#!/bin/sh

# ec2-start-instances <instance id> --region ap-southeast-1 -v ;; final command to be executed
instances=(space sperated instance ids) ##eg: (i-ava123 i-afavava i-ava1123)
case $1 in
  start )
    cmd="ec2-start-instances"
    ;;
  stop )
    cmd="ec2-stop-instances"
    ;;
  * )
    cmd="ec2-stop-instances"
esac

for instance in ${instances[@]}
do
  echo `$cmd $instance --region ap-southeast-1 -v`
done

{%endhighlight%}


Copied the following shell script to the jenkins server and created a new job with option parameter start/stop and build with the above shell script and argument as the `action start/stop` selected from the parameter.

{% highlight ruby %}
  /var/lib/jenkins/stop_start_server.sh $ACTION
{%endhighlight%}

Also set up a cron task in the jenkins server which calls the above script at 8 pm and shuts the servers, if any one is using them post 8 pm then they would go to jenkins UI and start the staging environment.

I hope something from the above random shit is helpful. Adios.


[aws-ec2-cli-documentation]: http://docs.aws.amazon.com/AWSEC2/latest/CommandLineReference/set-up-ec2-cli-linux.html#setting_up_ec2_command_linux

