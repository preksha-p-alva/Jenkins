pipeline {
  agent any
  stages {
    stage('Triger CloudBuild..') {
      steps {
        googleCloudBuild credentilsId: '4bd5d4b7-3d09-43b5-8089-8c306f1d9fe2' , 
        request: inline(' ' ' steps:
        // Creating a golden image
        - name: gcr.io/cloud-builders/gcloud
          id: create-golden-image
          args:
          - beta
          - compute
          - images
          - create                                                                                 
          - demo-image
          - --source-disk=demo-vm-instance
          - --source-disk-zone=asia-south1-c
          - --project=presales-infra-mod
          - --force
        // Creating a new instance template
        - name: gcr.io/cloud-builders/gcloud
          id: create-instance-template
          args:
          - beta
          - compute
          - instance-templates
          - create
          - nginx-template
          - --subnet=projects/presales-infra-mod/regions/asia-south1/subnetworks/subnet-1
          - --no-address
          - --no-address
          - --image-project=presales-infra-mod
          - --maintenance-policy=MIGRATE
          - --service-account=jenkins-cicd@presales-infra-mod.iam.gserviceaccount.com
          - --scopes=https://www.googleapis.com/auth/cloud-platform
          - --region=asia-south1
          - --tags=mig-rolling-update
          - --image=demo-image
          - --boot-disk-size=10
          - --boot-disk-type=New balanced persistent disk
          - --boot-disk-device-name=demo-vm-instance
          - --project=presales-infra-mod
        // Make sure that our MIG is stable before update
        - name: gcr.io/cloud-builders/gcloud
          id: wait-until-stable
          args:
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
        - name: gcr.io/cloud-builders/gcloud
          id: updating-mig
          args:
          - beta
          - compute
          - instance-groups
          - managed
          - rolling-action
          - start-update
          - demo-mig
          - --version=template=nginx-template
          - --type=proactive
          - --zone=asia-south1-c
          - --project=presales-infra-mod' ' '
      }
    }
  }
}
