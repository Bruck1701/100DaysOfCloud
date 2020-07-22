
![ELB-ASG-summary](https://user-images.githubusercontent.com/17711277/88171200-385ea280-cc1f-11ea-82db-bc7aeff50963.jpg)

# Auto Scaling Groups Set up 

## Introduction

AWS Auto Scaling is a service that monitors and adjusts the response capacity of your applications to keep them with a steady performance at the lowest cost.
You can build scaling plans for resources including Amazon EC2 instances and Spot Fleets, Amazon ECS tasks, Amazon DynamoDB tables and indexes, and Amazon Aurora Replicas.

## Prerequisite

In order to follow this mini-tutorial, it is necessary to know how to create and configure:
- EC2 instances
- Security Groups
- Application Load Balancer

and preferably you would have your security groups and Application Load Balancer already set up!


## Use Case
 
 Imagine a situation where your web app has been posted on social media and overnight it has become the new sensation. Every day your app is bombarded with a surge of users, but naturally this demand is not constant, because people still need to sleep. How would you provision your servers for good response time when it is being demanded by thousands of users? You can always pay for more computers on the cloud, but some times, in the middle of the night, they will be idle just seating there and you are being charged anyway to keep them idle. The solution would be to dynamically change the number of instances needed to provide the service, right? Increasing or decreasing the number of available computers depending on the number of users. That is what Auto Scaling is all about! 
 

### Step 1 — First things first 

Disclaimer:  AWS changes the layout of their dashboards from time to time, so the exact layout of the buttons and links may change, but the steps needed to build it, will likely remain the same or pretty close to these steps!

Getting into the Create Auto Scaling group page.


![createAutoScalingGroup](https://user-images.githubusercontent.com/17711277/88183181-6cdb5a00-cc31-11ea-81fe-8dc340b062e5.jpg)


Then after you click to create the ASG, you can choose between launch template or a launch configuration. Launch template is the newer version of the service and allows you to launch a spot fleet of instances (https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/spot-fleet.html). Launch configuration, on the other hand, allows you to specify only one instance type. 
So let's create it an ASG with launch template, since it is the newer version and it is the default choice.
First name your ASG and click on to create a launch template (assuming you don't have one already of course).

![createLaunchTemplate](https://user-images.githubusercontent.com/17711277/88184386-ea539a00-cc32-11ea-91ca-befa5eb188f0.jpg)



### Step 2 - Create a Launch Template

The launch template is used as a set of rules on how ASG will create the new resources - in our case, the new EC2 instances.

First you name your template, add some description, and for this tutorial I unchecked the Provide guidance checkbox.


![launchTemplateName](https://user-images.githubusercontent.com/17711277/88184464-09eac280-cc33-11ea-9883-a990a134e37d.jpg)


To keep within the Free tier, you can choose Amazon Linux 2 as AMI and t3.micro for instance type. 
For the Key pair to login, you select your key pair, or you can create a new key, as you could do when creating a new EC2 instance.

![chooseAMI](https://user-images.githubusercontent.com/17711277/88200869-06613680-cc47-11ea-8289-e743f8d2276d.jpg)

For network settings, be sure to choose VPC and then to select the the Security Groups that your EC2 that allow you to connect to the EC2 (normally by SSH) and one that allows your Load Balancer to send traffic to your instance. For this mini guide, I'm running a simple web server on port 80. So I keep one Security Group for the SSH port (MySG) and one for the Load Balancer (HTTP SG).


![networkSettings](https://user-images.githubusercontent.com/17711277/88201321-9901d580-cc47-11ea-80a1-593ebd8b7331.jpg)

There are a lot of other optional settings to configure in order to fine tune your ASG. However, besides these that we already set up, you will mostly need to set up the user data in Advanced details to install and configure your app in the new instance. In this example I just added some information to index.html in order to check if the instance is coming up correctly.


![userData](https://user-images.githubusercontent.com/17711277/88201824-3f4ddb00-cc48-11ea-8926-4f01fe9a2248.jpg)


After you added all the setting of your launch template, you are set to continue with the ASG configuration.


![templateReady](https://user-images.githubusercontent.com/17711277/88201985-7cb26880-cc48-11ea-887d-3eec55f65e85.jpg)


Go back to the Create launch group page, refresh the launch template button and your template should be available. Once you check that all the information on the template is correct, click next to go to configure settings.


![launchTemplateReady](https://user-images.githubusercontent.com/17711277/88202088-a53a6280-cc48-11ea-97df-11801c82efee.jpg)


### Step 3 - Configure settings

Purchase options: You can either choose "On-Demand or Spot instance" or a combination of both. From an architectural point of view, it will depend on the application being served, but the idea is that you can tune your auto scaling for the best cost over availability. For simplicity's sake of this guide, I chose "Adhere to launch template". 
For the networking setting you can choose in which subnets (AZs) you would like your ASG to deploy your EC2 Instances into. 


![purchaseOption](https://user-images.githubusercontent.com/17711277/88202545-53dea300-cc49-11ea-9c09-d9bad9b7eeb0.jpg)


### Step 4 - Advanced options.

Although it says that it is optional to set this up, I strongly suggest that you should configure this part to have your Load Balancer helping with the Auto Scaling! 
So, you choose your Load balancer and in which Target Group should your new instance be registered. 

![loadBalancingOption](https://user-images.githubusercontent.com/17711277/88203046-fd259900-cc49-11ea-90a0-5dfcfb65ad30.jpg)

## Health Checks

The EC2 Checkbox means that if the EC2 instance fails (operating system crash, for example), ASG will replace it. 
The ELB health check means that if the Load Balancer check from within the target group fails (the app stops responding), the Auto Scaling group will terminate that instance and create a new one automatically!
For this guide, I'll skip the additional settings.


### Step 5 - Group Size and Scaling policies

Here you decide how many instances you want running at a any given time and the maximum number of instances. Also you have the chance to set up your scaling policy, the strong suit of Auto Scaling!


![groupSize](https://user-images.githubusercontent.com/17711277/88207318-e2562300-cc4f-11ea-8a7e-2b4875e7a43a.jpg)


### Step 6 - Understanding scaling policies
In scaling policies is where you configure the rules that will make your ASG scale out (increase the number of instances) and scale in (terminate idle instances).
There are basically three types of scaling:
* Target Tracking Scaling
    * Most simple to set-up. I.e : The average CPU of all instances managed by ASG must be less than 40%
* Simple/Step Scaling
    * Using Cloudwatch to a specific trigger. You can also specify how many instances you want to be added when the alarm goes off. When using this type of scaling you also need to add the scale in rules as well!
* Scheduled Actions
    * When you know the pattern of demand of your app. For example, everyday from 6PM to 10PM your app receives at least three times the number of connections. In this case you can schedule your Scale out and scale in actions. 
Another important concept in Auto Scaling is cooldown. The Cooldown period ensures that your ASG won't get into a loop of creating and terminating instances one after the other. The cooldown forces the ASG to wait some time before launching or terminating instances when a previous scaling activity has just been executed.

When you create your scaling Policy from the ASG create page, you can only set the Target Tracking Scaling. If you want to set up Simple/Step Scaling or Scheduled Actions. You can choose none here and add the policy later once the ASG has been created. For this example, I set up target tracking for CPU usage.
![scalingPolicy](https://user-images.githubusercontent.com/17711277/88209452-09622400-cc53-11ea-8110-04e0f4d75575.jpg)

Once your scaling policy is set, you are practically good to go! You can also configure your ASG to send notifications to SNS topics whenever a scale operation is executed, but I won't go into these here (maybe on another tutorial). And as with a lot of other services, you can add tags to your ASG.
After you launch your ASG, if you don't have any EC2 instances running into the target group of the load balancer connected to the the ASG, the ASG will initialize the minimum number of instances that you previously defined. You can check this on the activity history of your ASG

![activityHistory](https://user-images.githubusercontent.com/17711277/88211312-d5d4c900-cc55-11ea-8350-755903b72376.jpg)


![oneInstance](https://user-images.githubusercontent.com/17711277/88211486-10d6fc80-cc56-11ea-916a-d3399a4d33b1.jpg)


### Step 7 - Testing your scaling policy

Cool! Now the ASG is running, but before we finish, it is a good idea to test if the scaling policy is working properly. Since we set up a target tracking scaling for CPU usage we can play around with the CPU of our EC2 Instance. Since we configure the Security Group to enable SSH access, we can connect to our running instance. 

We can check how many processors we have with 

```
cat /proc/cpuinfo
```


![cpuInfo](https://user-images.githubusercontent.com/17711277/88218775-ceff8380-cc60-11ea-8481-8b54eaf732d2.jpg)


So we have 1 processor available on our instances and it happens to be a  Intel Xeon Platinum 8175M @ 2.50GHz. When you google this model you get that it has 2 logical cores per processor. In this case, if we want to get our instance spike its CPU, we need to get both of them busy. The simplest way is to run two instances of:

```
$  yes > /dev/null &
```


![runningYes](https://user-images.githubusercontent.com/17711277/88213076-8ba11700-cc58-11ea-8282-f6bb013a3591.jpg)

When you run the "top" command you can see the CPU usage going upwards. 

![yes](https://user-images.githubusercontent.com/17711277/88218777-cf981a00-cc60-11ea-9405-455cb362b483.jpg)

You can also check on CloudWatch metrics on the instance monitoring tab:

![cloudWatch](https://user-images.githubusercontent.com/17711277/88212099-fa7d7080-cc56-11ea-9af5-165688a8d3fb.jpg)

After a few moments, you can check in the activity history tab on your Auto Scaling Group that two additional instances were created to help with the high demand detected by the target tracking.

![trackHistory](https://user-images.githubusercontent.com/17711277/88212120-049f6f00-cc57-11ea-835d-13ee24591b0a.jpg)


These are the resulting three instances created to keep the average CPU usage under the threshold of 40%.

![threeinstances](https://user-images.githubusercontent.com/17711277/88212145-0f5a0400-cc57-11ea-85d0-0879749fe8a0.jpg)


Great! It is working as expected! Don't forget to kill your running instances of "yes".

![killYes](https://user-images.githubusercontent.com/17711277/88214539-a4123100-cc5a-11ea-8cf7-cd4c2e408056.jpg)

## ☁️ Cloud Outcome

If everything worked out, you should have an ASG connected to an Application Load Balancer with a working scaling policy. Yay! 

## Next Steps

One possible next step is to configure AWS CLI to be able to change these settings from the CLI.


