---
title: Vad händer med Azure Batch AI? | Microsoft Docs
description: Läs om vad som händer med Azure Batch AI och beräkningsalternativen i Azure Machine Learning-tjänsten.
services: batch-ai
author: garyericson
ms.service: batch-ai
ms.topic: overview
ms.date: 2/14/2019
ms.author: garye
ms.openlocfilehash: fb1114e94c227ce6787532c6059186399d0f57f0
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961371"
---
# <a name="whats-happening-to-azure-batch-ai"></a>Vad händer med Azure Batch AI?

**Azure Batch AI-tjänsten tas ur bruk i mars.** De skalenliga tränings- och bedömningsfunktionerna i Batch AI är nu tillgängliga i [Azure Machine Learning Services](../machine-learning/service/overview-what-is-azure-ml.md), som blev allmänt tillgänglig den 4 december 2018.

Tillsammans med många andra funktioner för maskininlärning innehåller Azure Machine Learning-tjänsten ett molnbaserat hanterat beräkningsmål för att träna, distribuera och bedöms maskininlärningsmodeller. Den här beräkningsmålet kallas för [Azure Machine Learning Compute](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute). [Börja migrera och använda det idag](#migrate). Du kan interagera med Azure Machine Learning-tjänsten via dess [Python SDK: er](../machine-learning/service/quickstart-create-workspace-with-python.md), kommandoradsgränssnitt och [Azure Portal](../machine-learning/service/quickstart-get-started.md).

## <a name="support-timeline"></a>Supporttidslinje

| Date | Supportinformation för Batch AI-tjänsten |
| ---- |-----------------|
| 14 december&nbsp;&nbsp; 2018| Du kan använda dina befintliga Azure Batch AI-prenumerationer som tidigare. Men inga **nya prenumerationer** är möjliga och inga nya investeringar görs.|
| 31 mars&nbsp;&nbsp; 2019 | Efter detta datum fungerar inte längre befintliga Batch AI-prenumerationer. |

## <a name="how-does-azure-machine-learning-service-compare"></a>Jämförelse av Azure Machine Learning Service
Det är en molntjänst som du använder för att träna, distribuera, automatisera och hantera maskininlärningsmodeller i den omfattande skala som molnet ger. Få en större förståelse för [Azure Machine Learning Service i den här översikten](../machine-learning/service/overview-what-is-azure-ml.md).
 

En vanlig livscykel för modellutveckling innefattar förberedelse av data, träning och experimentering samt en distributionsfas. Du kan dirigera cykeln från slutpunkt till slutpunkt med hjälp av Machine Learning-pipelines.

![Flödesdiagram](./media/overview-what-happened-batch-ai/lifecycle.png)


[Mer information om hur tjänsten fungerar och dess huvudsakliga begrepp](../machine-learning/service/concept-azure-machine-learning-architecture.md). Många av begreppen i modellens träningsarbetsflöde liknar befintliga begrepp i Batch AI. 

Här visas en mappning av hur du kan tänka på dem mer specifikt:
 
|Batch AI-tjänst|  Azure Machine Learning-tjänst|
|:--:|:---:|
|Arbetsyta|Arbetsyta|
|Kluster|   Beräkning av typen `AmlCompute`|
|Filservrar|DataStores|
|Experiment|Experiment|
|Jobb|Körningar (tillåter kapslade körningar)|
 
Här är en annan vy i samma tabell som hjälper dig att visualisera ytterligare:
 
**Batch AI-hierarki**
![Flödesdiagram](./media/overview-what-happened-batch-ai/batchai-heirarchy.png) 
 
**Azure Machine Learning Service-hierarki**
![Flödesdiagram](./media/overview-what-happened-batch-ai/azure-machine-learning-service-heirarchy.png) 

### <a name="platform-capabilities"></a>Plattformsfunktioner
I Azure Machine Learning Service finns en uppsättning nya funktioner, inklusive en tränings->distributionsstack från slutpunkt till slutpunkt som du kan använda för din AI-utveckling utan att behöva hantera några Azure-resurser. I tabellen nedan jämförs funktioner som stöds för träning mellan de två tjänsterna.

|Funktion|BatchAI-tjänst|Azure Machine Learning-tjänst|
|-------|:-------:|:-------:|
|Alternativ för VM-storlek |CPU/GPU    |CPU/GPU. Stöder också FPGA för inferens|
|AI-förberett kluster (drivrutiner, Docker etc.)|  Ja |Ja|
|Nodförberedelse| Ja|    Nej|
|Val av operativsystemfamilj   |Delvis    |Nej|
|Dedikerade och virtuella LowPriority-datorer  |Ja    |Ja|
|Automatisk skalning   |Ja    |Ja (som standard)|
|Väntetid för automatisk skalning  |Nej |Ja|
|SSH    |Ja|   Ja|
|Montering på klusternivå |Ja (filresurser, blobar, NFS, anpassad)   |Ja (montera eller ladda ned ditt datalager)|
|Distribuerad träning|  Ja |Ja|
|Jobbkörningsläge|    Virtuell dator eller container|    Container|
|Anpassad containeravbildning|    Ja |Ja|
|Valfri verktygssats    |Ja    |Ja (kör Python-skript)|
|JobPreparation|    Ja |Inte ännu|
|Montering på jobbnivå |Ja (filresurser, blobar, NFS, anpassad)   |Ja (filresurser, blobar)|
|Jobbövervakning     |via GetJob|    via körningshistorik (mer omfattande information, anpassad körning för att få fler mått)|
|Hämta jobbloggar och filer/modeller |   via ListFiles och Storage-API:er  |via artefakttjänsten|
 |Stöd för Tensorboard   |Nej|    Ja|
|VM-familjens nivåkvoter |Ja    |Ja (med din tidigare kapacitet som flyttas framåt)|
 
Förutom den föregående tabellen finns det funktioner i Azure Machine Learning Service som normalt sett inte stöds i BatchAI.

|Funktion|BatchAI-tjänst|Azure Machine Learning-tjänst|
|-------|:-------:|:-------:|
|Förberedelse av miljö    |Nej |Ja (Conda-förberedelse och uppladdning till ACR)|
|Justering av hyperparameter  |Nej|    Ja|
|Modellhantering   |Nej |Ja|
|Driftsättning/distribution| Nej  |Via AKS och ACI|
|Förberedelse av data   |Nej |Ja|
|Beräkningsmål    |Virtuella Azure-datorer  |Lokal, BatchAI (som AmlCompute), DataBricks, HDInsight|
|Automatiserad maskininlärning |Nej|    Ja|
|Pipelines  |Nej |Ja|
|Batchbedömning  |Ja    |Ja|
|Portal/CLI-stöd|    Ja |Ja|


### <a name="programming-interfaces"></a>Programmeringsgränssnitt

Tabellen visar de olika programmeringsgränssnitt som är tillgängliga för varje tjänst.
    
|Funktion|BatchAI-tjänst|Azure Machine Learning-tjänst|
|-------|:-------:|:-------:|
|SDK    |Java, C#, Python, Nodejs   |Python (kör både konfigurations- och beräkningsbaserat för vanliga ramverk)|
|CLI    |Ja    |Inte ännu|
|Azure Portal   |Ja    |Ja (utom jobböverföring)|
|REST-API   |Ja    |Ja, men distribuerade över mikrotjänster|




## <a name="why-migrate"></a>Varför ska man migrera?

Genom att uppgradera från BatchAI-tjänstens förhandsversion till GA'ed Azure Machine Learning Service får du en bättre upplevelse tack vare begrepp som är enklare att använda, till exempel beräkningar och datalager. Du har även garanterad kundsupport och SLA:er på GA-nivå för Azure-tjänsten.

Azure Machine Learning Service innehåller också nya funktioner som automatiserad maskininlärning, finjustering av hyperparameter och ML-pipelines, vilket är användbart i de flesta storskaliga AI-arbetsbelastningar. Möjligheten att använda en tränad modell utan att växla till en separat tjänst hjälper till att slutföra datavetenskapsslingan från dataförberedelsen (med SDK:n för dataförberedelse) hela vägen till driftsättning och modellövervakning.

<a name="migrate"></a>

## <a name="how-do-i-migrate"></a>Hur migrerar jag?

Innan du följer stegen i den här migreringsguiden som hjälper dig att mappa kommandon mellan de två tjänsterna, rekommenderar vi att du ägnar lite tid åt att bli bekant med Azure Machine Learning Service via dess [dokumentation](../machine-learning/service/overview-what-is-azure-ml.md) och [självstudie i Python](../machine-learning/service/tutorial-train-models-with-aml.md).

Att undvika avbrott i dina program och dra nytta av de senaste funktionerna ska du göra följande innan 31 mars 2019:

1. Skapa en Azure Machine Learning-tjänstarbetsyta och kom igång:
    + [Python-baserad snabbstart](../machine-learning/service/quickstart-create-workspace-with-python.md)
    + [Azure Portal-baserad snabbstart](../machine-learning/service/quickstart-get-started.md)

1. Konfigurera en [Azure Machine Learning Compute](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute) för att träna modellen.

1. Uppdatera dina skript så att de använder Azure Machine Learning Compute.


### <a name="sdk-migration"></a>SDK-migrering

Nuvarande SDK-stöd i Azure Machine Learning Service är via flera Python-SDK:er. Den huvudsakliga SDK:n uppdateras ungefär varannan vecka och kan installeras från PyPi med följande kommando:

```python
pip install --upgrade azureml-sdk[notebooks]
```

Konfigurera din miljö och installera SDK:n med hjälp av dessa [snabbstartsanvisningar](../machine-learning/service/quickstart-create-workspace-with-python.md#install-the-sdk)

När du öppnar en Jupyter-anteckningsbok där kärnan pekar mot den relevanta Conda-miljön, kommer kommandona i de två tjänsterna mappas så här:


#### <a name="create-a-workspace"></a>Skapa en arbetsyta
Begreppet att initiera en arbetsyta med hjälp av en configuration.json i BatchAI mappas på samma sätt som när en konfigurationsfil används i Azure ML.

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

Mer information om klassen för AML-arbetsytan finns i [SDK-referensdokumentationen](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py).


#### <a name="create-a-compute-cluster"></a>Skapa ett beräkningskluster
Azure Machine Learning har stöd för flera beräkningsmål, varav vissa hanteras av tjänsten och andra kan kopplas till din arbetsyta (t.ex. Ett HDInsight-kluster eller en fjärransluten virtuell dator. Läs mer om olika [beräkningsmål](../machine-learning/service/how-to-set-up-training-targets.md). Konceptet att skapa ett BatchAI-beräkningskluster mappas till att skapa ett AmlCompute-kluster i Azure ML. Amlcompute tar en beräkningskonfiguration som liknar hur du skickar parametrar i BatchAI. En sak att tänka på är att automatisk skalning är aktiverat som standard i AmlCompute-klustret, medan det är avstängt som standard i BatchAI.

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


#### <a name="monitoring-status-of-your-cluster"></a>Övervaka statusen för ditt kluster
Det här är mer direkt i Azure ML, vilket du ser nedan.

I **Batch AI** gjorde du på det här sättet:

```python
cluster = client.clusters.get(cfg.resource_group, cfg.workspace, cluster_name)
utils.cluster.print_cluster_status(cluster)
```

I **Azure Machine Learning Service** försöker du att:

```python
gpu_cluster.get_status().serialize()
```

#### <a name="getting-reference-to-a-storage-account"></a>Hämta en referens till ett lagringskonto
Konceptet med en datalagring som en blob, är förenklat i Azure ML med DataStore-objektet. Som standard skapar Azure ML-arbetsytan ett lagringskonto, men du kan koppla din egen lagring också som en del av arbetsytan som skapas. 

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

Läs mer om hur du registrerar ytterligare lagringskonton eller hämtar en referens till ett annat registrerat datalager i [dokumentationen för Azure ML-tjänsten](../machine-learning/service/how-to-access-data.md#access).


#### <a name="downloading-and-uploading-data"></a>Nedladdning och uppladdning av filer 
Du kan använda någon av tjänsterna till att ladda upp data till lagringskontot på ett enkelt sätt med datalagerreferensen ovan. För BatchAI distribuerar vi även träningsskriptet som en del av filresursen, även om du kommer att se att du kan ange det som en del av din jobbkonfiguration när det gäller Azure ML.

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

#### <a name="create-an-experiment"></a>Skapa ett experiment
Som nämnts ovan har Azure ML ett koncept för ett experiment som liknar BatchAI. Varje experiment kan därefter ha enskilda körningar, vilket liknar hur vi hanterar jobb i BatchAI. I Azure ML kan du ha en hierarki under varje överordnad körning för enskilda underordnade körningar.

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


#### <a name="submit-a-job"></a>Skicka ett jobb
När du skapar ett experiment finns det några olika sätt att skicka en körning på. I det här exemplet försöker vi skapa en modell för djupinlärning med TensorFlow och vi använder en Azure ML-beräkning till detta. En [beräkning](../machine-learning/service/how-to-train-ml-models.md) är helt enkelt en omslutningsfunktion i den underliggande körningskonfigurationen, vilket gör det enklare att skicka körningar. Detta stöds för närvarande endast för Pytorch och TensorFlow. Via begreppet med datalager kan du även se hur enkelt det blir att ange monteringssökvägarna 

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

Själva jobbet skickas i BatchAI via funktionen Skapa.

```python
job_name = datetime.utcnow().strftime('tf_%m_%d_%Y_%H%M%S')
job = client.jobs.create(cfg.resource_group, cfg.workspace, experiment_name, job_name, parameters).result()
print('Created Job {0} in Experiment {1}'.format(job.name, experiment.name))
```

Den fullständiga informationen för träningens kodfragment (inklusive den mnist_replica.py-fil som vi laddade upp till filresursen ovan) finns i [BatchAI-exempel på anteckningsbok i GitHub-lagringsplats](https://github.com/Azure/BatchAI/tree/2238607d5a028a0c5e037168aefca7d7bb165d5c/recipes/TensorFlow/TensorFlow-GPU-Distributed).

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

Den fullständiga informationen för träningens kodfragment (inklusive mnist_replica.py-filen) finns i [Azure ML-exempel på anteckningsbok i GitHub-lagringsplats](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/distributed-tensorflow-with-parameter-server). Datalagringen kan antingen monteras på enskilda noder eller så kan träningsdata hämtas från själva noden. Mer information om referenser till datalager i din beräkning finns i [dokumentationen för Azure ML-tjänsten](../machine-learning/service/how-to-access-data.md#access). 

En körning skickas i Azure ML via funktionen Skicka.

```python
run = experiment.submit(estimator)
print(run)
```

Det finns ett annat sätt att ange parametrar för din körning med en körningskonfiguration – vilket är särskilt användbart när en anpassad träningsmiljö ska definieras. Du hittar mer information i det här [exemplet på en AmlCompute-anteckningsbok](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-amlcompute/train-on-amlcompute.ipynb). 

#### <a name="monitor-your-run"></a>Övervaka körningen
När du skickar en körning kan du antingen vänta tills den är klar eller övervaka den i Azure ML med Jupyter-widgetar som du kan anropa direkt från din kod. Du kan också hämta kontexten för en tidigare körning genom att göra en loop genom olika experiment i en arbetsyta och genom enskilda körningar inom varje experiment.

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



#### <a name="editing-a-cluster"></a>Redigera ett kluster
Det är enkelt att ta bort ett kluster. Dessutom kan du med Azure ML också uppdatera ett kluster från anteckningsboken om du vill skala till ett högre antal noder eller öka tidsperioden för inaktivitet innan du skalar ned klustret. Du kan inte ändra VM-storleken för själva klustret, eftersom det kräver en ny distribution i serverdelen.

I **Batch AI** gjorde du på det här sättet:
```python
_ = client.clusters.delete(cfg.resource_group, cfg.workspace, cluster_name)
```

I **Azure Machine Learning Service** försöker du att:

```python
gpu_cluster.delete()

gpu_cluster.update(min_nodes=2, max_nodes=4, idle_seconds_before_scaledown=600)
```

## <a name="support"></a>Support

BatchAI kommer att tas ur bruk 31 mars och blockerar redan nya prenumerationer från att registreras mot tjänsten, såvida den inte är godkänd genom ett undantag från supporten.  Kontakta oss på [Förhandsversionen av Azure Batch AI-träningen](mailto:AzureBatchAITrainingPreview@service.microsoft.com) om du har frågor eller feedback när du migrerar till Azure Machine Learning Service.

Microsoft Azure Machine Learning Service är nu allmänt tillgänglig. Det innebär att den innehåller ett dedikerat serviceavtal och olika supportplaner att välja bland.

Priset för att använda Azure-infrastrukturen via tjänsten BatchAI eller via Azure Machine Learning Service bör inte variera, eftersom vi bara debiterar priset för den underliggande beräkningen i båda fallen. Mer information finns på sidan med [priskalkylatorn](https://azure.microsoft.com/pricing/details/machine-learning-service/).

Visa regional tillgänglighet mellan de två tjänsterna i [Azure-portalen](https://azure.microsoft.com/global-infrastructure/services/?products=batch-ai,machine-learning-service&regions=all).


## <a name="next-steps"></a>Nästa steg

+ Läs [Översikt över Azure Machine Learning-tjänsten](../machine-learning/service/overview-what-is-azure-ml.md).

+ [Konfigurera ett beräkningsmål för modellträning](../machine-learning/service/how-to-set-up-training-targets.md) med Azure Machine Learning-tjänsten.

+ Granska [Azure-översikten](https://azure.microsoft.com/updates/) för att lära dig av andra Azure-tjänstuppdateringar.
