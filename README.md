# FHIRTOOMOP
## Introduction
FHIR to OMOP is a Software as a Service offering that can be used to convert your
bulk FHIR files to the OMOP format. The service can be accessed and used from the platform https://portal.preview.isccloud.io/cloudservices/services.
In Part 1 of the exercise, the cloud solution will be configured.

The service pulls your Bulk FHIR data from an Amazon S3 bucket and converts it
into OMOP. A FHIR server will be set up in Part 2, and the bulk FHIR coordinator will be configured so that its output is placed in an S3 bucket in Part 3.

Once converted into OMOP, one can either access the converted data directly from the IRIS database or connect the OHDSI tools (such as Atlas and Hades) to the database, which will be done in Part 4.

## Part 1: Configuring the FHIR to OMOP cloud solution
### Step 1
Go to https://portal.preview.isccloud.io/cloudservices/services, sign up with
your email address and then click on Create Deployment under the Intersystems
OMOP Platform Card. It is possible that if you are creating a deployment for the first
time, it shows “Start Trial” instead of “Create Deployment”. You will then first have to
click on “Start Trial” and then if you try again after refreshing the page, you will see
“Create Deployment” this time.

### Step 2
A S3 bucket will be precreated for the workshop. Input properties contain the information of the S3 bucket. Set the Input S3 bucket to arn:aws:s3:::fhiromop, the Input S3 prefix to Transaction/in/${group_id} where you replace ${group_id} accordingly and set the input region to us-east-1. The Target properties allow you to create credentials to connect to the IRIS OMOP database. Create a new password for this deployment. Note that this passwordd is different than the password you use
to login to https://portal.preview.isccloud.io/cloudservices/services. The password
what we are setting here is for the specific deployment of FHIR to OMOP service we
will deploy shortly. Do not forget to enable external connections.

![image](https://github.com/user-attachments/assets/75b2c693-eedb-4f61-8774-4aadfb3bf88a)

### Step 3
Under Cloud Options and Deployment name, continue with the default values, then review and create your deployment. 
Go back to the deployments tab. You can see that your newly created
deployment is present there. It takes around 15 -20 minutes for the deployment to complete. Until then, the status for the deployment is shown as “Pending”. When the deployment is created, it should show as "Running". While the deployment is being created, you can start with Part 2 of the exercise until you reach step 3 for that part.

### Step 4
Your deployment should now show as running. Click on the
deployment and login using the deployment password you set up in the step 2. Go to configurations and copy the S3 bucket policy. In order to allow the FHIR to OMOP solution to access the S3 bucket, this policy should be added to the policies of the S3 bucket. As in this, workshop you won't have access to the S3 bucket directly, please mail the copied S3 bucket policy to jelle.michiels@intersystems.com. The bucket policy will be added to the S3 bucket for you. After this is done, continue with part 2. At the end of part 3, a bulk FHIR should be placed in the S3 bucket and be picked up by the FHIR to OMOP pipeline, after which we will come back to the cloud portal to view the results.

![image](https://github.com/user-attachments/assets/fc05b3ab-ca63-46c0-9145-b2df8e506c8d)

## Part 2: FHIR Server installation

### Step 1
We will start by installing a FHIR server and loading in test data. An IRIS for Health server has been made available on "http://${group_id}.isc-ohdsiworkshop.com/irishealth/csp/sys/UtilHome.csp" where you should replace your ${group_id} accordingly. Log in using username OHDSIUSER and password ohdsi1009. You are now logged in on the management portal of IRIS for Health. Make sure you are in the OHDSI namespace.

![image](https://github.com/user-attachments/assets/ac675a50-df20-4129-881c-27a2ca57a365)

Go to the health tab in order to configure the FHIR server.

![image](https://github.com/user-attachments/assets/684896d2-227c-4ff4-87d5-9974ecc20150)


### Step 2
You should now have the option to go to 

## Part 3: Bulk FHIR export

## Part 4: Running OHDSI/Broadsea tools


