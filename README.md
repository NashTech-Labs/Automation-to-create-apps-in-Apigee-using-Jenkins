# Apps in Apigee

In Apigee, "apps" refer to developer applications or developer apps. Apps are entities created within the Apigee platform that represent applications developed by developers or organizations to consume and interact with APIs exposed by the API provider.

## Create Apps in Apigee using Jenkins

This repository contains Jenkinsfile which has script for creating apps in Apigee.

For using this template we have to update our Apigee organisation name and credentials in jenkinsfile for parameter:

`def orgList = ['APIGEE_ORGANISATION_NAME']`

`credentialsId: '<gcp_service_account>'`


Create a Jenkins pipeline using this repository and build that pipeline by passing parameters to it. You can select multiple envs at the same time in parameter. 


![jenkins_pipeline](https://i.postimg.cc/MTtfKbr4/Screenshot-from-2023-07-15-18-17-43.png)


Build the pipeline and it will create app in Apigee.

To verify this move to:

Apigee > Publish > Apps
