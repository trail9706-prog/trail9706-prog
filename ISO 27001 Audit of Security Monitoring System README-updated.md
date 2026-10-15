<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Build a Security Monitoring System

**Project Link:** [View Project](https://nextwork.ai/projects/a6948fcf-cdce-53e8-9037-892917ce7585)

**Author:** Katrail Harris  
**Email:** trail9706@gmail.com

---

![Image](https://nextwork.ai/overjoyed_violet_shy_tieke/uploads/a6948fcf-cdce-53e8-9037-892917ce7585_reghtjy)

## Introducing Today's Project!

In this project, I will demonstrate how to set up a monitoring system in AWS using CloudTrail, CloudWatch and SNS! I'm doing this project to learn how security and monitoring services in AWS work and to have a working system that actually sends me email.

### Tools and concepts

Services I used were CloudTrail, which logs AWS API activity; S3, which stores CloudTrail logs; Secrets Manager, which stores and manages secrets; SNS, which sends alarm notifications; and CloudWatch, which detects secret access and alerts on suspicious activity. I also learned how to troubleshoot notifications. These are the key concepts I learned in this project.

### Project reflection

This project took me approximately 3 hours. The most challenging part was the time it took to complete. The most rewarding part was finishing it, because even though it took 3 hours, I enjoyed every minute of it.

## Create a Secret

AWS Secrets Manager helps you protect secrets, which are passwords, API keys, credentials and sensitive information. Instead of storing important credentials in code (a no no!) or sharing them via email (double no no!), you can tuck them safely away in Secrets Manager.

To set up for my project, I created a secret called KatsTopSecretInfo in AWS Secrets Manager. This secret is a string that contains a hot take from me. It mentions that I need 1 cup of coffee a day to function.

![Image](https://nextwork.ai/overjoyed_violet_shy_tieke/uploads/a6948fcf-cdce-53e8-9037-892917ce7585_o5p6q7r8)

## Set Up CloudTrail

AWS CloudTrail is a monitoring service - think of it as an activity recorder throughout your AWS account. It documents every action taken, like who did what, when they did it, and where they did it from. This continuous recording is super valuable for security (spotting unusual activity), troubleshooting (figuring out what changed when something breaks), and meeting compliance requirements (proving you're following the rules).

CloudTrail captures different types of events, each showing you a different view into what's happening in your AWS account:
Management events: These show you admin actions that configure your AWS resources - creating an EC2 instance, updating a security group, or in our case, accessing a secret. We're focusing on these in our project since secret access gets recorded here. Data events: These track high-volume actions that operate ON your resources rather than creating or configuring them - like uploading a file to S3 or running a Lambda function. Insights events: These detect unusual patterns in your management events, like someone suddenly creating 100x more IAM users than normal. Network activity events: These track network-related activities, like changes to your VPC configuration or traffic to a subnet.

### Read vs Write Activity

Read API activity happens when someone views but doesn't change anything. For example, listing your S3 buckets, describing your EC2 instances, or in our project, viewing (but not changing) metadata about a secret. Write API activity occurs when changes happen - creating, deleting, modifying resources, or even retrieving the value of a secret (which is what we want to monitor).

## Verifying CloudTrail

I retrieved the secret in two ways. First, I navigated to AWS Secrets Manager through the search bar. On the secret details page, I scrolled down to the Overview section and chose "Retrieve secret value," which opened the actual content of my secret. This triggers what's called a GetSecretValue API call to AWS. It's this specific API call that I want to monitor with CloudTrail, because it represents someone reading my secret. The second way I retrieved my secret was by typing in a command in the CLI to generate a secret access event.

To analyze my CloudTrail events, i.e. to see the event where I retrieved my secret's value, I visited the Event history in CloudTrail. I found that there was a GetSecretValue event tracked, regardless of whether I retrieved the secret over the CLI or over the console. This tells me that CloudTrail can definitely see and track when someone opens the Secrets Manager secret.

![Image](https://nextwork.ai/overjoyed_violet_shy_tieke/uploads/a6948fcf-cdce-53e8-9037-892917ce7585_s8t9u0v1)

## CloudWatch Metrics

Amazon CloudWatch Logs is a service that helps bring together logs from different AWS services, including CloudTrail, for visibility, troubleshooting, and analysis.

It's especially powerful because once logs are in CloudWatch, you can create alerts based on specific patterns (such as someone accessing your secret), visualize trends, or trigger automated responses.

CloudTrail's Event History is useful for quickly reading management events that happened in the last 90 days, while CloudWatch Logs are better for combining and analyzing logs from different sources, accessing logs for longer than 90 days, and advanced filtering.

Metric value is what gets recorded when our filter spots a match in the logs. I'm setting it to [1] so that each time someone accesses our secret, the counter increases by exactly one. The Default value is what gets recorded when our filter doesn't find any matches during a given time period. I'm setting it to [0] so that time periods with no secret access show up as zero on our charts, rather than not showing up at all. This gives us a complete picture - we can see both when access happened AND when it didn't.

![Image](https://nextwork.ai/overjoyed_violet_shy_tieke/uploads/a6948fcf-cdce-53e8-9037-892917ce7585_a9b0c1d2)

## CloudWatch Alarm

The CloudWatch alarm threshold is when the alarm should trigger. I'm setting up a static threshold so that my alarm goes off when the SecretIsAccessed metric is greater than or equal to 1 in a 5-minute period. This is a very sensitive setting (i.e. it's easy for this alarm to go off) - which is great for high-security scenarios! In a real production environment, you might adjust this depending on how sensitive your secret is. For instance, if it's normal for my application to access a secret 10 times every 5 minutes, I might set a higher threshold to only alert on unusual access patterns.

An SNS (Simple Notification Service) topic is like a broadcast channel for your notifications. First, you create the channel (aka topic), then you invite subscribers (such as your email), and finally, you send messages to the topic. SNS automatically delivers that message to all subscribers. What I like about this approach is flexibility - you could start by just emailing yourself about security alerts, but later add a text message notification, trigger an automated response via Lambda, or even integrate with your team's Slack channel - all without changing how the alert is generated, just by adding more subscribers to your topic.

AWS requires email confirmation because otherwise anyone could subscribe an address to an SNS topic and start sending it messages. Confirmation helps prevent unwanted subscriptions for recipients (i.e. people who are receiving those emails).

![Image](https://nextwork.ai/overjoyed_violet_shy_tieke/uploads/a6948fcf-cdce-53e8-9037-892917ce7585_fsdghstt)

## Troubleshooting Notification Errors

To test my monitoring system, I enabled SNS notifications in CloudTrail. The result was that I received notifications of events.

When troubleshooting the notification issues, I investigated every single part of my monitoring system. I checked whether CloudTrail was picking up on events as I accessed my secret, whether CloudTrail was sending logs to CloudWatch, whether the filter was reacting to the correct events, whether the alarm was triggering properly, and whether the alarm triggering would send an email.

I originally didn't get an email because the CloudWatch alarm was not configured properly. Instead of calculating the average number of times a secret was accessed in a time period, it should have used the SUM.

## Success!

Retrieve the secret again and wait up to 5 minutes for CloudTrail to log the event and CloudWatch to trigger the alarm. If the alarm doesn't activate, verify the metric filter and troubleshooting configurations.

![Image](https://nextwork.ai/overjoyed_violet_shy_tieke/uploads/a6948fcf-cdce-53e8-9037-892917ce7585_ageraergearge)

## Comparing CloudWatch with CloudTrail Notifications

In a project extension, I navigated to my KatsTopSecretInfo secret again and selected Retrieve secret value to access the secret. This triggered another secret access event that was captured by both my CloudWatch alarm and the direct CloudTrail notifications.

After enabling CloudTrail SNS notifications, my inbox received a huge wave of emails. This will continue to happen as long as I keep my trail's SNS notification setting on and there is any activity in my account. In terms of the usefulness of these emails, I thought I would receive notifications of my secret being accessed only. Instead I ended up with multiple log files and multiple notifications.

![Image](https://nextwork.ai/overjoyed_violet_shy_tieke/uploads/a6948fcf-cdce-53e8-9037-892917ce7585_d7e8f9g0)

---

## ISO/IEC 27001:2022 Control Mapping

This project is a small, working example of detection and monitoring controls. The table maps each component I built to the Annex A control it supports and the evidence an auditor would request to verify it.

| Component I built | Annex A control | What it demonstrates | Audit evidence to request |
|---|---|---|---|
| CloudTrail trail recording management events | 8.15 Logging | Activity is recorded with who, what, when, and from where | Trail configuration, event selectors, multi-region setting, sample log events |
| CloudWatch Logs integration (beyond the 90-day Event History) | 8.15 Logging | Logs are retained and searchable for longer than the default window | Log group retention setting, retention policy, sample queries |
| Metric filter on GetSecretValue | 8.16 Monitoring activities | Specific high-risk behavior is detected in the logs | Filter pattern, metric namespace and name, test results |
| CloudWatch alarm (Sum >= 1 in 5 minutes) | 8.16 Monitoring activities | Detected behavior crosses a defined threshold and raises an alert | Alarm configuration, statistic, period, threshold rationale, alarm history |
| SNS topic with confirmed email subscription | 5.24 Incident management planning and preparation, 5.25 Assessment and decision on events | Alerts reach a named recipient, so events can be assessed | Subscription list, confirmation status, who owns the mailbox, response procedure |
| Troubleshooting each link from event to log to filter to alarm to email | 5.26 Response to incidents | The detection chain was tested end to end | Test records showing a triggered alarm and a received notification |
| Secret stored in Secrets Manager | 5.17 Authentication information, 8.5 Secure authentication | Credentials are kept out of code and email | Secret inventory, access policies, rotation settings, access log review |
| CloudTrail logs delivered to S3 | 5.33 Protection of records | Logs are stored centrally and can be protected | Bucket policy, encryption settings, log file integrity validation, deletion protection |

**Not covered by this lab:** multi-account or multi-region trails, centralized log archiving, least-privilege access to the logs themselves, and a documented procedure for responding to alarms. In a real audit these gaps are often where findings arise.

## Sample Audit Finding (Illustrative)

This finding is based on a misconfiguration I hit and fixed during this project. It shows how I would document a similar issue during an audit.

**Finding title:** Alarm for secret access is configured with a statistic that does not reliably detect events

**Criteria:** ISO/IEC 27001:2022 Annex A 8.16 requires that networks, systems, and applications are monitored for anomalous behavior and that appropriate action is taken to evaluate potential information security incidents.

**Condition:** The CloudWatch alarm monitoring the `SecretIsAccessed` metric was configured to evaluate the Average statistic. When the secret was retrieved, no alert email was received, even though CloudTrail had logged the event and the metric filter had matched it.

**Cause:** The alarm statistic was set to Average instead of Sum. The metric is a count of events, so Sum is the appropriate statistic for detecting that an access occurred in the evaluation period. The configuration had not been tested end to end before being relied on.

**Effect:** Access to a sensitive secret could occur without any alert reaching the responsible person, giving a false assurance that monitoring was in place. The control looked implemented on paper but did not operate effectively.

**Recommendation:**
1. Change the alarm statistic to Sum for count-based metrics.
2. Test every alarm end to end after configuration or change, and keep the test record as evidence.
3. Add alarm testing to the change management checklist for monitoring controls.
4. Review other count-based alarms for the same misconfiguration.

**Status in this lab:** Corrected, then retested successfully with an alert email received after secret retrieval.

---

*Built with [NextWork](https://nextwork.ai) - [View this project](https://nextwork.ai/projects/a6948fcf-cdce-53e8-9037-892917ce7585)*
