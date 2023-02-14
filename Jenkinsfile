pipeline {
  agent any
  stages {
    stage('Triger CloudBuild..') {
      steps {
        googleCloudBuild credentialsId: '<ENTER-YOUR-CREDS-ID>' , request: inline('''steps:
        # Creating a golden image
        - name: gcr.io/cloud-builders/gcloud
          id: create-golden-image
          args:
          - beta
          - compute
          - images
          - create                                                                                 
          - <ENTER-NEW-IMAGE-NAME>
          - --source-disk=<ENTER-YOUR-VM-SOURCEDISK-NAME>
          - --source-disk-zone=<ENTER-YOUR-SOURCE-DISK-ZONE>
          - --project=<ENTER-YOUR-PROJECT-ID>
          - --force
        #Creating a new instance template
        - name: gcr.io/cloud-builders/gcloud
          id: create-instance-template
          args:
          - beta
          - compute
          - instance-templates
          - create
          - <ENTER-YOUR-NEW-TEMPLATE-NAME>
          - --subnet=projects/<ENTER-YOUR-PROJECT-ID>/regions/<ENTER-YOUR-REGION-NAME>/subnetworks/<ENTER-YOUR-SUBNET-NAME>
          - --no-address
          - --image-project=<ENTER-YOUR-PROJECT-ID>
          - --maintenance-policy=MIGRATE
          - --service-account=<ENTER-YOUR-SERVICE-ACCOUNT-NAME>
          - --scopes=https://www.googleapis.com/auth/cloud-platform
          - --region=<ENTER-REGION-NAME>
          - --tags=mig-rolling-update
          - --image=<ENTER-NEW-IMAGE-NAME>
          - --boot-disk-size=<ENTER-BOOT-DISK-SIZE>
          - --boot-disk-type=<ENTER-DISK-SIZE>
          - --boot-disk-device-name=<ENTER-BOOT-DISK-NAME>
          - --project=<ENTER-YOUR-PROJECT-ID>
        #Make sure that our MIG is stable before update
        - name: gcr.io/cloud-builders/gcloud
          id: wait-until-stable
          args:
          - beta
          - compute
          - instance-groups
          - managed
          - wait-until
          - <ENTER-YOUR-MIG-NAME>
          - --stable
          - --project=<ENTER-YOUR-PROJECT-ID>
          - --zone=<ENTER-YOUR-ZONE-NAME>
        #Updating the MIG with new instance template
        - name: gcr.io/cloud-builders/gcloud
          id: updating-mig
          args:
          - beta
          - compute
          - instance-groups
          - managed
          - rolling-action
          - start-update
          - <ENTER-YOUR-MIG-NAME>
          - --version=template=<ENTER-YOUR-TEMPLATE-NAME>
          - --type=proactive
          - --zone=<ENTER-YOUR-ZONE-NAME>
          - --project=<ENTER-YOUR-PROJECT-ID>''')
      }
    }
  }
}
