---
title: Running a python serverless function in GCP
---

Prerequistes:
    must have a gcp account with billing enabled (valid billing account)
    npm and node
    python 2.7+

install severless global npm package --> creates sls cli

view current templates available --> sls create --help

skimming through we want to use the google-python template

run serverless create -n gcp-python-example -t google-python to create templated repo

you can look through the created repo to see what files were created

the hardest part for setting up a gcp serverless functions is creating the correct service account and credentials

They have supporting documentation for this here - https://serverless.com/framework/docs/providers/google/guide/credentials/

Here is a trimmed down version assuming you already have a gcp account / project setup with billing enabled

You need to enabled these apis for the project - https://console.cloud.google.com/apis/dashboard
Cloud Functions API
Cloud Deployment Manager V2 API
Cloud Build API
Cloud Storage
Cloud Logging API

Setup a service account with the appropriate permissions:

    Go to here - https://console.cloud.google.com/iam-admin/serviceaccounts
    Select the project you want to create the service account for
    Click create service account
    Give SA name serverless-python-example
    Click create and continue

    add these roles to the service account - note: you will have to 'add another role'
        Deployment Manager Editor
        Storage Admin
        Logging Admin
        Cloud Functions Developer

    You do not need to grant any user access to this service account

    Click 'done' and create the service account

Create credentials file for using gcp as the service account

    Click on newly created service account in sa view
    Go to the 'keys' tab
    Click 'Add Key' -> 'Create New Key'
    Make sure the key type is JSON
    Click Create

    This should download a json file to your local computer, these are the credentials serverless will use to deploy the function

Rename the file to better reflect the credentials -> project_name-gcp-python-example-sa.json

The last step is to update the serverless.yaml file

Get the project ID (not neccessarily the same as the project name) of the project you created the service account under
Update the project field in the serverless.yaml file with the id

Get the full absolute path to where the service account .json creds file is located on your machine
Update the credentials field in the serverless.yaml file with the full path

    Tip: If you are using windows with WSL2 like I am you can reference your native windows drives by prefixing with /mnt/<drive_letter>
    so the C: drive would be /mnt/c/users/.../project_name-gcp-python-example-sa.json

Install the needed serverless plugin - sls plugin install -n serverless-google-cloudfunctions
You can see where this plugin is defined in thee serverless.yaml file

Deploy your function with 'sls deploy'

serverless invoke --function first --stage dev --region us-central1
