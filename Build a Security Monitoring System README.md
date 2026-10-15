<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Build a Security Monitoring System

**Project Link:** [View Project](https://nextwork.ai/projects/a6948fcf-cdce-53e8-9037-892917ce7585)

**Author:** Katrail Harris  
**Email:** trail9706@gmail.com

---

![Image](https://nextwork.ai/overjoyed_violet_shy_tieke/uploads/a6948fcf-cdce-53e8-9037-892917ce7585_reghtjy)

## Introducing Today's Project!

In this project, I will demonstrate how to setup a monitoring system in AWS using CloudTrail, CloudWatch and SNS! I'm doing this project to learn how security and monitoring services in AWS work plus, have a working system that actually sends me email also.

### Tools and concepts

Services I used were CloudTrail - which logs AWS API activity; S3 - Which stores CloudTrail logs; Secrets Manager - Which stores and manages secrets; SNS - Which sends alarm notifications; Security Monitoring - which detects secret access and alearts on suspicious activity; and How to troubleshoot notifications. These are the key concepts I learnt in this project

### Project reflection

This project took me approximately 3 hours. The most challenging part was the time that it took to complete this project. It was most rewarding to this entire project  when I did because even though it took me 3 hours to complete, I enjoyed every minute of it. 

## Create a Secret

AWS Secrets Manager helps you protect secrets, which are passwords, API keys, credentials and sensitive information. Instead of storing important credentials in code (a no no!) or sharing them via email (double no no!), you can tuck them safely away in Secrets Manager.

To set up for my project, I created a secret called KatsTopSecretInfo in AWS Secrets Manager. This secret is a string that contains hot take from me. It mentions that I need 1 cup of coffee a day to function. 

![Image](https://nextwork.ai/overjoyed_violet_shy_tieke/uploads/a6948fcf-cdce-53e8-9037-892917ce7585_o5p6q7r8)

## Set Up CloudTrail

AWS CloudTrail is a monitoring service - think of it as an activity recorder throughout your AWS account. It documents every action taken, like who did what, when they did it, and where they did it from.This continuous recording is super valuable for security (spotting unusual activity), troubleshooting (figuring out what changed when something breaks), and meeting compliance requirements (proving you're following the rules).

CloudTrail captures different types of events, each showing you a different view into what's happening in your AWS account:
Management events: These show you admin actions that configure your AWS resources - creating an EC2 instance, updating a security group, or in our case, accessing a secret. We're focusing on these in our project since secret access gets recorded here. Data events: These track high-volume actions that operate ON your resources rather than creating or configuring them - like uploading a file to S3 or running a Lambda function. Insights events: These detect unusual patterns in your management events, like someone suddenly creating 100x more IAM users than normal. Network activity events: These track network-related activities, like changes to your VPC configuration or traffic to a subnet.

### Read vs Write Activity

Read API activity happens when someone views but doesn't change anything. For example, listing your S3 buckets, describing your EC2 instances, or in our project, viewing (but not changing) metadata about a secret. Write API activity occurs when changes happen - creating, deleting, modifying resources, or even retrieving the value of a secret (which is what we want to monitor).

## Verifying CloudTrail

I retrieved the secret in two ways: First through navigating to AWS Secrets Manager through the search bar. When I arrive on the secret details page, scroll down to the Overview section and choosing "Retreive secret value". When I click Retreive secret value, I opened the actual content of my secret. This triggers what's called a GetSecretValue API call to AWS. It's this specific API call that I want to monitor with CloudTrail, because it represents someone reading my secret. The second way to retreive my secret is by typing in a JSON string command to retreive a secret access event. 

To analyze my CloudTrail events i.e. sew the event where I put my secret's value, I visited the event history in CloudTrail.  I found that there was GetSecretsValue event tracked. Regardless of whether we did this over the CLI or over the console. This tells me that CloudTrail can definitely see and track when someone opens the Secrets Manager Key. 

![Image](https://nextwork.ai/overjoyed_violet_shy_tieke/uploads/a6948fcf-cdce-53e8-9037-892917ce7585_s8t9u0v1)

## CloudWatch Metrics

Amazon CloudWatch Logs is a service that helps bring together logs from different AWS services, including CloudTrail, for visibility, troubleshooting, and analysis.

It's especially powerful because once logs are in CloudWatch, you can create alerts based on specific patterns (such as someone accessing your secret), visualize trends, or trigger automated responses.

CloudTrail's Event History is useful for quickly reading management events that happened in the last 90 day, while CloudWatch Logs are better for combining and anlyzing logs from different sources, accessing logs for longer that 90 days and advanced filtering.

Metric value is what gets recorded when our filter spots a match in the logs. I'm setting it to [1 ] so that each time someone accesses our secret, the counter increases by exactly one. The Default value is what gets recorded when our filter doesn't find any matches during a given time period. I'm setting it to [0] so that time periods with no secret access show up as zero on our charts, rather than not showing up at all. This gives us a complete picture - we can see both when access happened AND when it didn't.

![Image](https://nextwork.ai/overjoyed_violet_shy_tieke/uploads/a6948fcf-cdce-53e8-9037-892917ce7585_a9b0c1d2)

## CloudWatch Alarm

The CloudWatch alarm threshold is when the alarm should trigger. I';m setting up a static threshold so that my alarm goes off when the SecretIsAccessed metric is greater than or equal to 1 in a 5-minute period. This is a very sensitive setting (i.e. it's easy for this alarm to go off) - which is great for high-security scenarios! In a real production environment, you might adjust this depending on how sensitive your secret is. For instance, if it's normal for my application to access a secret 10 times every 5 minutes, I might set a higher threshold to only alert on unusual access patterns.

An SNS (Simple Notification Service) topic is like a broadcast channel for your notifications. First, you create the channel aka (topic), then you invite subscribers (such as your email), and finally, you send messages to the topic. SNS automatically delivers that message to all subscribers. What I like about this approach is flexibility - you could start by just emailing yourself about security alerts, but later add a text message notification, trigger an automated response via Lambda, or even integrate with your team's Slack channel - all without changing how the alert is generated, just by adding more subscribers to your topic.

AWS requires email confirmation because it would automatically start enabling addresses that I subscribe to an SNS topic. This helps prevent any unwanted  subscriptions for recepients {i.e. people who are receiving those emails}.

![Image](https://nextwork.ai/overjoyed_violet_shy_tieke/uploads/a6948fcf-cdce-53e8-9037-892917ce7585_fsdghstt)

## Troubleshooting Notification Errors

To test my monitoring system, I enabled SNS notifications in CloudTrail. The results were that I received notifications of events.

When troubleshooting the notification issues, I investigated every single part of my monitoring system. I investigated whether CloudTrail is picking up on events that are happening as I access my secret, whether CloudTrail is sending logs to CloudWatch, if the fiilter was accidentaly reacting to the correct events, if the alarm was triggering properly, if the triggering of the alarm would send an email or not. 

I orginally didn't get an email at first because CloudWatch was not configured properly. instead of calculating the average number of times a secret was accessed in a time period it should have been the SUM.

## Success!

Retrieve the secret again and wait up to 5 minutes for CloudTrail to log the event and CloudWatch to trigger the alarm. If the alarm doesn’t activate, verify the metric filter and troubleshooting configurations.

![Image](https://nextwork.ai/overjoyed_violet_shy_tieke/uploads/a6948fcf-cdce-53e8-9037-892917ce7585_ageraergearge)

## Comparing CloudWatch with CloudTrail Notifications

In a project extension, I navigated to my TopSecretInfo secret again. I selected Retrieve secret value to access the secret.
This triggered another secret access event that was captured by both my CloudWatch Alarm and the direct CloudTrail notifications.

After enabling CloudTrail SNS notifications, my inbox received huge wave of emails. This will continue to happen as long as I keep my trail's SNS notification setting on, and if there is any activity in my account. In terms of the usefulness of these emails, I thought Iwould receive notifications of my secret being accessed only. Instead I end up with multiple log files and multiple notifications.

![Image](https://nextwork.ai/overjoyed_violet_shy_tieke/uploads/a6948fcf-cdce-53e8-9037-892917ce7585_d7e8f9g0)

---

*Built with [NextWork](https://nextwork.ai) - [View this project](https://nextwork.ai/projects/a6948fcf-cdce-53e8-9037-892917ce7585)*
