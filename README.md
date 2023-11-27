# aws-mycicd

# Continuous Integration and Continuous Deployment on AWS Elastic Beanstalk using AWS Code Commit / Code Build / Code Deploy / Code Pipeline Orchestration

![image](https://github.com/kohlidevops/aws-mycicd/assets/100069489/3c694646-4164-4746-9381-47782c9c0d61)

## AWS Accouunts

I have two AWS accounts - Account "A" contains Code Commit has my source code and one Code Pipeline which is used to copying source code as ZIP to Account "B" S3 bucket.
Account "B" has two Code Pipelines such as Staging and Production Pipeline.

## Steps to implement

I explain the steps which involved in this complete Pipeline.

1. When developer changes their code in Code Commit, then Account "A" Pipeline will trigger automatically then copy the source code as ZIP to Account "B" S3 bucket with the help of AWS Code Build and IAM Role.

2. The Staging Code Pipeline will trigger when S3 bucket received source code as ZIP. This pipeline will start the Code Deploy to apply this versioning code to Staging Elastic Beanstalk.

3. Once code deployed in Elastic Beanstalk, then custom stage will trigger the Lambda function to remove the cache (Invalidation Cache) from the Cloud front Distribution.

4. Once Cache has been cleared, then Code build will conduct the User Interface Testing and after the test, then report and build logs has been stored in to S3 bucket.

   #### Staging Pipeline has been completed

5. When the Code build status is succeeded then copy the source code artifacts from Staging S3 bucket to Production S3 bucket with help of AWS CloudWatch Event (Here, I have used CLoudWatch Event to detect the Code Build status change as "Succeeded" then one more Code Build will start to copy the Artifacts from Staging to Production S3 bucket.

(If Code Build failed means the Test case failed then this Pipeline will stop this work and send notify to DevOps team)

6. Production Pipeline will trigger automatically when any changes occurred in Production S3 bucket.

7. This pipleine has one Manual Approval stage. When this stage is Approved (I have shared the Staging URL for Review and asking for Approval to proceed), then Code Deploy will start the deploy this new version to Production Elastic Beanstalk.

## We are not hardcode any DB credentials into application code. Everything calling from Elastic Beanstalk environment variable. So I have easily depoloy the source code of staging to Production without any worry.

8. After this stage, again lambda will trigger to start the removing caching from Production Cloud front Distribution.
   

