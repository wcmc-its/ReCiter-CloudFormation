# ReCiter-CloudFormation

[ReCiter](https://github.com/wcmc-its/reciter/) is a highly accurate system for guessing which publications in PubMed a given person has authored. ReCiter includes a Java application, a DynamoDB-hosted database, and a set of RESTful microservices which collectively allow institutions to maintain accurate and up-to-date author publication lists for thousands of people. This software is optimized for disambiguating authorship in PubMed and, optionally, Scopus. 

ReCiter can be installed on a locally controlled server or using services provided by Amazon Web Services (AWS). For those looking to install ReCiter on AWS, this cloud repository provides a CloudFormation template.

[CloudFormation](https://aws.amazon.com/cloudformation/) allows you to use a simple text file to model and provision, in an automated and secure manner, all the resources needed for your applications across all regions and accounts. This file serves as the single source of truth for your cloud environment. 

As the below installation instructions explain, you can choose to install a template with a Scopus integration or without a Scopus integration.



## Installation instructions

1. Create an AWS user and account through AWS console
   1. Create an AWS user [here](https://console.aws.amazon.com/console/home).
   1. Navigate to the [IAM](https://console.aws.amazon.com/iam/home) (Identity and Access Management) managed service.
   1. Go to `Users` and click on `Add User`
   ![add user](/files/image6.png)   
   1. User name could be anything, but let's choose `svc-reciter.`
   ![add user](/files/image8.png)
   1. For `Access Type`, select as `Programmatic Access.`
   1. Click on `Next Permissions.`
   1. Click on `Attach existing policies directly.`
   1. Use the filter to find and select the policy, `AdministratorAccess.`
   ![add user](/files/image5.png)
   1. Click on `Next Review`
   1. Create user.
   1. Click on `Download credentials.` (You will only able to view the credential once. Store in a secure location.)
1. Install the AWS CLI (command-line interface) on your local machine. 
   1. Verify that you have Python installed, preferably Python 3.4 or greater. 
      1. To check on your version of Python, enter the following in Terminal: `python --version`
      1. If Python is not the proper version, enter: `brew install python`
   1. Use PIP to install the AWS CLI: `pip3 install awscli --upgrade --user` 
   1. Check version using `aws --version`
   1. Setup AWS profile
     1. In Terminal, enter `aws configure --profile reciter`
     ![add user](/files/image4.png)
     1. Input: the access key and secret key you got from creating the AWS user; also, input the region you want to run ReCiter in. 
     1. Your profile should now be set up, but let's run a test to see if its setup we will use the cli to get our AWS account number. 
     1. Enter `aws sts get-caller-identity --output text --query 'Account' --profile reciter` ???
1. Import the Reciter-PubMed CloudFormation template in AWS console
   1. Go to the CloudFormation service in [AWS console](https://console.aws.amazon.com/cloudformation/home)
   1. Click on `Create stack` 
   1. For template source, select Amazon S3 url. 
   ![add user](/files/image1.png)
   1. You may choose to enter either:
      1. "No Scopus" template - https://reciter-workshop.s3.amazonaws.com/aws-elasticbeanstalk-master-stack-noscopus.json
      1. Includes "Scopus" template(use if you have scopus subscription and have its api-key and insttoken) - https://reciter-workshop.s3.amazonaws.com/aws-elasticbeanstalk-master-stack.json
   1. Click on `Next` 
   1. Enter a name for the stack, e.g., `reciter-workshop-master-stack`
   1. For `ApplicationPubmedEnvPubmedApikey`, use `9ab81e95f12df169b4e40c02719f76db8308`. Although we recommend getting your own api key from Pubmed website
   1. For `ApplicationReciterEnvAMAZONAWSACCESSKEY` and `ApplicationReciterEnvAMAZONAWSSECRETKEY`, give the keys you created in IAM user.
   1. Enter DNS names for the ApplicationCNAMEPubmed and ApplicationCNAMEReciter fields.
      1. DNS names must be regionally unique.
      1. To verify a DNS is available, enter the following in Terminal where <profile-name> is your profile name that you setup in previous step and include your preferred dns prefix:
      `aws elasticbeanstalk check-dns-availability --cname-prefix <your preferred dns prefix> --profile <profile-name>`
      1. Suggestion: to avoid conflicts, include your personal institutional ID.
   1. Click `Next`, and add tags. These tags will be attached to all the resources that are created with this stack.
   1. Check the two acknowledge boxes and click "Create stack.
    ![add user](/files/image3.png)
   1. That should create your application hosting stack with load balancer and 1 instance to host the application.
   1. This generally takes ~10 minutes to create. In the meantime, we can import the second stack.
1. Configure your GitHub account.
   1. If you haven't done so already, create a [Github Account](https://github.com/).
   1. Visit the [settings](https://github.com/settings/profile), associated with your personal Github account. 
   1. Click on `Developer Settings`
   1. Go to [Personal Access Tokens](https://github.com/settings/tokens).
   1. Click on `Generate new token`.
   ![add user](/files/image7.png)
   1. In the `Note` field, enter `reciter-workshop` (or whatever alternative you wish).
   1. Check `public_repo` and the `Generate token` button below.
   ![add user](/files/image2.png)
   1. Note this token in a secure place.
1. Fork the ReCiter repository to your personal GitHub account.
   1. Go to the [ReCiter repository](https://github.com/wcmc-its/ReCiter).
   1. Click on the `Fork` button.	
   1. Go to the application,properties file as it is forked on your personal account as located here: 
   `https://github.com/<your-github-username>/ReCiter/blob/master/src/main/resources/application.properties`
   1. Edit the file and find `aws.s3.use.dynamic.bucketName` and set that flag = `true`:
   `aws.s3.use.dynamic.bucketName=true`
   1. Under commit message, enter `Dynamic bucket generation`. 
   1. Click `Commit`
   1. Edit the file and find `aws.dynamodb.settings.region` and set that to appropriate region you want all your dynamodb tables to be:
   for e.g. if you are in us-east-1(N. Virginia) `aws.dynamodb.settings.region=us-east-1`
   1. If you dont want to use scopus then ddit the file and find `use.scopus.articles` and set that to false:
   `use.scopus.articles=false`
   1. Under commit message, enter `set dynamodb region and scopus flag`. 
   1. Click `Commit`
1. Fork the ReCiter-Pubmed-Retrieval tool repository to you personal GitHub account.
   1. Go to the [ReCiter PubMed Retrieval Tool](https://github.com/wcmc-its/ReCiter-PubMed-Retrieval-Tool).
1. Fork the ReCiter-Scopus-Retrieval-Tool repository to you personal GitHub account. Do this if you have used the cloudformation template with scopus included and also if you have valid scopus subscription.
   1. Go to the [Scopus repository](https://github.com/wcmc-its/ReCiter-Scopus-Retrieval-Tool)
1. Fork the ReCiter-Publication-Manager repository to you personal GitHub account.
   1. Go to the [ReCiter publication manager repository](https://github.com/wcmc-its/ReCiter-Publication-Manager)
   1. Click on the `Fork` button. 
   1. Go to the local,js file as it is forked on your personal account as located here: 
   `https://github.com/<your-github-username>/ReCiter-Publication-Manager/blob/master/config/local.js`
   1. Edit the file and put your reciter endpoint and reciter-pubmed endpoint that you specified as the CNAME prefix in the previous cloudformation template. Those endpoint will be used by the application to manage the publication data. Also make sure the adminApikey is also the same as specified in the previous master cloudformation template.
1. Import the CI/CD (continuous integration/delivery) CloudFormation template in AWS console
   1. Before we proceed, we need to verify that the Reciter-PubMed CloudFormation template has been competely installed. In [CloudFormation home](https://console.aws.amazon.com/cloudformation/home), ensure that you see the `UPDATE_COMPLETE` status message for the Reciter-PubMed CloudFormation template. If this installation is not complete, wait until it is.
   1. At the [AWS cloudformation console](https://console.aws.amazon.com/cloudformation/home), click on `Create stack`.
   1. Enter the S3 URL we will be using: 
  `https://reciter-workshop.s3.amazonaws.com/aws-ci-cd-master-stack-noscopus.yml` or `https://reciter-workshop.s3.amazonaws.com/aws-ci-cd-master-stack.yml` if you have scopus subscription.
   ![add user](/files/image9.png)
   1. Click on `Next`
   1. Enter a stack name: `reciter-ci-cd`
   1. For GitHubToken, enter the token we generated for your personal GitHub account.
   1. In the GitHubUser field, enter your GitHub username.
   1. The remainder of fields can be set to their default.
   1. Click on `Next` and acknowledge the checkboxes
   1. After review, click `Create stack`
   1. Wait for stack to finish by looking for the `UPDATE_COMPLETE` status message.
1. Use ReCiter in production.
   1. Visit the [CodePipeline service](https://console.aws.amazon.com/codesuite/codepipeline/pipelines).
   1. If the stack has finished installing, you should see three pipelines: ReCiter and ReCiterPubmed and ReCiterPublicationManager. You will see four pipelines additionally ReCiterScopus if you used the template with Scopus.
   1. You can check the status of each pipeline as it goes through the process.
   1. Click on ReCiter. As you can see, it is pulling the changes for our source repository and then building the application. You can click on `Details` in the Build section to  see live logs of the build process
   1. When the build is complete, go the URL for ReCiter.	
   1. Use the CNAME you entered above for ReCiter, and go to a URL that has this general form: 
   `http://<cname>.<region>.elasticbeanstalk.com/swagger-ui/index.html`
   1. If you have trouble finding this URL, go [here](https://console.aws.amazon.com/elasticbeanstalk/home) and click on ReCiterService 
   1. You can do the same for the ReCiter Pubmed Service and other services that we created.
1. Teardown of resources - When you are finished experimenting with or using your AWS account, you should clean up the resources associated with it to avoid incurring charges for resources that you are not using.
   1. Optional: go to [CloudWatch](https://console.aws.amazon.com/cloudwatch/) and see which services are being used.
   1. Empty any S3 buckets that have been created [here](https://console.aws.amazon.com/s3/). There will be one created for codepipeline with bucket name codepipeline-<your-region>-<account-number> e.g. codepipeline-us-east-1-<account number>. Use the console to empty it or you can use the terminal to delete it as well using `aws s3 rm s3://<bucket-name> --recursive --profile <profile-name>`
   1. Select the “master” version of any CloudFormation stacks and delete them [Cloudformation](https://console.aws.amazon.com/cloudformation/). Delete cannot proceed if your setup is still using services. This may take several minutes.
   1. Wait for delete to be completed. When it is completed, it should say “No stacks.”
   1. Go to [DynamoDB console](https://console.aws.amazon.com/dynamodb/). Delete all the tables one by one by clicking on delete table button.
   1. Voila you have deleted all your resources and should have an empty account.
