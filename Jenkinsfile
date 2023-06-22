pipeline {
  agent any
  environment {
    CLOUDSDK_CORE_PROJECT='presales-infra-mod'
    CLIENT_EMAIL='jenkins-windows@presales-infra-mod.iam.gserviceaccount.com'
    GCLOUD_CREDS=credentials('presales-infra-mod')
    }
    stages {
      stage('Triger CloudBuild..') {
        steps {
          request:inline('''
          steps {
            # Creating a golden image
            - name:gcr.io/cloud-builders/gcloud
            id:create-golden-image
            args:- beta
            - compute
            - images
            - create
            - new-image-ver5
            - --source-disk=mig-windows-poc
            - --source-disk-zone=asia-south1-c
            - --project=presales-infra-mod
            - --force
            # Creating a new instance template
            - name:gcr.io/cloud-builders/gcloud
            id:create-instance-template
            args:- beta
            - compute
            - instance-templates
            - create
            - new-instance-template-ver5
            - --subnet=projects/presales-infra-mod/regions/asia-south1/subnetworks/cicd-subnet-1
            - --no-address
            - --image-project=presales-infra-mod
            - --maintenance-policy=MIGRATE
            - --service-account=jenkins-windows@presales-infra-mod.iam.gserviceaccount.com
            - --scopes=https://www.googleapis.com/auth/cloud-platform
            - --region="asia-south1"
            - --tags=mig-rolling-update
            - --image=new-image-ver5
            - --boot-disk-size=50
            - --boot-disk-device-name=mig-windows-poc
            - --project=presales-infra-mod
            # Make sure that our MIG is stable before update
            - name:gcr.io/cloud-builders/gcloud
            id:wait-until-stable
            args:- beta
            - compute
            - instance-groups
            - managed
            - wait-until
            - cicd-mig
            - --stable
            - --project=presales-infra-mod
            - --zone=asia-south1-c
            # Updating the MIG with new instance template
            - name:gcr.io/cloud-builders/gcloud
            id:updating-mig
            args:- beta
            - compute
            - instance-groups
            - managed
            - rolling-action
            - start-update
            - cicd-mig
            - --version=template=new-instance-template-ver5
            - --type=proactive
            - --zone=asia-south1-c
            - --project=presales-infra-mod 
            }
            ''')
            }
        }
    }
}
