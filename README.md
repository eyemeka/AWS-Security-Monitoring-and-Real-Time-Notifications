# AWS Security Monitoring and Real Time Notifications

<h2>Overview</h2>
This project focuses on setting up comprehensive identity monitoring and alerting within an AWS environment. It involves configuring three primary AWS services: <strong>CloudTrail</strong>, <strong>SNS (Simple Notification Service)</strong>, and <strong>EventBridge</strong>, to create an efficient alert system around AWS identity calls, specifically the <strong>GetCallerIdentity</strong> API call.

<strong>CloudTrail</strong> is configured to log and monitor all AWS account activity, ensuring a complete record of actions, especially identity-related events. <strong>SNS</strong> is then set up to send real-time email notifications for critical events, enabling immediate visibility when specific API calls, such as GetCallerIdentity, are made. Finally, <strong>EventBridge</strong> is configured to trigger whenever the GetCallerIdentity API call is executed, activating an alert pipeline that sends notifications via SNS.

This setup enhances security by providing timely alerts on identity-related activities, helping administrators maintain a robust audit trail, and quickly respond to potential unauthorized access attempts or misconfigurations.
<h2>Introduction</h2>
This project enhances security and monitoring within AWS by configuring CloudTrail, SNS, and EventBridge to track identity-based actions. By logging all account activity through CloudTrail and setting up SNS for email notifications, we create a real-time alert system for identity-related events. Specifically, EventBridge is configured to trigger notifications whenever the <strong>GetCallerIdentity</strong> API call is made, allowing immediate awareness of any identity verification attempts within the AWS environment. This setup strengthens security posture by enabling swift responses to potentially unauthorized access attempts or unusual account activity.
<h2>1. Configure CloudTrail</h2>
<h3>Navigate to CloudTrail</h3>
<ul>
 	<li>Click "Trails" in the left navigation pane</li>
 	<li>Click "Create trail</li>
</ul>
<h3>Configure the Trail:</h3>
<ul>
 	<li>Trail Name - MyCloudTrail</li>
 	<li>Storage Location: Choose "Create new S3 bucket" and enter a unique bucket name: my-cloudtrail-logs-unique-id</li>
 	<li>Log Events: Ensure "Management events" is enabled with "Read/Write" events set to "All".</li>
 	<li>Apply trail to all regions: Enable this option.</li>
</ul>
<h3>Advanced Settings:</h3>
<ul>
 	<li>Log file validation: Enable if desired</li>
 	<li>SNS: Leave unchecked for now (we'll set it up next)</li>
</ul>
<h3>Create the Trail:</h3>
<ul>
 	<li>Click "Create trail" and wait for it to set up</li>
</ul>
<img class="aligncenter size-large wp-image-1844" src="https://www.businesstoks.com.ng/wp-content/uploads/2024/10/Screenshot-185-1024x301.png" alt="" width="640" height="188" />
<h2>2. Set Up SNS to Send Email Alerts</h2>
<h3>Navigate to SNS:</h3>
<h3><strong>Create an SNS Topic</strong></h3>
<ul>
 	<li>Click on "Topics".</li>
 	<li>Click "Create topic".</li>
 	<li>Type: Choose "Standard".</li>
 	<li>Name: I entered APICallAlert</li>
 	<li>Click "Create topic</li>
</ul>
<img class="aligncenter size-large wp-image-1845" src="https://www.businesstoks.com.ng/wp-content/uploads/2024/10/Screenshot-186-1024x384.png" alt="" width="640" height="240" />
<h3></h3>
<h3>Subscribe to the SNS Topic:</h3>
<ul>
 	<li>Click on the topic you just created.</li>
 	<li>Click "Create subscription".</li>
 	<li>Protocol: Select "Email".</li>
 	<li>Endpoint: Enter your email address.</li>
 	<li>Click "Create subscription".</li>
 	<li>Check your email inbox for a confirmation email and click the "Confirm subscription" link.</li>
</ul>
<img class="aligncenter size-large wp-image-1846" src="https://www.businesstoks.com.ng/wp-content/uploads/2024/10/Screenshot-187-1024x417.png" alt="" width="640" height="261" />

Confirmation email was sent to my Gmail

<img class="aligncenter size-large wp-image-1847" src="https://www.businesstoks.com.ng/wp-content/uploads/2024/10/Screenshot-188-1024x427.png" alt="" width="640" height="267" />

I confirmed the subscription by clicking the "Confirm Subscription" link

<img class="aligncenter size-large wp-image-1848" src="https://www.businesstoks.com.ng/wp-content/uploads/2024/10/Screenshot-189-1024x268.png" alt="" width="640" height="168" />
<h3>Enable SNS Notification in CloudTrail:</h3>
<ul>
 	<li>Go back to CloudTrail.</li>
 	<li>Select your trail and click "Edit" in the "Trail settings" section.</li>
 	<li>Scroll to the SNS section, select your SNS topic (APICallAlert), and enable SNS notification delivery.</li>
 	<li>Save the changes.</li>
</ul>
<img class="aligncenter size-large wp-image-1849" src="https://www.businesstoks.com.ng/wp-content/uploads/2024/10/Screenshot-190-1024x412.png" alt="" width="640" height="258" />


<h2 class="styled__Paragraph-sc-y5pp90-3 eHgPWw">3. Set Up EventBridge to Trigger on GetCallerIdentity for Root User</h2>
<h3 class="styled__Paragraph-sc-y5pp90-3 eHgPWw">Navigate to EventBridge:</h3>
<h4>Create a Rule:</h4>
<ul>
 	<li>Click on "Rules".</li>
 	<li>Click "Create rule".</li>
</ul>
<h3>Configure the Rule:</h3>
<ul>
 	<li>Name:  GetCallerIdentityAlert</li>
 	<li>Event Bus: Ensure "AWS default event bus" is selected.</li>
 	<li>Rule Type: Choose "Rule with an event pattern".</li>
</ul>
<h4>Define the Event Pattern:</h4>
Choose "Custom patterns (JSON editor)" and enter:

{
"source": ["aws.sts"],
"detail-type": ["AWS API Call via CloudTrail"],
"detail": {
"eventSource": ["sts.amazonaws.com"],
"eventName": ["GetCallerIdentity"],
"userIdentity": {
"type": ["Root"]
}
}
}
<h3>Explanation:</h3>
This pattern filters for GetCallerIdentity API calls made by the root user.
<h3>Add SNS as the Target:</h3>
<ul>
 	<li>Under Target, select "AWS service".</li>
 	<li>Select a target: Choose "SNS topic".</li>
 	<li>Topic: Select the SNS topic (APICallAlert).</li>
</ul>
<h4>Configure Permissions (If Prompted):</h4>
<ul>
 	<li>Allow EventBridge to create a new role with permissions to publish to the SNS topic.</li>
</ul>
<h4>Create the Rule:</h4>
<ul>
 	<li>Review the settings and click "Create rule".</li>
</ul>
<img class="aligncenter size-large wp-image-1850" src="https://www.businesstoks.com.ng/wp-content/uploads/2024/10/Screenshot-191-1024x469.png" alt="" width="640" height="293" />


<h2>4. Test the Setup via Command Line</h2>
<h3>Install AWS CLI (If Not Installed):</h3>
<ul>
 	<li>Follow the instructions here for your OS.</li>
 	<li>Configure AWS CLI with Root Credentials:</li>
 	<li>Open your terminal or command prompt.</li>
</ul>
<h3>Run:</h3>
<ul>
 	<li>aws configure</li>
</ul>
Enter your root Access Key ID, Secret Access Key, Default region name (e.g., us-east-1), and Default output format (e.g., json).
<h3>Run the GetCallerIdentity Command:</h3>
<ul>
 	<li>aws sts get-caller-identity</li>
</ul>
<h3>Check CloudTrail Logs:</h3>
<ul>
 	<li>Go to the CloudTrail console.</li>
 	<li>Check the Event history for the GetCallerIdentity event to confirm it has been logged.</li>
</ul>

<h3>Verify SNS Email Notification:</h3>
<img class="aligncenter size-large wp-image-1853" src="https://www.businesstoks.com.ng/wp-content/uploads/2024/10/Screenshot-195-1024x252.png" alt="" width="640" height="158" />


<h2>Conclusion</h2>
The successful setup and testing of AWS Security Monitoring and Real-Time Notifications with CloudTrail, SNS, and EventBridge to monitor <em>GetCallerIdentity</em> API calls, enhances security monitoring and operational visibility within the AWS environment. By configuring CloudTrail to log API activity, SNS for real-time alerts, and EventBridge to trigger specific responses, I have created a comprehensive monitoring solution that is proactive and responsive to potential unauthorized access attempts or anomalies in account activity. This solution not only strengthens an organization's ability to detect and respond to security events but also lays a solid foundation for more advanced monitoring and automation as part of a robust AWS security posture.
