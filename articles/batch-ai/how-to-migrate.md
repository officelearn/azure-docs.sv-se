---
title: Migrera från Batch AI till Azure Machine Learning-tjänsten
description: Lär dig hur du migrerar till Azure Machine Learning-tjänsten för AMLcompute och hur din kod mappar till kod i Azure Machine Learning-tjänsten.
ms.service: batch-ai
services: batch-ai
ms.topic: overview
ms.author: jmartens
author: j-martens
ms.date: 2/28/2019
ms.openlocfilehash: e495ed06c640601c0500d14b42070a264fd687a9
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57862122"
---
# <a name="migrating-from-batch-ai-to-azure-machine-learning-service"></a>Migrera från Batch AI till Azure Machine Learning-tjänsten

**Azure Batch AI-tjänsten tas ur bruk i mars.** De skalenliga tränings- och bedömningsfunktionerna i Batch AI är nu tillgängliga i [Azure Machine Learning Services](../machine-learning/service/overview-what-is-azure-ml.md), som blev allmänt tillgänglig den 4 december 2018.

Tillsammans med många andra funktioner för maskininlärning innehåller Azure Machine Learning-tjänsten ett molnbaserat hanterat beräkningsmål för att träna, distribuera och bedöms maskininlärningsmodeller. Den här beräkningsmålet kallas för [Azure Machine Learning Compute](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute). Börja migrera och använder idag. Du kan interagera med Azure Machine Learning-tjänsten via dess [Python SDK: er](../machine-learning/service/quickstart-create-workspace-with-python.md), kommandoradsgränssnitt och [Azure Portal](../machine-learning/service/quickstart-get-started.md).

Uppgradera från förhandsversion Batch AI till GA'ed Azure Machine Learning-tjänsten ger dig en bättre upplevelse igenom koncept som är enklare att använda, till exempel Estimators och datalager. Du har även garanterad kundsupport och SLA:er på GA-nivå för Azure-tjänsten.

Azure Machine Learning service medför också nya funktioner som automatisk machine learning, finjustering av hyperparametrar och ML pipelines, vilket är användbart i de flesta storskaliga AI-arbetsbelastningar. Möjligheten att distribuera en tränad modell utan att växla till en separat tjänst hjälper till att komplettera data science-slinga från förberedelse av data (med Data Prep SDK) hela vägen till driftsättning och övervakning av modellen.

## <a name="start-migrating"></a>Börja migrera
Att undvika avbrott i dina program och dra nytta av de senaste funktionerna ska du göra följande innan 31 mars 2019:

1. Skapa en Azure Machine Learning-tjänstarbetsyta och kom igång:
    + [Python-baserad snabbstart](../machine-learning/service/quickstart-create-workspace-with-python.md)
    + [Azure Portal-baserad snabbstart](../machine-learning/service/quickstart-get-started.md)

1. Installera den [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) och [Data Förbered SDK](https://docs.microsoft.com/python/api/overview/azure/dataprep/intro?view=azure-dataprep-py#install). 

1. Konfigurera en [Azure Machine Learning Compute](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute) för att träna modellen.

1. Uppdatera dina skript så att de använder Azure Machine Learning Compute. I följande avsnitt visas hur vanliga kod som du använder för Batch AI-mappar till kod för Azure Machine Learning. 


## <a name="create-workspaces"></a>Skapa arbetsytor
Begreppet initierar en arbetsyta med hjälp av en configuration.json i Azure Batch AI mappar på samma sätt att använda en konfigurationsfil i Azure Machine Learning-tjänsten.

I **Batch AI** gjorde du på det här sättet:

```python
sys.path.append('../../..')
import utilities as utils

cfg = utils.config.Configuration('../../configuration.json')
client = utils.config.create_batchai_client(cfg)

utils.config.create_resource_group(cfg)
_ = client.workspaces.create(cfg.resource_group, cfg.workspace, cfg.location).result()
```

**Azure Machine Learning Service** försöker du att:

```python
from azureml.core.workspace import Workspace

ws = Workspace.from_config()
print('Workspace name: ' + ws.name, 
      'Azure region: ' + ws.location, 
      'Subscription id: ' + ws.subscription_id, 
      'Resource group: ' + ws.resource_group, sep = '\n')
```

Du kan dessutom också skapa en arbetsyta direkt genom att ange konfigurationsparametrar som

```python
from azureml.core import Workspace
# Create the workspace using the specified parameters
ws = Workspace.create(name = workspace_name,
                      subscription_id = subscription_id,
                      resource_group = resource_group, 
                      location = workspace_region,
                      create_resource_group = True,
                      exist_ok = True)
ws.get_details()

# write the details of the workspace to a configuration file to the notebook library
ws.write_config()
```

Mer information om klassen Azure Machine Learning-arbetsyta i den [SDK referensdokumentation](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py).


## <a name="create-compute-clusters"></a>Skapa beräkningskluster
Azure Machine Learning har stöd för flera beräkningsmål, varav vissa hanteras av tjänsten och andra kan kopplas till din arbetsyta (t.ex. Ett HDInsight-kluster eller en fjärransluten virtuell dator. Läs mer om olika [beräkningsmål](../machine-learning/service/how-to-set-up-training-targets.md). Konceptet för att skapa ett Azure Batch AI compute kluster maps till att skapa ett AmlCompute-kluster i Azure Machine Learning-tjänsten. Amlcompute tar i en beräkningskonfigurationen som liknar hur du skickar parametrar i Azure Batch AI. En sak att tänka på är att automatisk skalning är aktiverat som standard i AmlCompute klustret medan den är avstängd som standard i Azure Batch AI.

I **Batch AI** gjorde du på det här sättet:

```python
nodes_count = 2
cluster_name = 'nc6'

parameters = models.ClusterCreateParameters(
    vm_size='STANDARD_NC6',
    scale_settings=models.ScaleSettings(
        manual=models.ManualScaleSettings(target_node_count=nodes_count)
    ),
    user_account_settings=models.UserAccountSettings(
        admin_user_name=cfg.admin,
        admin_user_password=cfg.admin_password or None,
        admin_user_ssh_public_key=cfg.admin_ssh_key or None,
    )
)
_ = client.clusters.create(cfg.resource_group, cfg.workspace, cluster_name, parameters).result()
```

I **Azure Machine Learning Service** försöker du att:

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your CPU cluster
gpu_cluster_name = "nc6"

# Verify that cluster does not exist already
try:
    gpu_cluster = ComputeTarget(workspace=ws, name=gpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6',
                                                           vm_priority='lowpriority',
                                                           min_nodes=1,
                                                           max_nodes=2,
                                                           idle_seconds_before_scaledown='300',
                                                           vnet_resourcegroup_name='<my-resource-group>',
                                                           vnet_name='<my-vnet-name>',
                                                           subnet_name='<my-subnet-name>')
    gpu_cluster = ComputeTarget.create(ws, gpu_cluster_name, compute_config)

gpu_cluster.wait_for_completion(show_output=True)
```

Mer information om klassen för AMLCompute finns i [SDK-referensdokumentationen](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py). Observera att i konfigurationen ovan är endast vm_size och max_nodes obligatoriska. Resten av egenskaperna (t.ex. virtuella nätverk) används endast vid avancerad klusterkonfiguration.

## <a name="monitor-status-of-your-cluster"></a>Övervakarstatus för ditt kluster
Det här är mer direkt i Azure Machine Learning-tjänsten som visas nedan.

I **Batch AI** gjorde du på det här sättet:

```python
cluster = client.clusters.get(cfg.resource_group, cfg.workspace, cluster_name)
utils.cluster.print_cluster_status(cluster)
```

I **Azure Machine Learning Service** försöker du att:

```python
gpu_cluster.get_status().serialize()
```

## <a name="get-reference-to-a-storage-account"></a>Hämta en referens till ett lagringskonto
Konceptet med en datalagring som blob, hämtar förenklad i Azure Machine Learning-tjänsten med DataStore-objektet. Som standard skapar ett lagringskonto i din arbetsyta för Azure Machine Learning-tjänsten, men du kan koppla din egen storage också som en del av arbetsytan skapas. 

I **Batch AI** gjorde du på det här sättet:

```python
azure_blob_container_name = 'batchaisample'
blob_service = BlockBlobService(cfg.storage_account_name, cfg.storage_account_key)
blob_service.create_container(azure_blob_container_name, fail_on_exist=False)
```

I **Azure Machine Learning Service** försöker du att:

```python
ds = ws.get_default_datastore()
print(ds.datastore_type, ds.account_name, ds.container_name)
```

Läs mer om hur du registrerar ytterligare lagringskonton eller hämtar en referens till en annan registrerad datalager i den [dokumentation för Azure Machine Learning-tjänsten](../machine-learning/service/how-to-access-data.md#access).


## <a name="download-and-upload-data"></a>Ladda ned och ladda upp data 
Du kan använda någon av tjänsterna till att ladda upp data till lagringskontot på ett enkelt sätt med datalagerreferensen ovan. För Azure Batch AI distribuera vi också skriptet utbildning som en del av filresursen, även om hur du kan ange det som en del av din konfiguration för jobbet när det gäller Azure Machine Learning-tjänsten.

I **Batch AI** gjorde du på det här sättet:

```python
mnist_dataset_directory = 'mnist_dataset'
utils.dataset.download_and_upload_mnist_dataset_to_blob(
    blob_service, azure_blob_container_name, mnist_dataset_directory)

script_directory = 'tensorflow_samples'
script_to_deploy = 'mnist_replica.py'

blob_service.create_blob_from_path(azure_blob_container_name,
                                   script_directory + '/' + script_to_deploy, 
                                   script_to_deploy)
```


I **Azure Machine Learning Service** försöker du att:

```python
import os
import urllib
os.makedirs('./data', exist_ok=True)
download_url = 'https://s3.amazonaws.com/img-datasets/mnist.npz'
urllib.request.urlretrieve(download_url, filename='data/mnist.npz')

ds.upload(src_dir='data', target_path='mnist_dataset', overwrite=True, show_progress=True)

path_on_datastore = ' mnist_dataset/mnist.npz' ds_data = ds.path(path_on_datastore) print(ds_data)
```

## <a name="create-experiments"></a>Skapa experiment
Som nämnts ovan har ett koncept för ett experiment som liknar Azure Batch AI i Azure Machine Learning-tjänsten. Varje experimentet får sedan enskilda körningar, liknar hur vi ha jobb i Azure Batch AI. Azure Machine Learning-tjänsten kan du ha hierarki under varje överordnad kör för enskilda underordnade körningar.

I **Batch AI** gjorde du på det här sättet:

```python
experiment_name = 'tensorflow_experiment'
experiment = client.experiments.create(cfg.resource_group, cfg.workspace, experiment_name).result()
```

I **Azure Machine Learning Service** försöker du att:

```python
from azureml.core import Experiment

experiment_name = 'tensorflow_experiment'
experiment = Experiment(ws, name=experiment_name)
```


## <a name="submit-jobs"></a>Skicka jobb
När du skapar ett experiment finns det några olika sätt att skicka en körning på. I det här exemplet vi försöker skapa en modell för djupinlärning med TensorFlow och använder en Azure Machine Learning-tjänsten kostnadsuppskattning du gör. En [beräkning](../machine-learning/service/how-to-train-ml-models.md) är helt enkelt en omslutningsfunktion i den underliggande körningskonfigurationen, vilket gör det enklare att skicka körningar. Detta stöds för närvarande endast för Pytorch och TensorFlow. Via begreppet med datalager kan du även se hur enkelt det blir att ange monteringssökvägarna 

I **Batch AI** gjorde du på det här sättet:

```python
azure_file_share = 'afs'
azure_blob = 'bfs'
args_fmt = '--job_name={0} --num_gpus=1 --train_steps 10000 --checkpoint_dir=$AZ_BATCHAI_OUTPUT_MODEL --log_dir=$AZ_BATCHAI_OUTPUT_TENSORBOARD --data_dir=$AZ_BATCHAI_INPUT_DATASET --ps_hosts=$AZ_BATCHAI_PS_HOSTS --worker_hosts=$AZ_BATCHAI_WORKER_HOSTS --task_index=$AZ_BATCHAI_TASK_INDEX'

parameters = models.JobCreateParameters(
     cluster=models.ResourceId(id=cluster.id),
     node_count=2,
     input_directories=[
        models.InputDirectory(
            id='SCRIPT',
            path='$AZ_BATCHAI_JOB_MOUNT_ROOT/{0}/{1}'.format(azure_blob, script_directory)),
        models.InputDirectory(
            id='DATASET',
            path='$AZ_BATCHAI_JOB_MOUNT_ROOT/{0}/{1}'.format(azure_blob, mnist_dataset_directory))],
     std_out_err_path_prefix='$AZ_BATCHAI_JOB_MOUNT_ROOT/{0}'.format(azure_file_share),
     output_directories=[
        models.OutputDirectory(
            id='MODEL',
            path_prefix='$AZ_BATCHAI_JOB_MOUNT_ROOT/{0}'.format(azure_file_share),
            path_suffix='Models'),
        models.OutputDirectory(
            id='TENSORBOARD',
            path_prefix='$AZ_BATCHAI_JOB_MOUNT_ROOT/{0}'.format(azure_file_share),
            path_suffix='Logs')
     ],
     mount_volumes=models.MountVolumes(
            azure_file_shares=[
                models.AzureFileShareReference(
                    account_name=cfg.storage_account_name,
                    credentials=models.AzureStorageCredentialsInfo(
                        account_key=cfg.storage_account_key),
                    azure_file_url='https://{0}.file.core.windows.net/{1}'.format(
                        cfg.storage_account_name, azure_file_share_name),
                    relative_mount_path=azure_file_share)
            ],
            azure_blob_file_systems=[
                models.AzureBlobFileSystemReference(
                    account_name=cfg.storage_account_name,
                    credentials=models.AzureStorageCredentialsInfo(
                        account_key=cfg.storage_account_key),
                    container_name=azure_blob_container_name,
                    relative_mount_path=azure_blob)
            ]
        ),
     container_settings=models.ContainerSettings(
         image_source_registry=models.ImageSourceRegistry(image='tensorflow/tensorflow:1.8.0-gpu')),
     tensor_flow_settings=models.TensorFlowSettings(
         parameter_server_count=1,
         worker_count=nodes_count,
         python_script_file_path='$AZ_BATCHAI_INPUT_SCRIPT/'+ script_to_deploy,
         master_command_line_args=args_fmt.format('worker'),
         worker_command_line_args=args_fmt.format('worker'),
         parameter_server_command_line_args=args_fmt.format('ps'),
     )
)
```

Att skicka jobbet är själva i Azure Batch AI via funktionen Skapa.

```python
job_name = datetime.utcnow().strftime('tf_%m_%d_%Y_%H%M%S')
job = client.jobs.create(cfg.resource_group, cfg.workspace, experiment_name, job_name, parameters).result()
print('Created Job {0} in Experiment {1}'.format(job.name, experiment.name))
```

Den fullständiga informationen för det här kodfragmentet för träning (inklusive den mnist_replica.py-fil som vi hade laddade upp till filresursen ovan) kan hittas i den [Azure Batch AI-anteckningsbok github-exempellager](https://github.com/Azure/BatchAI/tree/2238607d5a028a0c5e037168aefca7d7bb165d5c/recipes/TensorFlow/TensorFlow-GPU-Distributed).

I **Azure Machine Learning Service** försöker du att:

```python
from azureml.train.dnn import TensorFlow

script_params={
    '--num_gpus': 1,
    '--train_steps': 500,
    '--input_data': ds_data.as_mount()

}

estimator = TensorFlow(source_directory=project_folder,
                       compute_target=gpu_cluster,
                       script_params=script_params,
                       entry_script='tf_mnist_replica.py',
                       node_count=2,
                       worker_count=2,
                       parameter_server_count=1,   
                       distributed_backend='ps',
                       use_gpu=True)
```

Den fullständiga informationen för det här kodfragmentet för träning (inklusive filen tf_mnist_replica.py) finns i den [Azure Machine Learning-tjänsten notebook github-exempellager](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/distributed-tensorflow-with-parameter-server). Datalagringen kan antingen monteras på enskilda noder eller så kan träningsdata hämtas från själva noden. Mer information om refererar till datalager i din kostnadsuppskattning är i den [dokumentation för Azure Machine Learning-tjänsten](../machine-learning/service/how-to-access-data.md#access). 

Skicka en körning i Azure Machine Learning är-tjänsten via funktionen Skicka.

```python
run = experiment.submit(estimator)
print(run)
```

Det finns ett annat sätt att ange parametrar för din körning med en körningskonfiguration – vilket är särskilt användbart när en anpassad träningsmiljö ska definieras. Du hittar mer information i det här [exemplet på en AmlCompute-anteckningsbok](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-amlcompute/train-on-amlcompute.ipynb). 

## <a name="monitor-runs"></a>Övervakaren körs
När du skickar in en körning kan du antingen vänta tills det för att utföra eller övervaka i Azure Machine Learning-tjänsten med snygga Jupyter widgetar som du kan anropa direkt från din kod. Du kan också hämta kontexten för en tidigare körning genom att göra en loop genom olika experiment i en arbetsyta och genom enskilda körningar inom varje experiment.

I **Batch AI** gjorde du på det här sättet:

```python
utils.job.wait_for_job_completion(client, cfg.resource_group, cfg.workspace, 
                                  experiment_name, job_name, cluster_name, 'stdouterr', 'stdout-wk-0.txt')

files = client.jobs.list_output_files(cfg.resource_group, cfg.workspace, experiment_name, job_name,
                                      models.JobsListOutputFilesOptions(outputdirectoryid='stdouterr')) 
for f in list(files):
    print(f.name, f.download_url or 'directory')
```


I **Azure Machine Learning Service** försöker du att:

```python
run.wait_for_completion(show_output=True)

from azureml.widgets import RunDetails
RunDetails(run).show()
```

Här är en ögonblicksbild av hur widgeten skulle läsas in i anteckningsboken för att visa dina loggar i realtid: ![Diagram över övervakningswidget](./media/overview-what-happened-batch-ai/monitor.png)



## <a name="edit-clusters"></a>Redigera kluster
Det är enkelt att ta bort ett kluster. Dessutom kan Azure Machine Learning-tjänsten du också att uppdatera ett kluster från i anteckningsboken om du vill skala till ett högre antal noder eller öka inaktiv väntetiden innan du skalar ned klustret. Du kan inte ändra VM-storleken för själva klustret, eftersom det kräver en ny distribution i serverdelen.

I **Batch AI** gjorde du på det här sättet:
```python
_ = client.clusters.delete(cfg.resource_group, cfg.workspace, cluster_name)
```

I **Azure Machine Learning Service** försöker du att:

```python
gpu_cluster.delete()

gpu_cluster.update(min_nodes=2, max_nodes=4, idle_seconds_before_scaledown=600)
```

## <a name="get-support"></a>Få support

Batch AI är som ska ta ur bruk 31 mars och redan blockerar nya prenumerationer från att registrera mot tjänsten om det inte är godkänd av utlöser ett undantag med support.  Kontakta oss på [Förhandsversionen av Azure Batch AI-träningen](mailto:AzureBatchAITrainingPreview@service.microsoft.com) om du har frågor eller feedback när du migrerar till Azure Machine Learning Service.

Microsoft Azure Machine Learning Service är nu allmänt tillgänglig. Det innebär att den innehåller ett dedikerat serviceavtal och olika supportplaner att välja bland.

Priser för att använda Azure-infrastrukturen via Azure Batch AI-tjänsten eller via Azure Machine Learning-tjänsten ska inte varierar som debiterar vi bara priset för den underliggande beräkningskraften i båda fallen. Mer information finns på sidan med [priskalkylatorn](https://azure.microsoft.com/pricing/details/machine-learning-service/).

Visa regional tillgänglighet mellan de två tjänsterna i [Azure-portalen](https://azure.microsoft.com/global-infrastructure/services/?products=batch-ai,machine-learning-service&regions=all).


## <a name="next-steps"></a>Nästa steg

+ Läs [Översikt över Azure Machine Learning-tjänsten](../machine-learning/service/overview-what-is-azure-ml.md).

+ [Konfigurera ett beräkningsmål för modellträning](../machine-learning/service/how-to-set-up-training-targets.md) med Azure Machine Learning-tjänsten.

+ Granska [Azure-översikten](https://azure.microsoft.com/updates/) för att lära dig av andra Azure-tjänstuppdateringar.
