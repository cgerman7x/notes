# Introduction
**CI/CD** integrates managing source code, provisioning the IT infrastructure, compiling, packaging, testing, and deploying into a pipeline.

**DevOps** combines development and operations. It must ensure identical environments at the end.

**IaC** infrastructure as code means that you can use version control and source code revision systems to manage your code.

**HCL** HashiCorp Configuration Language

# Terraform Basics
**Block** is a container for other content. It has two labels: the first one is the provider and resource (ie. google_compute_instance), and the second one is the ID, the name you give.

The body of the block is nested within { }.

**Workflow** usually involves:

```
terraform init
terraform plan --out=plan
terraform apply plan
terraform destroy
```

**Authentication methods**

***Authentication using a service account***

It relies on a .json file containing the service account key.
```
$ gcloud iam service-accounts create terraform \
    --description="Terraform Service Account" \
    --display-name="terraform"

$ export GOOGLE_SERVICE_ACCOUNT=`gcloud iam service-accounts \
    list --format="value(email)"  --filter=name:"terraform@"`

$ export GOOGLE_CLOUD_PROJECT=`gcloud info \
    --format="value(config.project)"`

$ gcloud projects add-iam-policy-binding $GOOGLE_CLOUD_PROJECT \
    --member="serviceAccount:$GOOGLE_SERVICE_ACCOUNT" \
    --role="roles/editor"

$ gcloud iam service-accounts keys create "./terraform.json"  \
  --iam-account=$GOOGLE_SERVICE_ACCOUNT    
```

***Authentication using a service account impersonation***

It relies on the user acting as the impersonated service account. For that, first the user must have the roles/iam.serviceAccountTokenCreator role assigned. Second, the Application Default Credentials (ADC) need to be configured.

```
$ export GOOGLE_IMPERSONATE_SERVICE_ACCOUNT=\
    `gcloud iam service-accounts list --format="value(email)" \
    --filter=name:terraform`

$ gcloud auth application-default login --no-launch-browser

$ export USER_ACCOUNT_ID=`gcloud config get core/account`

$ gcloud iam service-accounts add-iam-policy-binding \
    $GOOGLE_IMPERSONATE_SERVICE_ACCOUNT \
    --member="user:$USER_ACCOUNT_ID" \
    --role="roles/iam.serviceAccountTokenCreator"

$ export GOOGLE_CLOUD_PROJECT=`gcloud info --format="value(config.project)"`    
```

**Variables** by convention, are defined in variables.tf file. Their values can be set usually using a .tfvars file.

```
project_id = <PROJECT_ID>
server_name = <SERVER_NAME>
```

We can also override variables while invoking terraform:

```
terraform apply -var machine_type=e2_medium
```
