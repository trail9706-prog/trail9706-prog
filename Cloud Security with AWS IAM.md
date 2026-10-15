<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Cloud Security with AWS IAM

**Project Link:** [View Project](https://nextwork.ai/projects/51a5fd24-7458-58a6-8653-9b90119a0e4a)

**Author:** Katrail Harris  
**Email:** trail9706@gmail.com

---

![Image](https://nextwork.ai/overjoyed_violet_shy_tieke/uploads/aws-security-iam_1c864649)

## Introducing Today's Project!

### Project overview

In this project, I will demonstrate my ability to create two EC2 Instances. One development instance and one production instance in AWS Cloud. Create IAM Policies, create IAM Users and User Groups and create an AWS Account Alias.

### Tools and concepts

Services I used were Amazon EC2 and AWS IAM.  Key concepts I learnt include IAM users, user groups, policies, account aliases, and root user account. I also learnt how to use the JSON policy works and how to use the Policy Simulator. How to launch instances, how to tag an instance, how to log in as another user.

### Project reflection

This project took me approximately 2 hours. The most challenging part was understanding the IAM policy since it was written in JSON and It contained multiple statements. It was most for me to ee "permission denied" when the intern tried to delete the production instance. My IAM access management worked.

## Tags

### What I did in this step

In this step, I will lauch two EC2 instances because I need to boost NEXTWORK's computing power. I'm expecting more users and traffic into my wevste over the next year or so. 

### Understanding tags

Tags are organizational tools that let's us label our resources. they are helpful for grouping, resources, cost allocation and applying policies for all resources with the same tags.

### My tag configuration

The tag I’ve used on my EC2 instances is called Env.which stands for Environment. The value I’ve assigned for my instances are production and development. 

![Image](https://nextwork.ai/overjoyed_violet_shy_tieke/uploads/aws-security-iam_2e0e5a5d)

## IAM Policies

### What I did in this step

In this step, I will use IAM policies to control the access level of a new NextWork intern because they should have access to the development environment {i.e. the development instance} but NOT the production environment.

### Understanding IAM policies

IAM Policies are like rules that determine who can do what in our AWS account.

### The policy I set up

For this project, I’ve set up a policy using JSON. I'm using policies today to control who has access to our production or environment instance.

### Policy effect

I’ve created a policy that allows the policy holder (i.e. the intern to have permission to do anything they want to any instance tagged with "development". They can also see information related for any instance, they're denied access to deleting or creating tags for any instance as well.  

### Understanding Effect, Action, and Resource

The Effect, Action, and Resource attributes of a JSON policy means whether or not the policy is allowing or denying action {i.e. Effect}; wht the policy holder can or cannot do {i.e. Action}; and the specific AWS resources that the policy relates to {i.e. Resource}.

## My JSON Policy

![Image](https://nextwork.ai/overjoyed_violet_shy_tieke/uploads/aws-security-iam_1c864649)

## Account Alias

### What I did in this step

In this step, I will set up an Account Alias, which is like a nickname for omy AWS account's console login. This is  because an account alias makes it simpler for our users to login. 

### Understanding account aliases

An account alias is simply a nickname for our AWS account. Instead of a long account ID, I can now reference my account alias instead. 

### Setting up my account alias

Creating an account alias took me 20 seconds. It's a simple configuration in the IAM dashboard. Now, my new AWS console sign-in URL uses my account alias instead of my account ID.

![Image](https://nextwork.ai/overjoyed_violet_shy_tieke/uploads/aws-security-iam_0eb4439b)

## IAM Users and User Groups

### What I did in this step

In this step, I will setup two IAM resources - IAM users and IAM user groups, This is because IAM users are like logins for people that want to access to our AWS account. While user groups are like folders to manager users that have the same level of access.

### Understanding user groups

IAM user groups are like folders that collect IAM users so that I can apply permission settings at the group level.

### Attaching policies to user groups

I attached the policy I created to this user group, which means any user created inside this group will automatically get the permissions attached to my NextWorkDevEnvironmentPolicy IAM Policy.

### Understanding IAM users

IAM users are people or entities that have access and can login to my AWS account.

## Logging in as an IAM User

### Sharing sign-in details

The first way is to email sign-in instructions to the user, while the second way is to download .csv file with the sign-in details inside. 

### Observations from the IAM user dashboard

Once I logged in as my IAM user, I noticed that the user is already denied access to panels on the main AWS console dashboard. This was because I only set up permissions to the development EC2 instance, so my intern wouldn't have access to anything else and even see anything else. 

![Image](https://nextwork.ai/overjoyed_violet_shy_tieke/uploads/aws-security-iam_6f2ab446)

## Testing IAM Policies

### What I did in this step

In this step, I will log in to my own AWS account as the intern and test access to the production and development instancs because I want to make sure my the intern doesn't have the ability to do anything that affects my production environment. 

### Testing policy actions

I tested my JSON IAM policy by attempting to stop both the development and the production instances. 

### Stopping the production instance

When I tried to stop the production instance I wass met with an error. This was because my production instance is tagged with the "production" label, which is outside of the  scope of the permission policy the I created. Interns are only are only allowed to do things to the development instance. 

![Image](https://nextwork.ai/overjoyed_violet_shy_tieke/uploads/aws-security-iam_0e7a9d6a)

### Stopping the development instance

Next, when I tried to stop the development instance we successfully saw the instance state change to Stopping and then Stopped. This was because the permission policy I created allows the intern {i.e. users the nextwork-dev-group} to stop intances. 

![Image](https://nextwork.ai/overjoyed_violet_shy_tieke/uploads/aws-security-iam_1811801c)

## IAM Policy Simulator

To extend my project, I'm going to test my permission policies in a safer and more controlled way using a tool called the IAM Policy Simulator. I'm doing this because having to stop instances and log into AWS accounts as other users are a bit disruptive. The Policy Simulator is a more sufficient way. 

### Understanding the IAM Policy Simulator

The IAM Policy Simulator is a tool that lets us simulate actions and test permission settings by defining a specific user/group/role and the action we want to test for. It's useful for savcing time when testing permission setting. No more logging into another user or actual stopping resources. Yay!

### How I used the simulator

I set up a simulation for whether the dev user group has persission to stop instances or delete Tag's. The results  were denied for both. I had to adjust the scope of the EC2 instances to ones that are tagged with "development". Once I applied that tag permission wa allowed. 

![Image](https://nextwork.ai/overjoyed_violet_shy_tieke/uploads/aws-security-iam_069d8a621)

---

*Built with [NextWork](https://nextwork.ai) - [View this project](https://nextwork.ai/projects/51a5fd24-7458-58a6-8653-9b90119a0e4a)*
