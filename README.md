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
A S3 bucket will be precreated for the workshop. Input properties contain the information of the S3 bucket. Set the Input S3 bucket to arn:aws:s3:::fhiromop, the Input S3 prefix to Transaction/in/${group_id} where you replace ${group_id} accordingly and set the input region to us-east-1. The Target properties allow you to create credentials to connect to the IRIS OMOP database. Create a new password for this deployment. Note that this password is different than the password you use
to login to https://portal.preview.isccloud.io/cloudservices/services. The password
what we are setting here is for the specific deployment of FHIR to OMOP service we
will deploy shortly. Do not forget to enable external connections.

![image](https://github.com/user-attachments/assets/75b2c693-eedb-4f61-8774-4aadfb3bf88a)

### Step 3
Under Cloud Options and Deployment name, continue with the default values, then review and create your deployment. 
Go back to the deployments tab. You can see that your newly created
deployment is present there. It takes around 15 -20 minutes for the deployment to complete. Until then, the status for the deployment is shown as “Pending”. When the deployment is created, it should show as "Running". While the deployment is being created, you can start with Part 2 of the exercise until you have completed step 3 for that part.

### Step 4
Your deployment should now show as running. Click on the
deployment and login using the deployment password you set up in the step 2. Go to configurations and copy the S3 bucket policy. In order to allow the FHIR to OMOP solution to access the S3 bucket, this policy should be added to the policies of the S3 bucket. As in this, workshop you won't have access to the S3 bucket directly, please mail the copied S3 bucket policy to jelle.michiels@intersystems.com. The bucket policy will be added to the S3 bucket for you. After this is done, continue with part 2. At the end of part 3, a bulk FHIR should be placed in the S3 bucket and be picked up by the FHIR to OMOP pipeline, after which we will come back to the cloud portal to view the results.

![image](https://github.com/user-attachments/assets/fc05b3ab-ca63-46c0-9145-b2df8e506c8d)

## Part 2: FHIR Server installation

### Step 1
We will start by installing a FHIR server and loading in test data. An IRIS for Health server has been made available on "http://${group_id}.isc-ohdsiworkshop.com/irishealth/csp/sys/UtilHome.csp" where you should replace your ${group_id} accordingly. Log in using username OHDSIUSER and password ohdsi1009. You are now logged in on the management portal of IRIS for Health. Make sure you are in the OHDSI namespace. If not, on the top click on the namespace specified next to namespace in order to switch.

Go to the health tab in order to configure the FHIR server.

![image](https://github.com/user-attachments/assets/684896d2-227c-4ff4-87d5-9974ecc20150)
![image](https://github.com/user-attachments/assets/4a304bba-7451-4581-9a1d-17d1054c6f5f)


### Step 2
There, on the left side, select FHIR configuration. This should now show a screen with two options. Package configuration allows you to configure a FHIR server so it uses specified FHIR packages (for instance, the Belgian eHealth profiles (https://www.ehealth.fgov.be/standards/fhir/index.html). Server configuration allows you to set up a FHIR server. Click on server configuration.

![image](https://github.com/user-attachments/assets/9ef8f9df-6abb-4b60-919e-c3cc5a71ad73)

### Step 3

Click on add endpoint and select the core FHIR package to be hl7.fhir.r4.core@4.0.1. It should automatically propose the following url: /csp/healthshare/ohdsi/fhir/r4. Create the endpoint with the default settings. While the endpoint is being created, check back at Part 1 if the FHIR to OMOP deployment is already created and complete step 4 of Part 1.

![image](https://github.com/user-attachments/assets/898bef78-83f2-4dc7-8155-61a70a37a60a)

Wait for the endpoint to be created. Once it is created, click on the endpoint and click on edit at the bottom in order to turn on Allow Unauthenticated Access (for clinfhir in step 6) and then click update to save the changes.

### Step 4

Wait for the endpoint to be created. In the next step, we will load synthetic data generated by Synthea (https://synthetichealth.github.io/synthea/) into the FHIR server. In order to do so, go to the webterminal which is installed next to IRIS on "http://${group_id}.isc-ohdsiworkshop.com/irishealth/terminal/", log in using your credentials (username:OHDSIUSER, password:ohdsi1009) and execute the following commands one by one:
```ObjectScript
zn "OHDSI"
do ##class(HS.FHIRServer.Tools.DataLoader).SubmitResourceFiles("C:/Users/Administrator/Documents/fhirout","FHIRServer", "/csp/healthshare/ohdsi/fhir/r4")
```
The output should show the different patients being loaded.

### Step 5
We will explore the FHIR server briefly. The FHIR server specification can be found at https://build.fhir.org/http.html. GET and POST statements, for instance, can be used to retrieve resources from the server and post resources on the server respectively.

If you have Postman installed locally, create a new GET request to the FHIR endpoint "http://${group_id}.isc-ohdsiworkshop.com/irishealth/csp/healthshare/ohdsi/fhir/r4/Patient". Otherwise, you can access the online version of Postman (https://www.postman.com) or use an API client of your choice. Under Authorization, select Basic Authentication and use the credentials used to connect to the IRIS instance on which the FHIR server is hosted. This request will return a Bundle resource containing Patient resources on the FHIR server. 

Additionaly, you can add search parameters to your query. For example, "http://${group_id}.isc-ohdsiworkshop.com/irishealth/csp/healthshare/ohdsi/fhir/r4/Patient?gender=male" will return all male patients only. "http://${group_id}.isc-ohdsiworkshop.com/irishealth/csp/healthshare/ohdsi/fhir/r4/Patient?given=Loyd" should return a Patient with given name Loyd. Note the resource id ${id} of the Patient (not of the Bundle itself). Quering "http://${group_id}.isc-ohdsiworkshop.com/irishealth/csp/healthshare/ohdsi/fhir/r4/Patient/${id}" for the given id return the same patient.

Finally, query "http://${group_id}.isc-ohdsiworkshop.com/irishealth/csp/healthshare/ohdsi/fhir/r4/Patient/${id}/$everything". The $everything operation will return all related resources for a patient in a Bundle. What would be the equivalent tables for these resources in OMOP?

### Optional: Step 6
Go to clinfhir.com. ClinFHIR is an open-source tool providing an educational environment and also allows health information technology developers to create or search for Fast Healthcare Interoperability Resources® (FHIR®)-based resources. On the right, add the FHIR server you just created. Give it the name IRISFHIRServer and enter the server url (http://${group_id}.isc-ohdsiworkshop.com/irishealth/csp/healthshare/ohdsi/fhir/r4). Select the correct FHIR version and select that the server supports the $everything. Add the server. Now click on edit (in the top right corner) and change the data server to the one we just created.

![image](https://github.com/user-attachments/assets/ba98bd0f-bf0e-45f1-9817-ad18df7bf111)

Now you can open the Patient Viewer (under for Implementers in the left part). Click on Select patient 



## Part 3: Bulk FHIR export
### Step 1
First, configure the credentials that the Bulk FHIR Coordinator uses to connect to the FHIR Server. In order to do so, go to management portal again (ttp://${group_id}.isc-ohdsiworkshop.com/irishealth/csp/sys/UtilHome.csp). Go to Interoperability <- Configure <- Credentials. There, create a new credential with ID OHDSICredentials and your username and password (OHDSIUSER and ohdsi1009). Save this credential.

### Step 2
Go to the Health tab in the management portal again. Now go to Bulk FHIR Coordinator UI. Create a new configuration by clicking on the blue "New Configuration" and selecting "Create new"

Under configuration setting, set the name to "BulkFHIRCoordinator" and the BFC endpoint to "/bulk/r4". 

Under Authorization types, select the BasicAuth adapter. This enables you to create bulk FHIR exports using your credentials. 

Under Fetch type, select HS.BulkFHIR.Fetch.PureFHIR.Adapter. Select the Endpoint url to be the same as the one for the FHIR server you created in Part 2 (http://${group_id}.isc-ohdsiworkshop.com/irishealth/csp/healthshare/ohdsi/fhir/r4). Set the Authorization type to be HTTP, allowing to connect to the FHIR server using Basic Authentication. Select the HTTP credentials you created in step 1.

Finally, under Storage location, set the file url to be "/bulkfhir/file". This is an endpoint where the Bulk FHIR export files can be retrieved. Make a note of the Directory for later.
Review your settings and then press configure to set up the Bulk FHIR coordinator.

### Step 3
Go back to the homepage of the management portal. There, click on Interoperability <- Configure <- Production. First, click on recover and click ok when prompted. Now, you can go ahead and start the production. 

The main functionality of this production is to retrieve the output of a Bulk FHIR export and post it onto the S3 bucket which will trigger the FHIR to OMOP pipeline configured in Part 1. First, we will add a business operation which is responsible of sending the Bulk FHIR export zip file to the S3 Bucket. Click on the plus symbol next to the operations column. Now add the following configuration: 
- Operation Class: EnsLib.CloudStorage.BusinessOperation
- Operation Name: S3Upload
- Enable Now: true

![image](https://github.com/user-attachments/assets/146cf8ce-a021-44dc-82a9-95e4cad35484)

Now, under Processes, click on ToS3Upload. Then, on the right (in the Settings tab for this component), look for the BucketSubfolder setting under Basic Settings. Set this to be Transaction/in/${group_id} where group_id should be replaced accordingly (example Transaction/in/g1). Then click apply. Under informational settings, you can click on the looking glass next to the Class Name. This will open the BPL code (a graphical editor) for this business process.

Finally, back in the production, click on the service with name BulkFHIRFileService. Under Settings for this component, make sure the File Path equals the one you got from the Bulk FHIR Coordinator set-up in the final part of step 2. Enable the business service and click apply. Don't worry if the Business Service turns red. This is because the folder for the Bulk FHIR export will only be created at the first time of an export.

### Step 4
The other components in the production are responsible of orchestrating the Bulk FHIR export. First, go (back in the homepage of the management portal) to Interoperability <- Configure <- System Default Settings. Here, at the settings to be used by the CreateGroup and Orchestrator process. Create two settings by clicking New and setting: 
- Setting Name: SessionApplication
- Setting Value: /csp/healthshare/ohdsi/fhir/r4 (the FHIR endpoint)
and
  - Setting Name: BaseURL
  - Setting Value: http://${group_id}.isc-ohdsiworkshop.be/irishealth/csp/healthshare/ohdsi/fhir/r4 (the full FHIR URL)




## FHIR to OMOP results












