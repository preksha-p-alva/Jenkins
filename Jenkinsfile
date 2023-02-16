pipeline {
    agent any
    stages {
        stage('Triger CloudBuild..') {
            steps {
                googleCloudBuild credentialsId:'Presales - Infra Mod' , request:inline(''' steps:
                // Creating a golden image
                - name:gcr.io/cloud-builders/gcloud
                id:create-golden-image
                args
                - beta
                - compute
                - images
                - create                                                                                 
                - demo-image-1
                - --source-disk=demo-vm-instance-1
                - --source-disk-zone=asia-south1-c
                - --project=presales-infra-mod
                - --force
                // Creating a new instance template
                - name:gcr.io/cloud-builders/gcloud
                id:create-instance-template
                args
                - beta
                - compute
                - instance-templates
                - create
                - example-template-custom-1
                - --subnet=projects/presales-infra-mod/regions/asia-south1/subnetworks/subnet-1
                - --no-address
                - --no-address
                - --image-project=presales-infra-mod
                - --maintenance-policy=MIGRATE
                - --service-account=jenkins@presales-infra-mod.iam.gserviceaccount.com
                - --scopes=https://www.googleapis.com/auth/cloud-platform
                - --region=asia-south1
                - --tags=mig-rolling-update
                - --image=demo-image-1
                - --boot-disk-size=10
                - --boot-disk-type=New balanced persistent disk
                - --boot-disk-device-name=demo-vm-instance-1
                - --project=presales-infra-mod
                // Make sure that our MIG is stable before update
                - name:gcr.io/cloud-builders/gcloud
                id:wait-until-stable
                args
                - beta
                - compute
                - instance-groups
                - managed
                - wait-until
                - demo-mig
                - --stable
                - --project=presales-infra-mod
                - --zone=asia-south1-c
                // Updating the MIG with new instance template
                - name:gcr.io/cloud-builders/gcloud
                id:updating-mig
                args
                - beta
                - compute
                - instance-groups
                - managed
                - rolling-action
                - start-update
                - demo-mig
                - --version=template=example-template-custom-1
                - --type=proactive
                - --zone=asia-south1-c
                - --project=presales-infra-mod ''')
            }
        }
    }
}



