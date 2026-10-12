# Lab Validation and Detection Evidence

I validated my AWS-hosted Wazuh home lab by generating security events on the Windows endpoint and investigating the resulting events in the Wazuh dashboard.

Test 1: Windows Account Manipulation

- Objective: Observe security events associated with Windows account creation and group membership changes.
- Method: Created a temporary local account and added it to the local Administrators group in the isolated lab environment.
- Evidence: Reviewed the Wazuh dashboard and event report for account-change and group-membership events.
- Security relevance: Unauthorized account creation or privilege changes can indicate attempts to establish persistence or gain elevated access.

Test 2: Windows Security Log Clearing

- Objective: Determine whether Wazuh records an event when the Windows Security audit log is cleared.
- Method: Cleared the Security event log on the Windows lab endpoint.
- Evidence: Investigated events using the log_clearing_audit log rule-group filter.
- Security Relevance: Monitoring audit-log clearing helps investigators identify activity that may undermine security visibility or incident investigations.

# Lessons Learned

This project provided hands-on practice with endpoint enrollment, centralized security monitoring, event filtering, alert investigation, and interpreting Windows security events. 

Detection results should be validated against the individual event details and the Wazuh rules active in the lab configuration.

