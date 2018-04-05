---
title: Azure Snabbstart – CNTK-utbildning med Batch AI – Python | Microsoft Docs
description: Lär dig att snabbt köra ett CNTK-utbildningsjobb med Batch AI med hjälp av Python-SDK:n
services: batch-ai
documentationcenter: na
author: lliimsft
manager: Vaman.Bedekar
editor: tysonn
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: Python
ms.topic: quickstart
ms.date: 10/06/2017
ms.author: lili
ms.openlocfilehash: f535c9adf4926f29ae9cade6382debedab73937d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="run-a-cntk-training-job-using-the-azure-python-sdk"></a>Kör ett CNTK-utbildningsjobb med hjälp av Azure Python-SDK:n

Den här snabbstarten visar dig hur du kan använd Azure Python-SDK:n för att köra ett utbildningsjobb med Microsoft Cognitive Toolkit (CNTK) med hjälp av Batch AI-tjänsten. 

I det här exemplet använder du MNIST-databasen med handritade bilder för att träna ett CNN (Convolutional Neural Network) i ett GPU-kluster med en nod. 

## <a name="prerequisites"></a>Nödvändiga komponenter

* Azure-prenumeration – Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 

* Azure Python SDK. Mer information finns i [Installationsanvisningar](/python/azure/python-sdk-azure-install)

* Azure-lagringskonto. Mer information finns i [Skapa ett Azure-lagringskonto](../storage/common/storage-create-storage-account.md)

* Azure Active Directory – autentiseringsuppgifter för tjänstens huvudnamn. Mer information finns i [Hur du skapar ett tjänstens huvudnamn med CLI](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md)

* Registrera Batch AI-resursprovidrarna en gång för din prenumeration med hjälp av Azure Cloud Shell eller Azure CLI. En providerregistrering kan ta upp till 15 minuter.

  ```azurecli
  az provider register -n Microsoft.BatchAI
  az provider register -n Microsoft.Batch
  ```


## <a name="configure-credentials"></a>Konfigurera autentiseringsuppgifter
Skapa dessa parametrar i ditt Python-skript och sätt in dina egna värden:

```Python
# credentials used for authentication
client_id = 'my_aad_client_id'
secret = 'my_aad_secret_key'
token_uri = 'my_aad_token_uri'
subscription_id = 'my_subscription_id'

# credentials used for storage
storage_account_name = 'my_storage_account_name'
storage_account_key = 'my_storage_account_key'

# specify the credentials used to remote login your GPU node
admin_user_name = 'my_admin_user_name'
admin_user_password = 'my_admin_user_password'
```

Som bästa praxis bör alla autentiseringsuppgifter lagras i en separat konfigurationsfil, vilken inte visas i det här exemplet. Referera till [recepten](https://github.com/Azure/BatchAI/tree/master/recipes) när du ska implementera en konfigurationsfil. 

## <a name="authenticate-with-batch-ai"></a>Autentisera med Batch AI

Om du vill få åtkomst till Azure Batch AI måste du autentiseras med Azure Active Directory. Nedan visas koden som du använder för att autentisera dig gentemot tjänsten (skapa ett `BatchAIManagementClient`-objekt) med hjälp av autentiseringsuppgifterna för tjänstens huvudnamn och ditt prenumerations-ID:

```Python
from azure.common.credentials import ServicePrincipalCredentials
import azure.mgmt.batchai as batchai
import azure.mgmt.batchai.models as models

creds = ServicePrincipalCredentials(
        client_id=client_id, secret=secret, token_uri=token_uri)

batchai_client = batchai.BatchAIManagementClient(credentials=creds,
                                         subscription_id=subscription_id
)
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Batch AI-kluster och jobb är Azure-resurser och måste placeras i en Azure-resursgrupp. I följande kodavsnitt skapas en resursgrupp:

```Python
from azure.mgmt.resource import ResourceManagementClient

resource_group_name = 'myresourcegroup'

resource_management_client = ResourceManagementClient(
        credentials=creds, subscription_id=subscription_id)

resource = resource_management_client.resource_groups.create_or_update(
        resource_group_name, {'location': 'eastus'})
```


## <a name="prepare-azure-file-share"></a>Förbered en Azure-filresurs
I illustrationssyfte använder den här snabbstarten en Azure-filresurs som värd för utbildningsinformationen och skripten för utbildningsjobbet. 

1. Skapa en filresurs med namnet *batchaiquickstart*.

  ```Python
  from azure.storage.file import FileService 
 
  azure_file_share_name = 'batchaiquickstart' 
 
  service = FileService(storage_account_name, storage_account_key) 
 
  service.create_share(azure_file_share_name, fail_on_exist=False)
  ``` 
 
2. Skapa en katalog i resursen med namnet *mnistcntksample* 

  ```Python
  mnist_dataset_directory = 'mnistcntksample' 
 
  service.create_directory(azure_file_share_name, mnist_dataset_directory, fail_on_exist=False) 
  ```
3. Hämta [exempelpaketet](https://batchaisamples.blob.core.windows.net/samples/BatchAIQuickStart.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=b&sig=hrAZfbZC%2BQ%2FKccFQZ7OC4b%2FXSzCF5Myi4Cj%2BW3sVZDo%3D) och packa upp det. Överför innehållet till den katalog där du ska köra Python-skriptet.

  ```Python
  for f in ['Train-28x28_cntk_text.txt', 'Test-28x28_cntk_text.txt', 'ConvNet_MNIST.py']:     
     service.create_file_from_path(
             azure_file_share_name, mnist_dataset_directory, f, f) 
  ```

## <a name="create-gpu-cluster"></a>Skapa GPU-kluster

Skapa ett Batch AI-kluster. I det här exemplet består klustret av en enskild STANDARD_NC6 VM-nod. Den här virtuella datorstorleken har en NVIDIA K80 GPU. Montera filresursen i en mapp med namnet *azurefileshare*. Den fullständiga sökvägen till den här mappen på GPU-beräkningsnoden är $AZ_BATCHAI_MOUNT_ROOT/azurefileshare.

```Python
cluster_name = 'mycluster'

relative_mount_point = 'azurefileshare' 
 
parameters = models.ClusterCreateParameters(
    # Location where the cluster will physically be deployed
    location='eastus', 
 
    # VM size. Use NC or NV series for GPU
    vm_size='STANDARD_NC6', 
 
    # Configure the ssh users
    user_account_settings=models.UserAccountSettings(
         admin_user_name=admin_user_name,
         admin_user_password=admin_user_password), 
 
    # Number of VMs in the cluster
    scale_settings=models.ScaleSettings(
         manual=models.ManualScaleSettings(target_node_count=1)
     ), 
 
    # Configure each node in the cluster
    node_setup=models.NodeSetup( 
 
        # Mount shared volumes to the host
         mount_volumes=models.MountVolumes(
             azure_file_shares=[
                 models.AzureFileShareReference(
                     account_name=storage_account_name,
                     credentials=models.AzureStorageCredentialsInfo(
         account_key=storage_account_key),
         azure_file_url='https://{0}.file.core.windows.net/{1}'.format(
               storage_account_name, azure_file_share_name),
                  relative_mount_path = relative_mount_point)],
         ), 
    ), 
) 
batchai_client.clusters.create(resource_group_name, cluster_name, parameters).result() 
```

## <a name="get-cluster-status"></a>Hämta klusterstatus

Övervaka klusterstatusen med följande kommando: 

```Python
cluster = batchai_client.clusters.get(resource_group_name, cluster_name)
print('Cluster state: {0} Target: {1}; Allocated: {2}; Idle: {3}; '
      'Unusable: {4}; Running: {5}; Preparing: {6}; leaving: {7}'.format(
    cluster.allocation_state,
    cluster.scale_settings.manual.target_node_count,
    cluster.current_node_count,
    cluster.node_state_counts.idle_node_count,
    cluster.node_state_counts.unusable_node_count,
    cluster.node_state_counts.running_node_count,
    cluster.node_state_counts.preparing_node_count,
    cluster.node_state_counts.leaving_node_count)) 
```

Den föregående koden skriver ut grundläggande klusterallokeringsinformation, t.ex. följande:

```Shell
Cluster state: AllocationState.steady Target: 1; Allocated: 1; Idle: 0; Unusable: 0; Running: 0; Preparing: 1; Leaving 0
 
```  

Klustret är klart när noderna har allokerats och är färdigförberedda (se `nodeStateCounts`-attributet). Om något går fel så innehåller `errors`-attributet felbeskrivningen.

## <a name="create-training-job"></a>Skapa utbildningsjobb

När klustret är klart så konfigurerar du utbildningsjobbet och skickar in det. 

```Python
job_name = 'myjob' 
 
parameters = models.job_create_parameters.JobCreateParameters( 
 
     # Location where the job will run
     # Ideally this should be co-located with the cluster.
     location='eastus', 
 
     # The cluster this job will run on
     cluster=models.ResourceId(cluster.id), 
 
     # The number of VMs in the cluster to use
     node_count=1, 
 
     # Override the path where the std out and std err files will be written to.
     # In this case we will write these out to an Azure Files share
     std_out_err_path_prefix='$AZ_BATCHAI_MOUNT_ROOT/{0}'.format(relative_mount_point), 
 
     input_directories=[models.InputDirectory(
         id='SAMPLE',
         path='$AZ_BATCHAI_MOUNT_ROOT/{0}/{1}'.format(relative_mount_point, mnist_dataset_directory))], 
 
     # Specify directories where files will get written to 
     output_directories=[models.OutputDirectory(
        id='MODEL',
        path_prefix='$AZ_BATCHAI_MOUNT_ROOT/{0}'.format(relative_mount_point),
        path_suffix="Models")], 
 
     # Container configuration
     container_settings=models.ContainerSettings(
        models.ImageSourceRegistry(image='microsoft/cntk:2.1-gpu-python3.5-cuda8.0-cudnn6.0')), 
 
     # Toolkit specific settings
     cntk_settings = models.CNTKsettings(
        python_script_file_path='$AZ_BATCHAI_INPUT_SAMPLE/ConvNet_MNIST.py',
        command_line_args='$AZ_BATCHAI_INPUT_SAMPLE $AZ_BATCHAI_OUTPUT_MODEL')
 ) 
 
# Create the job 
batchai_client.jobs.create(resource_group_name, job_name, parameters).result() 
```

## <a name="monitor-job"></a>Övervaka jobb
Du kan inspektera jobbets status med följande kommando: 
 
```Python
job = batchai_client.jobs.get(resource_group_name, job_name) 
 
print('Job state: {0} '.format(job.execution_state.name))
```

Utdata liknar följande: `Job state: running`.

`executionState` innehåller jobbets aktuella körningsstatus:
* `queued`: jobbet väntar på att klusternoderna ska bli tillgängliga
* `running`: jobbet körs
* `succeeded` (eller `failed`): jobbet har slutförts och `executionInfo` innehåller information om resultatet
 
## <a name="list-stdout-and-stderr-output"></a>Lista stdout- och stderr-utdata
Använd följande kommando när du ska lista länkar till stdout- och stderr-loggfiler:

```Python
files = batchai_client.jobs.list_output_files(resource_group_name, job_name, models.JobsListOutputFilesOptions("stdouterr")) 
 
for file in list(files):
     print('file: {0}, download url: {1}'.format(file.name, file.download_url)) 
```
## <a name="delete-resources"></a>Ta bort resurser

Ta bort jobbet med följande kommando:
```Python
batchai_client.jobs.delete(resource_group_name, job_name) 
```

Ta bort klustret med följande kommando:
```Python
batchai_client.clusters.delete(resource_group_name, cluster_name)
```
## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du kör ett CNTK-utbildningsjobb i ett Batch AI-kluster med hjälp av Azure Python-SDK:n. Mer information om hur du använder Batch AI med olika verktyg finns i [utbildningsrecept](https://github.com/Azure/BatchAI).
