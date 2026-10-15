<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Cloud Security with AWS IAM

**Project Link:** [View Project](https://nextwork.ai/projects/51a5fd24-7458-58a6-8653-9b90119a0e4a)

**Author:** Katrail Harris  
**Email:** trail9706@gmail.com

---

![Image](https://nextwork.ai/overjoyed_violet_shy_tieke/uploads/aws-security-iam_1c864649)

## Introducing Today's Project!

### Project overview

In this project, I will demonstrate my ability to create two EC2 instances, one development instance and one production instance, in the AWS Cloud. I will also create IAM policies, IAM users and user groups, and an AWS account alias.

### Tools and concepts

Services I used were Amazon EC2 and AWS IAM. Key concepts I learned include IAM users, user groups, policies, account aliases, and the root user account. I also learned how JSON policies work and how to use the Policy Simulator, plus how to launch instances, how to tag an instance, and how to log in as another user.

### Project reflection

This project took me approximately 2 hours. The most challenging part was understanding the IAM policy, since it was written in JSON and contained multiple statements. It was most rewarding for me to see "permission denied" when the intern tried to stop the production instance. My access management worked.

## Tags

### What I did in this step

In this step, I will launch two EC2 instances because I need to boost NEXTWORK's computing power. I'm expecting more users and traffic on my website over the next year or so.

### Understanding tags

Tags are organizational tools that let us label our resources. They are helpful for grouping resources, cost allocation, and applying policies to all resources with the same tags.

### My tag configuration

The tag I've used on my EC2 instances is called Env, which stands for Environment. The values I've assigned to my instances are production and development.

![Image](https://nextwork.ai/overjoyed_violet_shy_tieke/uploads/aws-security-iam_2e0e5a5d)

## IAM Policies

### What I did in this step

In this step, I will use IAM policies to control the access level of a new NextWork intern, because they should have access to the development environment (i.e. the development instance) but NOT the production environment.

### Understanding IAM policies

IAM policies are like rules that determine who can do what in our AWS account.

### The policy I set up

For this project, I've set up a policy using JSON. I'm using policies today to control who has access to our production or development instances.

### Policy effect

I've created a policy that allows the policy holder (i.e. the intern) to have permission to do anything they want to any instance tagged with "development." They can also view information about any instance, but they're denied access to deleting or creating tags for any instance.

### Understanding Effect, Action, and Resource

The Effect, Action, and Resource attributes of a JSON policy mean whether the policy is allowing or denying an action (i.e. Effect); what the policy holder can or cannot do (i.e. Action); and the specific AWS resources that the policy relates to (i.e. Resource).

## My JSON Policy

![Image](https://nextwork.ai/overjoyed_violet_shy_tieke/uploads/aws-security-iam_1c864649)

## Account Alias

### What I did in this step

In this step, I will set up an account alias, which is like a nickname for my AWS account's console login. An account alias makes it simpler for our users to log in.

### Understanding account aliases

An account alias is simply a nickname for our AWS account. Instead of a long account ID, I can now reference my account alias.

### Setting up my account alias

Creating an account alias took me 20 seconds. It's a simple configuration in the IAM dashboard. Now, my new AWS console sign-in URL uses my account alias instead of my account ID.

![Image](https://nextwork.ai/overjoyed_violet_shy_tieke/uploads/aws-security-iam_0eb4439b)

## IAM Users and User Groups

### What I did in this step

In this step, I will set up two IAM resources: IAM users and IAM user groups. IAM users are like logins for people who want access to our AWS account, while user groups are like folders for managing users who have the same level of access.

### Understanding user groups

IAM user groups are like folders that collect IAM users so that I can apply permission settings at the group level.

### Attaching policies to user groups

I attached the policy I created to this user group, which means any user created inside this group will automatically get the permissions attached to my NextWorkDevEnvironmentPolicy IAM policy.

### Understanding IAM users

IAM users are people or entities that have access and can log in to my AWS account.

## Logging in as an IAM User

### Sharing sign-in details

The first way is to email sign-in instructions to the user, while the second way is to download a .csv file with the sign-in details inside.

### Observations from the IAM user dashboard

Once I logged in as my IAM user, I noticed that the user was already denied access to panels on the main AWS console dashboard. This was because I only set up permissions for the development EC2 instance, so my intern wouldn't have access to, or even see, anything else.

![Image](https://nextwork.ai/overjoyed_violet_shy_tieke/uploads/aws-security-iam_6f2ab446)

## Testing IAM Policies

### What I did in this step

In this step, I will log in to my own AWS account as the intern and test access to the production and development instances, because I want to make sure the intern doesn't have the ability to do anything that affects my production environment.

### Testing policy actions

I tested my JSON IAM policy by attempting to stop both the development and the production instances.

### Stopping the production instance

When I tried to stop the production instance, I was met with an error. This was because my production instance is tagged with the "production" label, which is outside the scope of the permission policy I created. Interns are only allowed to do things to the development instance.

![Image](https://nextwork.ai/overjoyed_violet_shy_tieke/uploads/aws-security-iam_0e7a9d6a)

### Stopping the development instance

Next, when I tried to stop the development instance, I successfully saw the instance state change to Stopping and then Stopped. This was because the permission policy I created allows the intern (i.e. users in the nextwork-dev-group) to stop instances.

![Image](https://nextwork.ai/overjoyed_violet_shy_tieke/uploads/aws-security-iam_1811801c)

## IAM Policy Simulator

To extend my project, I'm going to test my permission policies in a safer and more controlled way using a tool called the IAM Policy Simulator. I'm doing this because having to stop instances and log in to AWS accounts as other users is a bit disruptive. The Policy Simulator is a more efficient way.

### Understanding the IAM Policy Simulator

The IAM Policy Simulator is a tool that lets us simulate actions and test permission settings by defining a specific user, group, or role and the action we want to test. It's useful for saving time when testing permission settings. No more logging in as another user or actually stopping resources. Yay!

### How I used the simulator

I set up a simulation to test whether the dev user group has permission to stop instances or delete tags. The results were denied for both. I had to adjust the scope of the EC2 instances to ones that are tagged with "development." Once I applied that tag, the permission was allowed.

![Image](https://nextwork.ai/overjoyed_violet_shy_tieke/uploads/aws-security-iam_069d8a621)

---

## ISO/IEC 27001:2022 Control Mapping

This project is a small, working example of access control. The table maps each part of what I built to the Annex A control it supports and the evidence an auditor would request to verify it.

| Component I built | Annex A control | What it demonstrates | Audit evidence to request |
|---|---|---|---|
| IAM policy scoped to resources tagged Env=development | 5.15 Access control, 8.3 Information access restriction | Access is limited to what the role needs, based on a defined rule | Policy JSON, access control policy, list of roles and what each may access |
| IAM user created for the intern | 5.16 Identity management | Each person gets a unique identity rather than sharing a login | User list, joiner and leaver records, evidence of unique IDs |
| User group with the policy attached | 5.18 Access rights | Permissions are assigned by group and role, not user by user | Group membership list, access request and approval records, periodic access reviews |
| Intern limited to the development environment | 8.31 Separation of development, test and production environments | A non-production user cannot affect production | Environment separation design, test showing denied production access |
| Explicit deny on creating or deleting tags | 8.2 Privileged access rights | Prevents a user from retagging production as "development" to bypass the policy | Policy deny statements, list of who can change tags |
| Env tag on each EC2 instance | 5.9 Inventory of information and other associated assets | Assets are labeled by environment, so ownership and handling rules can be applied | Asset inventory, tagging standard, untagged resource report |
| Intern denied permissions outside EC2 development | 5.3 Segregation of duties, 8.2 Privileged access rights | Duties and privileges are split, so the intern cannot take admin actions | Role definitions, evidence of admin versus non-admin separation |
| Sharing sign-in details by email or .csv | 5.17 Authentication information, 8.5 Secure authentication | Shows the choices available for issuing credentials and where the risk sits | Onboarding procedure, forced password reset setting, MFA enforcement, password policy |
| Testing the policy as the intern and in the Policy Simulator | 5.15 Access control (operating effectiveness) | Access rules were verified by testing, not assumed to work | Test records showing allowed and denied results, screenshots, simulator output |
| Root user account concept | 8.2 Privileged access rights | The most powerful account should be tightly protected | Root MFA status, evidence root is not used day to day, access key check |

**Notes on scope:** An account alias is a usability feature, not a security control, so I did not map it to a control. The mapping above is based on my written description of the policy.

**Not covered by this lab:** MFA, password policy, access reviews, a formal joiner, mover, and leaver process, logging of IAM activity (see my Security Monitoring project), and permission boundaries or centralized identity federation. In a real audit these gaps are often where findings arise.

## Sample Audit Findings (Illustrative)

These findings are based on choices and design points in this project. They show how I would document similar issues during an audit.

### Finding 1: Initial credentials issued by email or downloadable file without stated safeguards

**Criteria:** ISO/IEC 27001:2022 Annex A 5.17 requires that allocation and management of authentication information is controlled through a management process, including advising personnel on appropriate handling. Annex A 8.5 requires secure authentication based on access restrictions.

**Condition:** The project describes two ways of sharing sign-in details with a new IAM user: emailing the instructions or downloading a .csv file. No mention is made of forcing a password change at first sign-in or of requiring MFA.

**Cause:** Credential issuance relies on convenient delivery methods, and no documented onboarding procedure sets minimum safeguards.

**Effect:** Credentials sent by email or stored in a downloaded file could be exposed or reused, allowing unauthorized access to the AWS account.

**Recommendation:**
1. Require a password reset at first sign-in.
2. Enforce MFA for all IAM users.
3. Deliver credentials through a secure channel, separate from the username, and delete the .csv after use.
4. Document the process in an onboarding procedure.

### Finding 2: Broad permissions granted on development resources

**Criteria:** ISO/IEC 27001:2022 Annex A 5.15 and 8.2 require that access rights are allocated and restricted according to need, with privileged access limited and managed.

**Condition:** The policy allows the intern to perform any action on instances tagged "development."

**Cause:** The policy is scoped by environment, which keeps production safe, but not by the specific actions the intern's role requires.

**Effect:** The intern could take actions beyond their duties on development resources, such as terminating instances other team members rely on.

**Recommendation:**
1. Replace the broad allow with a list of only the actions the role needs.
2. Review the policy periodically as part of access reviews.
3. Keep the explicit deny on tag changes, since tags control the boundary between environments.

**Positive observation:** The explicit deny on creating and deleting tags is a strong design choice. Because the policy depends on the Env tag to separate environments, preventing tag changes closes an obvious bypass.

---

*Built with [NextWork](https://nextwork.ai) - [View this project](https://nextwork.ai/projects/51a5fd24-7458-58a6-8653-9b90119a0e4a)*
