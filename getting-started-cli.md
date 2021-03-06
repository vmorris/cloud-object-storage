---

copyright:
  years: 2017
lastupdated: "2017-12-01"

---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:codeblock: .codeblock}
{:pre: .pre}
{:screen: .screen}
{:tip: .tip}


# Getting started (CLI)
In this quickstart guide, you'll create a bucket and upload objects, and set up access policies to allow other users to work with your data.
{: shortdesc}

## Before you begin
You'll need:  
  * an [{{site.data.keyword.cloud}} Platform account](https://cloud.ibm.com/registration/?target=%2Fcatalog%2Fservices%2Fcloud-object-storage)  
  * an [instance of {{site.data.keyword.cos_full}}](/docs/services/cloud-object-storage/basics/order-storage.html)  
  * the [{{site.data.keyword.cloud_notm}} CLI](https://cloud.ibm.com/docs/cli/index.html#overview)  
  * and some files on your local computer to upload.
{: #prereqs}

Looking for a walkthrough that uses the console instead of the CLI? [Click here](/docs/services/cloud-object-storage/getting-started.html#getting-started-console-).
{:tip}

## Gather key information
  1. First, make sure you have an API key.  Get this from [IBM Cloud Identity and Access Management](https://cloud.ibm.com/iam#/apikeys).
  1. Login to the {{site.data.keyword.cloud_notm}} Platform using the CLI.

    For increased security, it's also possible to store the API key in a file or set it as an environment variable.
{:tip}

```
bx login --apikey <value>
```
{:codeblock}

```
Authenticating...
OK

Targeted account <account-name> (<account-id>)

Targeted resource group default

API endpoint:     https://api.ng.cloud.ibm.com (API version: 2.75.0)
Region:           us-south
User:             <email-address>
Account:          <account-name> (<account-id>)
Resource group:   default
```

1. Now you need the name and ID for your new instance. Use the name you gave the instance when creating it.

```
bx resource service-instances
```
{:codeblock}

```
Retrieving service instances in resource group Default and all locations under account <account-name> as <email-address>...
OK
Name                                               Location     State    Type               Tags
<resource-instance-name>                           global       active   service_instance
```

```
bx resource service-instance <instance-name>
```
{:codeblock}

```
Retrieving service instance <sinstance-name> in resource group Default under account <account-name> as<email-address>...
OK

Name:                  <resource-instance-name>
ID:                    <resource-instance-id>
Location:	             global
Service Name:          cloud-object-storage
Resource Group Name:   Default
State:                 active
Type:                  service_instance
Tags:
```

    **Note**: You may need to change the **Resource Group Name** in the case you created your instance in a group other than **Default**. You can list your available resource groups with `bx resource groups` then change to a specific resource group with `bx target -g "<another-resource-group>"`    {:tip}
    

  1. Next, get a token from IAM.

```
bx iam oauth-tokens
```
{:codeblock}

```
IAM token:  Bearer <token>
UAA token:  Bearer <refresh-token>
```

## Create a bucket and upload an object

  1. Take your new token, and the ID of the instance, and create a new bucket in the `us-south` region.

    **Note**: Personally Identifiable Information (PII): When creating buckets and/or adding objects, please ensure to not use any information that can identify any user (natural person) by name, location or any other means in the name of the bucket or object.
    {:tip}

```sh
  curl -X "PUT" "https://s3.us-south.objectstorage.softlayer.net/<bucket-name>" \
       -H "Authorization: Bearer <token>" \
       -H "ibm-service-instance-id: <resource-instance-id>"
```
{:codeblock}

  1. Upload an object.

```sh
  curl -X "PUT" "https://s3.us-south.objectstorage.softlayer.net/<bucket-name>/<object-key>" \
       -H "Authorization: Bearer <token>" \
       -H "Content-Type: text/plain; charset=utf-8" \
       -d "This is a tiny object made of plain text."
```
    {:codeblock}

## Manage access

  1. Invite someone to your account with minimal permissions.

```
bx account user-invite <email-address> <org-name> auditor <space-name> auditor
```
{:codeblock}

  1. Then grant them read-only access to your {{site.data.keyword.cos_short}} instances.

```
bx iam user-policy-create <email-address> --roles Reader --service-name cloud-object-storage
```
{:codeblock}

  1. Grant them write access to the bucket you created.

```
bx iam user-policy-create <email-address> --roles Writer --service-name cloud-object-storage --resource-type bucket--resource <bucket-name>
```
{:codeblock}

Want to learn more?  [Read more of the documentation](/docs/services/cloud-object-storage/about-cos.html).
