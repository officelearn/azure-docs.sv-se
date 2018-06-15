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
ms.openlocfilehash: da5c1181f9c4d311bdeabe837435ae4e0eb3dc1a
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
ms.locfileid: "31513256"
---
# <a name="run-a-cntk-training-job-using-the-azure-python-sdk"></a>Kör ett CNTK-utbildningsjobb med hjälp av Azure Python-SDK:n

Den här snabbstarten visar dig hur du kan använd Azure Python-SDK:n för att köra ett utbildningsjobb med Microsoft Cognitive Toolkit (CNTK) med hjälp av Batch AI-tjänsten.

I det här exemplet använder du MNIST-databasen med handritade bilder för att träna ett CNN (Convolutional Neural Network) i ett GPU-kluster med en nod.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Azure-prenumeration – Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

* Azure Python SDK. Mer information finns i [Installationsanvisningar](/python/azure/python-sdk-azure-install)

* Azure-lagringskonto. Mer information finns i [Skapa ett Azure-lagringskonto](../storage/common/storage-create-storage-account.md)

* Azure Active Directory – autentiseringsuppgifter för tjänstens huvudnamn. Mer information finns i [Hur du skapar ett tjänstens huvudnamn med CLI](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md)

* Registrera Batch AI-resursprovidrarna en gång för din prenumeration med Azure Cloud Shell eller Azure CLI. En providerregistrering kan ta upp till 15 minuter.

```azurecli
az provider register -n Microsoft.BatchAI
```

## <a name="configure-credentials"></a>Konfigurera autentiseringsuppgifter
Lägg till följande kod i skriptet och ersätt `FILL-IN-HERE` med lämpliga värden:

```Python
# credentials used for authentication
aad_client_id = 'FILL-IN-HERE'
aad_secret = 'FILL-IN-HERE'
aad_tenant = 'FILL-IN-HERE'
subscription_id = 'FILL-IN-HERE'

# credentials used for storage
storage_account_name = 'FILL-IN-HERE'
storage_account_key = 'FILL-IN-HERE'

# specify the credentials used to remote login your GPU node
admin_user_name = 'FILL-IN-HERE'
admin_user_password = 'FILL-IN-HERE'
```

Observera att du aldrig bör infoga autentiseringsuppgifter i källkoden. Här gör vi det för att göra snabbstarten enklare.
Överväg att använda miljövariabler eller en separat konfigurationsfil i stället.

## <a name="create-batch-ai-client"></a>Skapa en Batch AI-klient

Följande kod skapar ett tjänstobjekt med huvudautentiseringsuppgifter och en Batch AI-klient:

```Python
from azure.common.credentials import ServicePrincipalCredentials
import azure.mgmt.batchai as batchai
import azure.mgmt.batchai.models as models

creds = ServicePrincipalCredentials(
        client_id=aad_client_id, secret=aad_secret, tenant=aad_tenant)

batchai_client = batchai.BatchAIManagementClient(
    credentials=creds, subscription_id=subscription_id)
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
I illustrationssyfte används i den här snabbstarten en Azure-filresurs som värd för utbildningsinformationen och skripten för utbildningsjobbet.

1. Skapa en filresurs med namnet `batchaiquickstart`.

```Python
from azure.storage.file import FileService
azure_file_share_name = 'batchaiquickstart'
service = FileService(storage_account_name, storage_account_key)
service.create_share(azure_file_share_name, fail_on_exist=False)
```

2. Skapa en katalog i resursen `mnistcntksample`

```Python
mnist_dataset_directory = 'mnistcntksample'
service.create_directory(azure_file_share_name, mnist_dataset_directory,
                         fail_on_exist=False)
```
3. Hämta [exempelpaketet](https://batchaisamples.blob.core.windows.net/samples/BatchAIQuickStart.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=b&sig=hrAZfbZC%2BQ%2FKccFQZ7OC4b%2FXSzCF5Myi4Cj%2BW3sVZDo%3D) och packa upp det till den aktuella katalogen. Följande kod laddar upp de filer som krävs i Azure-filresursen:

```Python
for f in ['Train-28x28_cntk_text.txt', 'Test-28x28_cntk_text.txt',
          'ConvNet_MNIST.py']:
     service.create_file_from_path(
             azure_file_share_name, mnist_dataset_directory, f, f)
```

## <a name="create-gpu-cluster"></a>Skapa GPU-kluster

Skapa ett Batch AI-kluster. I det här exemplet består klustret av en enskild STANDARD_NC6 VM-nod. Den här virtuella datorstorleken har en NVIDIA K80 GPU. Montera filresursen i en mapp med namnet `azurefileshare`. Den fullständiga sökvägen till mappen på GPU-beräkningsnoden är `$AZ_BATCHAI_MOUNT_ROOT/azurefileshare`.

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
                    azure_file_url='https://{0}/{1}'.format(
                        service.primary_endpoint, azure_file_share_name),
                    relative_mount_path=relative_mount_point)],
        ),
    ),
)
batchai_client.clusters.create(resource_group_name, cluster_name,
                               parameters).result()
```

## <a name="get-cluster-status"></a>Hämta klusterstatus

Övervaka klusterstatusen med följande kommando:

```Python
cluster = batchai_client.clusters.get(resource_group_name, cluster_name)
print('Cluster state: {0} Target: {1}; Allocated: {2}; Idle: {3}; '
      'Unusable: {4}; Running: {5}; Preparing: {6}; Leaving: {7}'.format(
    cluster.allocation_state,
    cluster.scale_settings.manual.target_node_count,
    cluster.current_node_count,
    cluster.node_state_counts.idle_node_count,
    cluster.node_state_counts.unusable_node_count,
    cluster.node_state_counts.running_node_count,
    cluster.node_state_counts.preparing_node_count,
    cluster.node_state_counts.leaving_node_count))
```

Den föregående koden skriver ut grundläggande klusterallokeringsinformation, som i följande exempel:

```
Cluster state: AllocationState.steady Target: 1; Allocated: 1; Idle: 0; Unusable: 0; Running: 0; Preparing: 1; Leaving: 0
```

Klustret är klart när noderna har allokerats och är färdigförberedda (se `nodeStateCounts`-attributet). Om något går fel så innehåller `errors`-attributet felbeskrivningen.

## <a name="create-training-job"></a>Skapa utbildningsjobb

När klustret har skapats konfigurerar du utbildningsjobbet och skickar det:

```Python
job_name = 'myjob'

parameters = models.job_create_parameters.JobCreateParameters(
    # The job and cluster must be created in the same location
    location=cluster.location,
    # The cluster this job will run on
    cluster=models.ResourceId(id=cluster.id),
    # The number of VMs in the cluster to use
    node_count=1,
    # Write job's standard output and execution log to Azure File Share
    std_out_err_path_prefix='$AZ_BATCHAI_MOUNT_ROOT/{0}'.format(
        relative_mount_point),
    # Configure location of the training script and MNIST dataset
    input_directories=[models.InputDirectory(
        id='SAMPLE',
        path='$AZ_BATCHAI_MOUNT_ROOT/{0}/{1}'.format(
            relative_mount_point, mnist_dataset_directory))],
    # Specify location where generated model will be stored
    output_directories=[models.OutputDirectory(
        id='MODEL',
        path_prefix='$AZ_BATCHAI_MOUNT_ROOT/{0}'.format(relative_mount_point),
        path_suffix="Models")],
    # Container configuration
    container_settings=models.ContainerSettings(
        image_source_registry=models.ImageSourceRegistry(
            image='microsoft/cntk:2.1-gpu-python3.5-cuda8.0-cudnn6.0')),
    # Toolkit specific settings
    cntk_settings=models.CNTKsettings(
        python_script_file_path='$AZ_BATCHAI_INPUT_SAMPLE/ConvNet_MNIST.py',
        command_line_args='$AZ_BATCHAI_INPUT_SAMPLE $AZ_BATCHAI_OUTPUT_MODEL')
)

# Create the job
batchai_client.jobs.create(resource_group_name, job_name, parameters).result()
```

## <a name="monitor-job"></a>Övervaka jobb
Du kan granska jobbets status med följande kod:

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
Använd följande kod för att lista de stdout-, stderr- och loggfiler som genererats:

```Python
files = batchai_client.jobs.list_output_files(
    resource_group_name, job_name,
    models.JobsListOutputFilesOptions(outputdirectoryid="stdouterr"))

for file in (f for f in files if f.download_url):
    print('file: {0}, download url: {1}'.format(file.name, file.download_url))
```

## <a name="list-generated-model-files"></a>Lista genererade modellfiler
Använd följande kod för att lista de modellfiler som genererats:
```Python
files = batchai_client.jobs.list_output_files(
    resource_group_name, job_name,
    models.JobsListOutputFilesOptions(outputdirectoryid="MODEL"))

for file in (f for f in files if f.download_url):
    print('file: {0}, download url: {1}'.format(file.name, file.download_url))
```

## <a name="delete-resources"></a>Ta bort resurser

Ta bort jobbet med följande kod:
```Python
batchai_client.jobs.delete(resource_group_name, job_name)
```

Använd följande kod för att ta bort klustret:
```Python
batchai_client.clusters.delete(resource_group_name, cluster_name)
```

Använd följande kod för att ta bort alla allokerade resurser:
```Python
resource_management_client.resource_groups.delete('myresourcegroup')
```
## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du kör ett CNTK-utbildningsjobb i ett Batch AI-kluster med hjälp av Azure Python-SDK:n. Mer information om hur du använder Batch AI med olika verktyg finns i [utbildningsrecept](https://github.com/Azure/BatchAI).
