###################################################################################################################
# Documentation - Learning GCP 
###################################################################################################################
# https://cloud.google.com/storage/docs/authentication#client-libs
# https://cloud.google.com/python/docs/setup#linux

# Install the gcloud CLI [https://cloud.google.com/sdk/docs/install#deb]
```
$ sudo apt-get update -y && sudo apt-get upgrade -y
$ sudo apt-get install apt-transport-https ca-certificates gnupg curl
$ curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo gpg --dearmor -o /usr/share/keyrings/cloud.google.gpg
$ echo "deb [signed-by=/usr/share/keyrings/cloud.google.gpg] https://packages.cloud.google.com/apt cloud-sdk main" | sudo tee -a /etc/apt/sources.list.d/google-cloud-sdk.list
$ sudo apt-get update && sudo apt-get install google-cloud-cli
```

# Setting up a Python development environment
$ sudo apt update
$ sudo apt install python3 python3-dev python3-venv python3-pip
$ pip3 --version
$ mkdir aws-gcp-federated-demo
$ cd aws-gcp-federated-demo
$ python3 -m venv env
$ source env/bin/activate
$ pip install google-cloud-storage

# Attributes for AWS Provider
google.subject=assertion.arn
attribute.account=assertion.account
attribute.aws_role=assertion.arn.extract('assumed-role/{role}/')
attribute.aws_ec2_instance=assertion.arn.extract('assumed-role/{role_and_session}').extract('/{session}')
ex:
   arn:aws:iam::932589472370:role/main_role
   arn:aws:sts::932589472370:assumed-role/main_role

# Binding Attributes
gcloud storage buckets add-iam-policy-binding BUCKET_ID \
    --role=roles/storage.objectViewer \
    --member="principalSet://iam.googleapis.com/projects/522908643793/locations/global/workloadIdentityPools/POOL_ID/attribute.ATTRIBUTE_NAME/ATTRIBUTE_VALUE"

Ex: gcloud storage buckets add-iam-policy-binding gs://myexamplebucket4 \
    --role=roles/storage.objectViewer \
    --member="principal://iam.googleapis.com/projects/522908643793/locations/global/workloadIdentityPools/aws-gcp-pool-02/subject/SUBJECT_ATTRIBUTE_VALUE"

# Generate an AWS configuration file.
gcloud iam workload-identity-pools create-cred-config \
   projects/522908643793/locations/global/workloadIdentityPools/aws-gcp-pool/providers/aws-gcp-pool-provider \
   --service-account main-svc-account@main-gcp-de.iam.gserviceaccount.com \
   --aws \
   --output-file config.json \
   --enable-imdsv2

$ export GOOGLE_APPLICATION_CREDENTIALS=config.json
$ export GOOGLE_CLOUD_PROJECT=main-gcp-de

# DOCKER COMMANDS:
    docker build -t ssadcloud/myapp:latest .
    docker push ssadcloud/myapp:latest
    docker pull ssadcloud/myapp:latest
    docker run -p 8080:8080 ssadcloud/myapp:latest

#################################################################
# INSTALLATIONS
#################################################################
# SSH CONNECT TO MAIN K8S INSTANCE
1. # SSH CONNECT TO MAIN K8S INSTANCE

2. # After connecting to Machince,
   $ sudo su
   $ cd
   $ mkdir <<name>>
   $ cd <<name>>

3. # Version Control System - GIT
    Key page: https://git-scm.com/download/linux

    $ sudo apt-get install git -y
    $ git --version

4. # Build Tool - JDK - Java and Maven
   $ sudo apt-get update
   $ sudo apt-get install openjdk-17-jdk -y
   $ ls /usr/lib/jvm/
   $ nano ~/.bashrc
   ```bash
      export JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64
      export PATH=$JAVA_HOME/bin:$PATH
   ```
   $ source ~/.bashrc
   $ echo $JAVA_HOME
   $ which java

   $ sudo apt-get install maven -y   # Maven Installation
   $ mvn --version

5.  # JENKINS INSTALLAITON
    https://pkg.jenkins.io/debian-stable/

    $   sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
    https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key

    $   echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]" \
    https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
    /etc/apt/sources.list.d/jenkins.list > /dev/null

    $   sudo apt-get update
    $   sudo apt-get install fontconfig openjdk-17-jre
    $   sudo apt-get install jenkins
    $   cat /var/lib/jenkins/secrets/initialAdminPassword

5. # For Ubuntu(debian)
```bash
    $ sudo su
    cd
    apt-get update -y
    apt-get install docker.io -y

```
6. # KUBECTL INSTALLATION ON AWS UBUNTU LINUX MACHINE
```bash 
    $ curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
    $ sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```
gcloud beta container --project "main-gcp-de" clusters create "main-k8s-standard-cluster" --region "us-central1" --tier "standard" --no-enable-basic-auth --cluster-version "1.31.5-gke.1023000" --release-channel "regular" --machine-type "e2-medium" --image-type "COS_CONTAINERD" --disk-type "pd-balanced" --disk-size "100" --metadata disable-legacy-endpoints=true --service-account "main-svc-account@main-gcp-de.iam.gserviceaccount.com" --num-nodes "3" --logging=SYSTEM,WORKLOAD --monitoring=SYSTEM,STORAGE,POD,DEPLOYMENT,STATEFULSET,DAEMONSET,HPA,CADVISOR,KUBELET --enable-ip-alias --network "projects/main-gcp-de/global/networks/default" --subnetwork "projects/main-gcp-de/regions/us-central1/subnetworks/default" --no-enable-intra-node-visibility --default-max-pods-per-node "110" --enable-ip-access --security-posture=standard --workload-vulnerability-scanning=disabled --no-enable-master-authorized-networks --no-enable-google-cloud-access --addons HorizontalPodAutoscaling,HttpLoadBalancing,GcePersistentDiskCsiDriver --enable-autoupgrade --enable-autorepair --max-surge-upgrade 1 --max-unavailable-upgrade 0 --binauthz-evaluation-mode=DISABLED --enable-managed-prometheus --enable-shielded-nodes

################################################################################################
# CI/CD RELEASE JENKINS INTEGRATION 
################################################################################################
A. Build Source Code ( Using Jenkins)
     $ clean package

B. Push Images to DOCKER HUB
```bash
   sudo docker build -t <<name-of-docker-hub>>\myapp .
   sudo docker push <<name-of-docker-hub>>\myapp:latest
```

C. Push Images to AWS GAR
```bash
    sudo aws ecr get-login-password --region us-east-2 | sudo docker login --username AWS --password-stdin 932589472370.dkr.ecr.us-east-2.amazonaws.com
    sudo docker build -t myapp-ecr .
    sudo docker tag myapp-ecr:latest 932589472370.dkr.ecr.us-east-2.amazonaws.com/myapp-ecr:latest
    sudo docker push 932589472370.dkr.ecr.us-east-2.amazonaws.com/myapp-ecr:latest
```
D. For Deployment 
  # Delete Previous Deployment/Services
    $ sudo kubectl delete -f manifests/myapp-service.yaml
    $ sudo kubectl delete -f manifests/myapp-deployment.yaml

  # Create Deployments and Services
    $ sudo kubectl apply -f manifests/myapp-deployment.yaml
    $ sudo kubectl apply -f manifests/myapp-service.yaml

E. GITHUB and JENKINS WEBHOOK:
Use when you want to trigger JENKINS job upon commits to GitHub Repo

   $ http://<<public-ip or public-dns-jenkins>>8080/github-webhook/

F. Grant Jenkins sudo previlages
    $ sudo nano /etc/sudoers
    ## Now add the below lines in your sudoers file :
    jenkins ALL=(ALL) NOPASSWD: ALL
