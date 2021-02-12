# EBS-Snapshot-using-AWS-lambda

In the Create function wizard, ensure Author from scratch is selected and enter the following values in the bottom form:
	
	Name: EbsSnapshot
        
	Runtime: Node.js 12.X
        
	Role: Choose an existing role
  
Existing role: EBSLambdaRole-...

![image](https://user-images.githubusercontent.com/58148717/107816129-ed7eb680-6d39-11eb-9c91-5c0ba51e9fae.png)


Scroll down to the Function code section, and overwrite the contents of the index.js file with the following code: ( Please check index.js file above)

The function uses the AWS SDK (AWS on line 3) to perform EC2 (ec2 on line 4) operations, specifically creating a snapshot (ec2.createSnapshot on line 10). The function will receive a volume ID (event.volume on line 8) in the event body that is sent when the function is triggered to specify which volume to create a snapshot of. You will configure another function to send the volume ID in a following Lab Step. and will take a snapshot of the volume, so let's use the code editor to create the logic for this function. The function will return an error if there is something wrong with the request (lines 11-14) and will return the information about the snapshot if it succeeds (lines 15-18).

Click Deploy in the upper-right corner to save the function

![image](https://user-images.githubusercontent.com/58148717/107816289-274fbd00-6d3a-11eb-998f-753b3979e4a4.png)


In the EC2 Dashboard, click on Volumes and grab the Volume id of desire EBS 

Return to the Lambda Console and click TakeEbsSnapshot function to view its details: 

Click Test in the upper-right corner

In the Configure test event form, enter the following values into the form

• Event name: TestSnapshot
• Event body: Enter the following JSON into the event editor at the bottom of the form but replace <volume_ID> with the volume ID you copied: 

{
  "volume": "<volume_ID>"
}


Click Create and test it


In the next step we will automate the snapshots' creation by creating a Lambda function that will retrieve the Volume IDs and create a CloudWatch Event to schedule the function.

Return to the Lambda Console, and click Create function in the upper-right corner 

The new function will get all of the Volume IDs of the EBS volumes that exist

![image](https://user-images.githubusercontent.com/58148717/107816446-641bb400-6d3a-11eb-97df-350ddef71dd2.png)

Scroll down to the Function code section, and overwrite the contents of the index.js file with the following code (check the index1.js file above)

this function will gets the volume IDs (ec2.describeVolumes on line 8) and calls the first Lambda function to take the snapshot of each volume (lambda.invoke on line 22). The parameters (params on passed to the first Lambda include the Payload that specifies the volume ID (data.Volumes[i].VolumeId on line 18) and the function name (FunctionName on line 17) of the first Lambda function. If you have used a different name than TakeEbsSnapshot, you need to specify the function name that you used

Scroll down to the Basic Settings panel, and set the Timeout to 10 sec

![image](https://user-images.githubusercontent.com/58148717/107816564-8f060800-6d3a-11eb-9636-2e81e2e2a421.png)

In the Configure test event form, enter the following values into the form

• Event name: TestSnapshots

• Event body: Enter the following JSON into the event editor at the bottom of the form 

![image](https://user-images.githubusercontent.com/58148717/107816625-a34a0500-6d3a-11eb-8590-585052d151aa.png)

The function doesn't require any event data, so an empty JSON object is used ({}).

Click test

In the Designer panel, click on Add triggers > EventBridge (CloudWatch Events)


![image](https://user-images.githubusercontent.com/58148717/107816689-bc52b600-6d3a-11eb-8a89-48361edd14cd.png)


Scroll down to the Configure triggers pane and enter the following values:
	
  . Rule: MonitorWebsiteTask
  
  . Schedule expression: rate(1 minute) 
  
Enable trigger: Checked


























