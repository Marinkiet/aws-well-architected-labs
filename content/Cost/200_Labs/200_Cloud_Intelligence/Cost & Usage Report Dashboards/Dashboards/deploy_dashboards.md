---
title: "Deploy Dashboards"
date: 2022-10-10T11:16:08-04:00
chapter: false
weight: 1
pre: "<b>1 </b>"
---

## Authors
- Yuriy Prykhodko, AWS Principal Technical Account Manager
- Iakov Gan, AWS Sr. Technical Account Manager
- Thomas Buatois, AWS Cloud Infrastructure Architect (ProServe)

## Contributors
- Aaron Edell, Global Head of Business and GTM - Customer Cloud Intelligence
---

## Introduction
There are two primary method for deploying **CUDOS**, **KPI** and **Cost Intelligence Dashboard** listed below; *basic* and *advanced*. Both methods use CloudFormation templates to automate the setup. If you wish to use a command line tool or to deploy manually, see the alternative deployment methods section. 

## Basic Setup
If you want to deploy **CUDOS**, **KPI** and **Cost Intelligence Dashboard** into your Management (Payer) Account, follow these steps. 

{{%expand "Click here to continue with Basic Setup" %}}

![Images/arch4.png](/Cost/200_Cloud_Intelligence/Images/arch4.png?classes=lab_picture_verysmall)

### Cost and Usage Report (CUR)
The [Cost & Usage Report](https://docs.aws.amazon.com/cur/latest/userguide/what-is-cur.html) is the foundation for multiple CID Dashboards. CID requires Cost & Usage Report to be created with the following format:
- Additional report details: Include **Resource IDs**
- Time Granularity: **Hourly**
- Report Versioning: **Overwrite existing report**
- Report data integration for: **Amazon Athena**
- Compression type: **Parquet**

This step will create a new CUR for you. You can request a [backfill](https://docs.aws.amazon.com/cur/latest/userguide/troubleshooting.html#backfill-data) after finishing this lab to have data in your dashboards from the past 7 months or more.

### Create and Deploy CUR

1. Login to the Management (Payer) Account in the region of your choice. 
   
2. Click the **Launch CloudFormation button** below to open the **pre-populated stack template** in your CloudFormation console and select **Next**.

	- [Launch CloudFormation Template](https://console.aws.amazon.com/cloudformation/home#/stacks/new?&templateURL=https://aws-managed-cost-intelligence-dashboards.s3.amazonaws.com/cfn/cur-aggregation.yaml&stackName=CID-CUR-Destination&param_CreateCUR=True&param_DestinationAccountId=REPLACE%20WITH%20CURRENT%20ACCOUNT%20ID&param_SourceAccountIds=)
	
![Images/multi-account/cf_dash_2.png](/Cost/200_Cloud_Intelligence/Images/multi-account/cf_dash_launch_2.png?classes=lab_picture_small)

3. Set CreateCUR to **true**.
   
![Images/multi-account/cfn_dash_param_dst_dedicated_3.png.png](/Cost/200_Cloud_Intelligence/Images/multi-account/cfn_dash_param_dst_dedicated_3.png?classes=lab_picture_small)

4. Enter your Management (Payer) Account ID. Leave Source Account Empty. 

   
![Images/multi-account/cfn_dash_param_dst_dedicated_2.png](/Cost/200_Cloud_Intelligence/Images/multi-account/cfn_dash_param_dst_dedicated_2.png?classes=lab_picture_small)

5. Select **Next** at the bottom of **Specify stack details** and then select **Next** again on the **Configure stack options** page.

6.  Review the configuration, click **I acknowledge that AWS CloudFormation might create IAM resources, and click Create stack**.
![Images/cf_dash_9.png](/Cost/200_Cloud_Intelligence/Images/cf_dash_9.png?classes=lab_picture_small)

7. You will see the stack will start with **CREATE_IN_PROGRESS**.
   **NOTE:** This step can take 5-15mins
    ------------ | -------------

8. Once complete, the stack will show **CREATE_COMPLETE**.

**You will now need to wait 24 hours for your first CUR to be deployed into S3 before you can continue**

### Enable QuickSight
QuickSight is the AWS Business Intelligence tool that will allow you to not only view the Standard AWS provided insights into all of your accounts, but will also allow to produce new versions of the Dashboards we provide or create something entirely customized to you. 

**If you are already a regular QuickSight user** you will need to make sure you have an enterprise license and add permissions for QuickSight to read your CUR bucket. You can accomplish this by going to the persona icon in the upper right hand corner of QuickSight, clicking Manage Quicksight, clicking on Security and Permissions, clicking on managing QuickSight access to AWS Service, selecting S3, then selecting the CUR bucket from the list of S3 buckets. If you are new to QuickSight, complete the steps below.

1. Log into your AWS Account and search for **QuickSight** in the list of Services

2. You will be asked to **sign up** before you will be able to use it

    ![QuickSight Sign up Workflow Image](https://wellarchitectedlabs.com/Cost/200_Cloud_Intelligence/Images/QS-signup.png?classes=lab_picture_small)

3. After pressing the **Sign up** button you will be presented with 2 options, please ensure you select the **Enterprise Edition** during this step

4. Select **continue** and you will need to fill in a series of options in order to finish creating your account. 

    + Ensure you select the region that is most appropriate based on where your S3 Bucket is located containing your Cost & Usage Report file.

        ![Select Region and Amazon S3 Discovery](/Cost/200_Cloud_Intelligence/Images/QS-s3.png?classes=lab_picture_small)
    
    + Enable the Amazon S3 option and select the bucket where your **Cost & Usage Report** is stored, as well as your **Athena** query bucket

        ![Image of s3 buckets that are linked to the QuickSight account. Enable bucket and give Athena Write permission to it.](/Cost/200_Cloud_Intelligence/Images/QS-bucket.png?classes=lab_picture_small)

5. Click **Finish** and wait for the congratulations screen to display

6. Click **Go to Amazon QuickSight**
![](/Cost/200_Cloud_Intelligence/Images/Congrats-QS.png?classes=lab_picture_small)
1. Click on the persona icon on the top right and select manage QuickSight. 
2. Click on the SPICE Capacity option. Purchase enough SPICE capacity so that the total is roughly 40GB. If you get SPICE capacity errors later, you can come back here to purchase more. If you've purchased too much you can also release it after you've deployed the dashboards. 


### Deploy the Dashboards

1. Login into your Management (Payer) Account

2. Click the **Launch CloudFormation button** below to open the **pre-populated stack template** in your CloudFormation console and select **Next**

	- [Launch CloudFormation Template](https://console.aws.amazon.com/cloudformation/home#/stacks/create/review?&templateURL=https://aws-managed-cost-intelligence-dashboards.s3.amazonaws.com/cfn/cid-cfn.yml&stackName=Cloud-Intelligence-Dashboards&param_DeployCUDOSDashboard=yes&param_DeployKPIDashboard=yes&param_DeployCostIntelligenceDashboard=yes)
	
3. Enter a **Stack name** for your template such as **Cloud-Intelligence-Dashboards**
4. Review **Common Parameters** and confirm prerequisites before specifying the other parameters. You must answer 'yes' to both prerequisites questions.
5. Copy and paste your **QuicksightUserName** into the parameter text box
To find your QuickSight username:
	- Open a new tab or window and navigate to the **QuickSight** console
	- Find your username in the top right navigation bar
![Images/cf_dash_qs_2.png](/Cost/200_Cloud_Intelligence/Images/cf_dash_qs_2.png?classes=lab_picture_small)

6. Update your **CURBucketPath** if needed. 
   1. (Default) If you used the CFN Template in the CUR setup process above then **CURBucketPath** needs to be the s3 path to the folder in your CUR bucket where account IDs are. For example `s3://cid-1234567890123-shared/cur/`
   2. If you did *not* use the CFN automated CUR setup process above and have just one CUR you setup manually then **CURBucketPath** needs to be the s3 path to the folder in your CUR bucket where the year folders are. For example `s3://cid-1234567890123-shared/prefix/name/name/` (double check this path, you must see /year=xxxx partitions in there)


7. Select the Dashboards you want to install. We recommend deploying all three: Cost Intelligence Dashboard, CUDOS, and the KPI Dashboard.

8.  Review the configuration, click **I acknowledge that AWS CloudFormation might create IAM resources, and click Create stack**.

9.  You will see the stack will start in **CREATE_IN_PROGRESS** 

   **NOTE:** This step can take up to 7 minutes. 
    ------------ | -------------

10. Once complete, the stack will show **CREATE_COMPLETE**

11. While this is working, head back to QuickSight and click on manage Quicksight from the person icon on the top right. 

![quicksightpermissionss3_1](/Cost/200_Cloud_Intelligence/Images/quicksightpermissionss3_1.png?classes=lab_picture_small)

12. Select Security and permissions. Under QuickSight access to AWS services click manage. Select Athena, S3, and in S3 select both your CUR bucket **and** Query results bucket (Ex: will be something like `aws-athena-query-results-cid-1234567890123-us-east-1`). If you do not see this bucket, please check if it is created by the CloudFormation stack.

![quicksightpermissionss3_2](/Cost/200_Cloud_Intelligence/Images/quicksightpermissionss3_2.png?classes=lab_picture_small)

13. Navigate back to CloudFormation and to the **Output of the Stack** tab and check dashboards URLS. Click on a URL to open the dashboards.

   **NOTE:** Stack can take 5-7 mins.
    ------------ | -------------

If you see no data please check the following:
 1) Double check that QuickSight has permissions to read from your CUR bucket **and** your Query Results location bucket. Query results bucket name will be something like `aws-athena-query-results-cid-1234567890123-us-east-1`
 2) In QuickSight, go to Datasets and click on Summary View. Check for errors (if you see a status `Failed`, you can click it to see more info).
 3) Check if CUR data has arrived to the S3 bucket. If you just created CUR you will need to wait 24 hours before the first data arrives.
 4) The QuickSight datasets refresh once per day at midnight, if your first CUR was delivered after midnight, you may need to click manual refresh on each dataset to see data in the dashboard. This will auto-resolve after midnight the next night.

{{% /expand%}}

## Advanced Setup
If you want to deploy **CUDOS**, **KPI** and **Cost Intelligence Dashboard** in an account other than your Management (Payer) Account, or wish to deploy the dashboards on top of multiple Management (Payer) Accounts or multiple linked accounts, use this method. 

{{< rawhtml >}}
<iframe width="560" height="315" src="https://www.youtube.com/embed/uAiYmJu99zU" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
{{< /rawhtml >}}

{{%expand "Click here to continue with Advanced Setup" %}}



## Deployment Overview

![Images/arch5.png](/Cost/200_Cloud_Intelligence/Images/arch5.png?classes=lab_picture_small)

Advanced deployment supports multiple scenarios:

1. Deploying Dashboards into an account other than the management (payer) account.
2. Deploying Dashboards into an account, where multiple CURs, from multiple payer accounts are aggregated.
3. Also Advanced deployment option is good for a situation where you do not have access to the management (payer) account, but want to deploy the Dashboaeds on top of multiple Linked Accounts.

You will be deploying CloudFormation templates into two different types of places: your **Source Accounts** (typically management or payer account, but can be also a linked account as well) and your **Destination** or **Data Collection Account** (where you will deploy dashboards). You can create a new Linked Account or use an existing one. 


![Images/arch6.png](/Cost/200_Cloud_Intelligence/Images/arch6.png?classes=lab_picture_verysmall)

There will be 3 steps related to CloudFormation deployment: 
1. In **Destination** or **Data Collection Account**, the CloudFormation template will create an S3 bucket for CUR aggregation `cid-{accountid}-shared`.
2. In one or more **Source Accounts**. The CloudFormation template will create a new CUR, an S3 bucket, and a replication rule.
3. In **Destination** or **Data Collection Account**, you will install another CloudFormation template that will create Dashboards.

As a result of first 2 steps, in **Data Collection Account** you will have an S3 bucket with the following structure:

```html
s3://<prefix>-<destination-accountid>-shared/
	cur/<src-account1>/cid/cid/year=XXXX/month=YY/*.parquet
	cur/<src-account2>/cid/cid/year=XXXX/month=YY/*.parquet
	cur/<src-account3>/cid/cid/year=XXXX/month=YY/*.parquet
```

### Step 1. Deploy a Bucket for CUR Aggregation in Data Collection Account (where you will deploy your dashboards)

Here we will deploy the CFN template but setting the CFN parameters for a Destination Account.

1. Login to the Destination account in the region of your choice. I can be any account inside or outside your AWS Organization.
   
2. Click the **Launch CloudFormation button** below to open the **pre-populated stack template** in your CloudFormation console and select **Next**.

	- [Launch CloudFormation Template](https://console.aws.amazon.com/cloudformation/home#/stacks/create/review?&templateURL=https://aws-managed-cost-intelligence-dashboards.s3.amazonaws.com/cfn/cur-aggregation.yaml&stackName=CID-CUR-Destination&param_CreateCUR=False&param_DestinationAccountId=REPLACE%20WITH%20THE%20CURRENT%20ACCOUNT%20ID&param_SourceAccountIds=PUT%20HERE%20THE%20PAYER%20ACCOUNT%20ID)
	
![Images/multi-account/advanced-step1.png](/Cost/200_Cloud_Intelligence/Images/multi-account/advanced-step1.png?classes=lab_picture_small)

3. Enter your **Destination** Account Id (Current Account). 
   
   **NOTE:** Please note this Account ID, we will need it later when we will deploy this same stack in your management (payer)/source accounts.

4. Disable CUR creation by entering **False** as the parameter value if you are replicating CURs from management (payer) accounts. You will only need to activate this if you are replicating CURs from linked accounts (not management payer accounts) and you want to have cost and usage data for this Destination account as well.
   
5. Enter your **Source Account(s)** IDs, using commas to separate multiple Account IDs. 
   
6.  Review the configuration, click **I acknowledge that AWS CloudFormation might create IAM resources**, and click **Create stack**.

7. You will see the stack will start with **CREATE_IN_PROGRESS**.

   **NOTE:** This step can take 5-15mins
    ------------ | -------------

8. Once complete, the stack will show **CREATE_COMPLETE**.

### Step 2. Create CUR and Replication in Source Account (Management Account)

1. Login to your Source Account (can be management account or linked account if you're using [member CURs](https://aws.amazon.com/about-aws/whats-new/2020/12/cost-and-usage-report-now-available-to-member-linked-accounts/)).

2. Click the **Launch CloudFormation button** below to open the **stack template** in your CloudFormation console and select **Next**.

	- [Launch CloudFormation Template](https://console.aws.amazon.com/cloudformation/home#/stacks/create/review?&templateURL=https://aws-managed-cost-intelligence-dashboards.s3.amazonaws.com/cfn/cur-aggregation.yaml&stackName=CID-CUR-Replication&param_CreateCUR=True&param_DestinationAccountId=REPLACE%20WITH%20DATA%20COLLECTION%20ACCOUNT%20ID&param_SourceAccountIds=)
	
![Images/multi-account/advanced-step2.png](/Cost/200_Cloud_Intelligence/Images/multi-account/advanced-step2.png?classes=lab_picture_small)


3. Enter a **Stack name** for your template such as **CID-CUR-Replication**.

4. Enter your **Desitnation** AWS Account ID as a parameter (Your Data Collection Account, where you will deploy dashboards).

6.  Review the configuration, click **I acknowledge that AWS CloudFormation might create IAM resources, and click Create stack**.

7. You will see the stack will start with **CREATE_IN_PROGRESS** .
   **NOTE:** This step can take 5-15mins
    ------------ | -------------

8.  Once complete, the stack will show **CREATE_COMPLETE**.
   **NOTE:** It takes 24 hours for your first CUR to be delivered
    ------------ | -------------

9. It will take about 24 hours for your CUR to populate and replicate to your destination (data collection) account where you will deploy the dashboards. Return to this step after 24 hours. We also recommend creating a Support Case in Service=Billing and category=Invoices and Reporting, requesting a backfill of your CUR (name=cid) with 12 months of data. Case must be created from your Source Account (Management/Payer account).
     
### Add or delete accounts (Optional)

The guide in this section will only work if you have already deployed the CUR replication setup using the CloudFormation template above.

1. Login to the __Destination__ Account.
   
2. Find your existing template and choose __Update__

3. Check __Use current template__ then choose __Next__

4. Update the AWS Account IDs list to modify CUR aggregation (ADD or DELETE)
    
5. Select **Next** at the bottom of **Specify stack details** and then select **Next** again on the **Configure stack options** page

6.  Review the configuration, click **I acknowledge that AWS CloudFormation might create IAM resources, and click Create stack**.

7. You will see the stack will start with **UPDATE_IN_PROGRESS** 
   **NOTE:** This step can take up to 5mins
    ------------ | -------------

8.  Once complete, the stack will show **UPDATE_COMPLETE**

## Step 3. Enable QuickSight in Data Collection Account
QuickSight is the AWS Business Intelligence tool that will allow you to not only view the Standard AWS provided insights into all of your accounts, but will also allow to produce new versions of the Dashboards we provide or create something entirely customized to you. If you are already a regular QuickSight user you can skip these steps and move on to the next step. If not, complete the steps below.

1. Log into your Destination Linked Account and search for **QuickSight** in the list of Services

2. You will be asked to **sign up** before you will be able to use it

    ![QuickSight Sign up Workflow Image](https://wellarchitectedlabs.com/Cost/200_Cloud_Intelligence/Images/QS-signup.png?classes=lab_picture_small)

3. After pressing the **Sign up** button you will be presented with 2 options, please ensure you select the **Enterprise Edition** during this step

4. Select **continue** and you will need to fill in a series of options in order to finish creating your account. 

    + Ensure you select the region that is most appropriate based on where your S3 Bucket is located containing your Cost & Usage Report file.

        ![Select Region and Amazon S3 Discovery](/Cost/200_Cloud_Intelligence/Images/QS-s3.png?classes=lab_picture_small)
    
    + Enable the Amazon S3 option and select the bucket where your **Cost & Usage Report** is stored, as well as your **Athena** query bucket

        ![Image of s3 buckets that are linked to the QuickSight account. Enable bucket and give Athena Write permission to it.](/Cost/200_Cloud_Intelligence/Images/QS-bucket.png?classes=lab_picture_small)

5. Click **Finish** and wait for the congratulations screen to display

6. Click **Go to Amazon QuickSight**
![](/Cost/200_Cloud_Intelligence/Images/Congrats-QS.png?classes=lab_picture_small)
1. Click on the persona icon on the top right and select manage QuickSight. 
2. Click on the SPICE Capacity option. Purchase enough SPICE capacity so that the total is roughly 40GB. If you get SPICE capacity errors later, you can come back here to purchase more. If you've purchased too much you can also release it after you've deployed the dashboards. 

## Step 4. Deploy Dashboards in Data Collection Account
In this option we use guide you through using a CloudFormation template that will deploy **all needed resources**. You will cut and paste some parameters (An S3 path to CUR data, A QuickSight user that will be the owner of the QuickSight assets, and which dashboards you want to deploy) into the template and click run. 

All other resources are created automatically: Athena Workgroup and bucket, Glue table, Crawler, QS dataset, and finally the dashboards. The template uses a custom resource (a Lambda with [this CLI tool](https://github.com/aws-samples/aws-cudos-framework-deployment/)) to create, delete, or update assets. 

1. Login into your Linked (Data Collection) Account

2. Click the **Launch CloudFormation button** below to open the **pre-populated stack template** in your CloudFormation console and select **Next**

	- [Launch CloudFormation Template](https://console.aws.amazon.com/cloudformation/home#/stacks/create/review?&templateURL=https://aws-managed-cost-intelligence-dashboards.s3.amazonaws.com/cfn/cid-cfn.yml&stackName=Cloud-Intelligence-Dashboards&param_DeployCUDOSDashboard=yes&param_DeployKPIDashboard=yes&param_DeployCostIntelligenceDashboard=yes)
	
3. Enter a **Stack name** for your template such as **Cloud-Intelligence-Dashboards**
4. Review **Common Parameters** and confirm prerequisites before specifying the other parameters. You must answer 'yes' to both prerequisites questions.
5. Copy and paste your **QuicksightUserName** into the parameter text box.
To find your QuickSight username:
	- Open a new tab or window and navigate to the **QuickSight** console
	- Find your username in the top right navigation bar
![Images/cf_dash_qs_2.png](/Cost/200_Cloud_Intelligence/Images/cf_dash_qs_2.png?classes=lab_picture_small)

1. Update your **CURBucketPath** if needed. 
   1. (Default) If you used the CFN Template in the CUR setup process above then **CURBucketPath** needs to be the s3 path to the folder in your CUR bucket where account IDs are. For example `s3://cid-1234567890123-shared/cur/`
   2. If you did *not* use the CFN automated CUR setup process above and have just one CUR you setup manually then **CURBucketPath** needs to be the s3 path to the folder in your CUR bucket where the year folders are. For example `s3://cid-1234567890123-shared/prefix/name/name/` (double check this path, you must see /year=xxxx partitions in there).

   Please note that **CURBucketPath** parameter currently cannot be updated once the stack is created. If you need to change it you can delete and re-create the stack.

2. Select the Dashboards you want to install. We recommend deploying all three: Cost Intelligence Dashboard, CUDOS, and the KPI Dashboard.

3.  Review the configuration, click **I acknowledge that AWS CloudFormation might create IAM resources, and click Create stack**.

4.  You will see the stack will start in **CREATE_IN_PROGRESS** 
   **NOTE:** This step can take 5mins
    ------------ | -------------

1. Once complete, the stack will show **CREATE_COMPLETE**

2. While this is working, head back to QuickSight and click on manage Quicksight from the person icon on the top right. 

![quicksightpermissionss3_1](/Cost/200_Cloud_Intelligence/Images/quicksightpermissionss3_1.png?classes=lab_picture_small)

10. Select Security and permissions. Under QuickSight access to AWS services click manage. Select Athena, S3, and in S3 select both your CUR bucket **and** Query results bucket (Ex: will be something like `aws-athena-query-results-cid-1234567890123-us-east-1`). If you do not see this bucket, please check if it is created by the CloudFormation stack.

![quicksightpermissionss3_2](/Cost/200_Cloud_Intelligence/Images/quicksightpermissionss3_2.png?classes=lab_picture_small)

11. Navigate back to CloudFormation and to the **Output of the Stack** tab and check dashboard URLS. Click on a URL to open the dashboards.
   **NOTE:** This Output Section will be available once the Stack is Completed
    ------------ | -------------


If you see no data in QuickSight Dahsboards, plase check the following:
 1) Double check that QuickSight has permissions to read from your CUR bucket.
 2) In QuickSight, go to Datasets and click on Summary View. Check for errors (if you see a status `Failed`, you can click it to see more info).
 3) Check if CUR data has arrived to the S3 bucket. If you just created CUR you will need to wait 24 hours before the first data arrives. We also recommend creating a Support Case in Service=Billing and category=Invoices and Reporting, requesting a backfill of your CUR (name=cid) with 12 months of data. Case must be created from your Source Account (Management/Payer account).
 4) The QuickSight datasets refresh once per day at midnight, if your first CUR was delivered after midnight, you may need to click manual refresh on each dataset to see data in the dashboard. This will auto-resolve after midnight the next night.

{{% /expand%}}

## Alternative Setup
If you do not want to use CloudFormation to setup your dashboards, you have the option of using our command line tool or deploying manually. 

{{% button href="../alternative_deployments" %}}Visit Guide{{% /button %}}

---




{{< prev_next_button link_prev_url="../.." link_next_url="../post_deployment_steps" />}}


