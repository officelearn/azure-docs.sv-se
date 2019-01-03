---
title: Beräkningsmål för modellträning
titleSuffix: Azure Machine Learning service
description: Konfigurera utbildning-miljöer (beräkningsmål) för machine learning-modellen. Du kan enkelt växla mellan miljöer för utbildning. Starta utbildning lokalt. Om du vill skala ut kan växla till en molnbaserad beräkningsmål.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.reviewer: larryfr
manager: cgronlun
ms.service: machine-learning
ms.component: core
ms.topic: article
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 9037f6d7602f186bc30e55acbc050280bca134ee
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/27/2018
ms.locfileid: "53794472"
---
# <a name="set-up-compute-targets-for-model-training"></a>Konfigurera beräkningsmål för modellträning

Med Azure Machine Learning-tjänsten kan du träna din modell på olika beräkningsresurser. Dessa beräkningsresurser, kallas __beräkningsmål__, kan vara lokala eller i molnet. I den här artikeln får du lära dig om stöds beräkningsmål och hur de används.

Beräkningsmål är en resurs där du kör din utbildningsskript, eller du förvarar din modell när det distribueras som en webbtjänst. Du kan skapa och hantera ett beräkningsmål med hjälp av SDK: N för Azure Machine Learning, Azure-portalen eller Azure CLI. Om du har beräkningsmål som skapats via en annan tjänst (till exempel ett Azure HDInsight-kluster), kan du använda dessa mål genom att koppla dem till din arbetsyta för Azure Machine Learning-tjänsten.

Det finns tre olika kategorier av beräkningsmål som har stöd för Azure Machine Learning:

* __Lokala__: Den lokala datorn eller en moln-baserad virtuell dator (VM) som du använder som en miljö för utveckling och experimentering. 
* __Hanterade beräkning__: Beräkning av Azure Machine Learning är en beräkning som erbjuder som hanteras av Azure Machine Learning-tjänsten. Erbjudandet kan du enkelt skapa en enda eller flera noder beräkning för utbildning, testa och batch inferensjobb.
* __Ansluten beräkning__: Du kan också ta med din egen Azure-molnet beräkning och koppla den till Azure Machine Learning. Läs mer om stöd för beräkningstyper och hur de används i följande avsnitt.


## <a name="supported-compute-targets"></a>Stöds beräkningsmål

Azure Machine Learning-tjänsten har olika stöd för olika beräkningsmål. En typisk modellen för säkerhetsutveckling börjar med utveckling och experiment som utförs på en liten mängd data. I det här skedet rekommenderar vi att du använder en lokal miljö som din lokala dator eller en molnbaserad VM. När du skalar upp utbildning på större datauppsättningar eller distribuerad utbildning, Använd en Azure Machine Learning compute-miljö att skapa en enda eller flernodigt kluster som skalar varje gång skickar du en körning. Du kan även bifoga dina egna beräkningsresurs, även om stöd för olika scenarier kan variera som beskrivs i följande tabell:

|Beräkningsmål| GPU-acceleration | Automatiserad<br/> finjustering av hyperparametrar | Automatiserad</br> maskininlärning | Pipeline-vänlig|
|----|:----:|:----:|:----:|:----:|
|[Lokal dator](#local)| Kanske | &nbsp; | ✓ | &nbsp; |
|[Azure Machine Learning-beräkning](#amlcompute)| ✓ | ✓ | ✓ | ✓ |
|[Fjärransluten virtuell dator](#vm) | ✓ | ✓ | ✓ | ✓ |
|[Azure Databricks](#databricks)| &nbsp; | &nbsp; | ✓ | ✓[*](#pipeline-only) |
|[Azure Data Lake Analytics](#adla)| &nbsp; | &nbsp; | &nbsp; | ✓[*](#pipeline-only) |
|[Azure HDInsight](#hdinsight)| &nbsp; | &nbsp; | &nbsp; | ✓ |

> [!IMPORTANT]
> <a id="pipeline-only"></a>__*__ _Azure Databricks och Azure Data Lake Analytics kan endast användas i en pipeline._<br/>
> Mer information om pipelines finns i [Pipelines i Azure Machine Learning](concept-ml-pipelines.md).
>
> Azure Machine Learning-beräkningsmiljö måste skapas från en arbetsyta. Du kan inte koppla befintliga instanser till en arbetsyta.
>
> Andra beräkningsmål måste skapas utanför Azure Machine Learning och sedan ansluts till din arbetsyta.
>
> När du tränar en modell compute några mål är beroende av Docker-behållaravbildningar. GPU-basavbildningen måste bara användas på Microsoft Azure-tjänster. För modellträning omfattar tjänsterna:
> * Azure Machine Learning-beräkning
> * Azure Kubernetes Service
> * Windows virtuell dator för datavetenskap (DSVM)

## <a name="workflow"></a>Arbetsflöde

Arbetsflöde för att utveckla och distribuera en modell med Azure Machine Learning följer de här stegen:

1. Utveckla de machine learning-utbildningsskript i Python.
1. Skapa och konfigurera beräkningsmål eller bifoga en befintlig beräkningsmål.
1. Skicka utbildningsskript till beräkningsmål.
1. Granska resultaten för att hitta den bästa modellen.
1. Registrera modellen i registret för modellen.
1. Distribuera modellen.

> [!NOTE]
> Utbildning skriptet inte är kopplat till en specifik beräkningsmål. Du kan träna från början på den lokala datorn och sedan växla beräkningsmål utan att behöva skriva om skriptet utbildning.
> 
> När du kopplar ett beräkningsmål till din arbetsyta genom att skapa en hanterad compute eller genom att koppla en befintlig beräkning, ange ett namn som din databearbetning. Namnet måste vara mellan två och 16 tecken långt.

Om du vill växla från en beräkningsmål till ett annat du behöver en [körningskonfigurationen](concept-azure-machine-learning-architecture.md#run-configuration). Körningskonfigurationen definierar hur du kör skriptet på beräkningsmål.

## <a name="training-scripts"></a>Utbildningsskript

När du startar en körning för utbildning, skapar en ögonblicksbild av den katalog som innehåller dina utbildningsskript och skickar dem till beräkningsmål. Mer information finns i [ögonblicksbilder](concept-azure-machine-learning-architecture.md#snapshot).

## <a id="local"></a>Lokal dator

När du tränar lokalt kan du använda SDK för att skicka utbildning-åtgärd. Du kan träna med hjälp av en miljö som hanteras av användaren eller systemet.

### <a name="user-managed-environment"></a>Användarhanterade miljö

Kontrollera att alla nödvändiga paket är tillgängliga i Python-miljön där du kör skriptet i en användarhanterade miljö. Följande kodavsnitt är ett exempel på hur du konfigurerar utbildning för en användarhanterade miljö:

```python
from azureml.core.runconfig import RunConfiguration

# Edit a run configuration property on the fly.
run_config_user_managed = RunConfiguration()

run_config_user_managed.environment.python.user_managed_dependencies = True

# Choose a specific Python environment by pointing to a Python path. For example:
# run_config.environment.python.interpreter_path = '/home/ninghai/miniconda3/envs/sdk2/bin/python'
```

  
### <a name="system-managed-environment"></a>System-hanterad miljö

Hanteras av datorn miljöer är beroende av conda att hantera beroenden. Conda skapar en fil med namnet **conda_dependencies.yml** som innehåller en lista över beroenden. Du kan be system och skapa en ny conda-miljö som du kan köra dina skript det. Hanteras av datorn miljöer kan återanvändas senare, så länge filen conda_dependencies.yml är oförändrat. 

Den första konfigurationen upp av en ny miljö kan ta flera minuter att slutföra baserat på storleken på de nödvändiga beroendena. Följande kodfragment visar hur du skapar en miljö för hanteras av datorn som är beroende av scikit-Läs:

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

run_config_system_managed = RunConfiguration()

run_config_system_managed.environment.python.user_managed_dependencies = False
run_config_system_managed.auto_prepare_environment = True

# Specify the conda dependencies with scikit-learn

run_config_system_managed.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
```

## <a id="amlcompute"></a>Azure Machine Learning-beräkning

Beräkning av Azure Machine Learning är en infrastruktur för hanterade beräkning som används att enkelt skapa en beräkning för en eller flera noder. Beräkningen har skapats i din arbetsyteregion som en resurs som kan delas med andra användare i din arbetsyta. Beräkningen som skalar upp automatiskt när ett jobb skickas och kan placeras i en Azure-nätverk. Beräkningen som körs i en miljö med behållare och paketerar din modell beroenden i en Docker-behållare.

Du kan använda beräkning av Azure Machine Learning för att distribuera träningsprocess över ett kluster med CPU eller GPU-beräkningsnoder i molnet. Mer information om de storlekar som innehåller GPU: er finns i [GPU-optimerade virtuella datorstorlekar](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu).

> [!NOTE]
> Beräkning av Azure Machine Learning har standardgränser, till exempel antalet kärnor som kan allokeras. Mer information finns i [hantera och begära kvoter för Azure-resurser](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas).

Du kan skapa en Azure Machine Learning compute-miljö på begäran när du schemalägger en körning eller som en beständig resurs.

### <a name="run-based-creation"></a>Kör-baserade skapas

Du kan skapa en Azure Machine Learning-beräkningsmiljö som beräkningsmål vid körning. Beräkningen som skapas automatiskt för din körning och skalar upp till antalet **max_nodes** som du anger i kör config. Beräkningarna tas bort automatiskt när körningen har slutförts.

> [!IMPORTANT]
> Kör-baserade skapandet av en Azure Machine Learning-beräkningsmiljö förhandsvisas just nu. Använd inte kör-baserade skapas om du använder automatisk finjustering av hyperparametrar eller automatiserade maskininlärning. Skapa beräkningsmiljö Azure Machine Learning för att använda finjustering av hyperparametrar eller automatiserade maskininlärning, innan du skickar en körning.

```python
from azureml.core.compute import ComputeTarget, AmlCompute

# First, list the supported VM families for Azure Machine Learning Compute
AmlCompute.supported_vmsizes()

from azureml.core.runconfig import RunConfiguration

# Create a new runconfig object
run_config = RunConfiguration()

# Signal that you want to use AmlCompute to execute the script
run_config.target = "amlcompute"

# AmlCompute is created in the same region as your workspace
# Set the VM size for AmlCompute from the list of supported_vmsizes
run_config.amlcompute.vm_size = 'STANDARD_D2_V2'

```

### <a name="persistent-compute-basic"></a>Beständiga beräkning: Basic

En beständig Azure Machine Learning-beräkningsmiljö kan återanvändas i jobb. Beräkningarna kan delas med andra användare i arbetsytan och sparas mellan jobb.

Om du vill skapa en beständig Azure Machine Learning-miljö beräkningsresurs du anger den **vm_size** och **max_nodes** egenskaper. Azure Machine Learning använder sedan smarta standardvärden för de andra egenskaperna. Compute-skalar ned till noll noder när den inte används och skapar dedikerade virtuella datorer för att köra dina jobb efter behov. 

* **vm_size**: VM-familj med noder som skapats av beräkning av Azure Machine Learning.
* **max_nodes**: Det maximala antalet noder att automatiskt skala upp till när du kör ett jobb på beräkning av Azure Machine Learning.

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that the cluster doesn't already exist
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                           max_nodes=4)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)

```

### <a name="persistent-compute-advanced"></a>Beständiga beräkning: Advanced

Du kan också konfigurera flera avancerade egenskaper när du skapar en Azure Machine Learning-beräkningsmiljö. Egenskaperna kan du skapa ett beständiga kluster med fast storlek eller i ett befintligt Azure virtuellt nätverk i din prenumeration.

Tillsammans med den **vm_size** och **max_nodes** egenskaper, du kan också använda följande egenskaper:

* **min_nodes**: Det minsta antalet noder att skala ned till när du kör ett jobb i en Azure Machine Learning-beräkningsmiljö. Minsta standard är noll (0) noder.
* **vm_priority**: Typ av virtuell dator för att använda när du skapar en miljö för Azure Machine Learning beräkningsresursen. Välj mellan **dedikerade** (standard) och **lowpriority**. Virtuella datorer med låg prioritet använder överflödig kapacitet i Azure. Dessa virtuella datorer är billigare, men kan vara pre-empted körs när dessa virtuella datorer används.
* **idle_seconds_before_scaledown**: Mängden inaktivitetstid som ska vänta efter en körning är klar och innan du automatisk skalning upp till antalet **min_nodes**. Tids inaktivitet som standard är 120 sekunder.
* **vnet_resourcegroup_name**: Resursgruppen för den __befintliga__ virtuellt nätverk. Azure Machine Learning-beräkningsmiljö har skapats i det här virtuella nätverket.
* **vnet_name**: Namnet på det virtuella nätverket. Det virtuella nätverket måste vara i samma region som din Azure Machine Learning-arbetsyta.
* **subnet_name**: Namnet på undernätet i det virtuella nätverket. Azure Machine Learning-miljö beräkningsresurser tilldelas IP-adresser från det här intervallet i undernätet.

> [!TIP]
> När du skapar en beständig Azure Machine Learning-miljö beräkningsresurs kan du uppdatera egenskaper som antal **min_nodes** eller **max_nodes**. Du kan uppdatera en egenskap genom att anropa den `update()` funktionen för egenskapen.

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that the cluster doesn't already exist 
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                           vm_priority='lowpriority',
                                                           min_nodes=2,
                                                           max_nodes=4,
                                                           idle_seconds_before_scaledown='300',
                                                           vnet_resourcegroup_name='<my-resource-group>',
                                                           vnet_name='<my-vnet-name>',
                                                           subnet_name='<my-subnet-name>')
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)

```


## <a id="vm"></a>Fjärransluten virtuell dator

Azure Machine Learning har även stöd för att få ut dina egna beräkningsresurs och kopplar den till din arbetsyta. En sådan resurstypen är ett godtyckligt fjärransluten virtuell dator, så länge den kan nås från Azure Machine Learning-tjänsten. Resursen kan vara en Azure-dator, en fjärrserver i din organisation eller lokalt. Mer specifikt kan baserat på IP-adress och autentiseringsuppgifter (användarnamn och lösenord eller SSH-nyckel), du använda alla tillgängliga virtuella datorer för fjärranslutna körningar.
Du kan använda en inbyggd system conda-miljö, en befintlig Python-miljö eller en Docker-behållare. När du kör genom att använda en Docker-behållare kan behöva du ha Docker-motor som körs på den virtuella datorn. Fjärrfunktioner för virtuell dator är särskilt användbart när du vill att en molnbaserad experimentering miljö för utveckling och som är mer flexibel än den lokala datorn.

> [!TIP]
> Använd DSVM som virtuell Azure-dator med det här scenariot. Den här virtuella datorn är en förkonfigurerad för datavetenskap och AI-utvecklingsmiljö i Azure den virtuella datorn erbjuder en granskad val av verktyg och ramverk för fullständig livscykel machine learning-utveckling. Mer information om hur du använder DSVM med Azure Machine Learning finns [konfigurera en utvecklingsmiljö](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-environment#dsvm).

> [!WARNING]
> Azure Machine Learning stöder endast virtuella datorer som kör Ubuntu. När du skapar en virtuell dator eller välj en befintlig virtuell dator, väljer du en virtuell dator med Ubuntu.

Följande steg använder SDK för att konfigurera en DSVM som utbildning mål:

1. Om du vill koppla en befintlig virtuell dator som en beräkningsmål, måste du ange det fullständigt kvalificerade domännamnet (FQDN), användarnamn och lösenord för den virtuella datorn. I det här exemplet ersätter \<fqdn > med offentliga FQDN för den virtuella datorn eller den offentliga IP-adressen. Ersätt \<användarnamn > och \<lösenord > med SSH-användarnamn och lösenord för den virtuella datorn.

    ```python
    from azureml.core.compute import RemoteCompute, ComputeTarget
    
    # Create the compute config
    attach_config = RemoteCompute.attach_configuration(address = "ipaddress",
                                                       ssh_port=22,
                                                       username='<username>',
                                                       password="<password>")

    # If you use SSH instead of a password, use this code:
    #                                                  ssh_port=22,
    #                                                  username='<username>',
    #                                                  password=None,
    #                                                  private_key_file="path-to-file",
    #                                                  private_key_passphrase="passphrase")

    # Attach the compute target
    compute = ComputeTarget.attach(ws, "attach-dsvm", attach_config)

    compute.wait_for_completion(show_output=True)
    ```

1. Skapa en konfiguration för beräkningsmål DSVM. Docker och conda används för att skapa och konfigurera miljön på DSVM.

    ```python
    from azureml.core.runconfig import RunConfiguration
    from azureml.core.conda_dependencies import CondaDependencies

    # Load into memory the cpu-dsvm.runconfig file created in the previous attach operation
    run_config = RunConfiguration(framework = "python")

    # Set the compute target to the Linux DSVM
    run_config.target = compute_target_name

    # Use Docker in the remote VM
    run_config.environment.docker.enabled = True

    # Use the CPU base image
    # To use GPU in DSVM, you must also use the GPU base Docker image "azureml.core.runconfig.DEFAULT_GPU_IMAGE"
    run_config.environment.docker.base_image = azureml.core.runconfig.DEFAULT_CPU_IMAGE
    print('Base Docker image is:', run_config.environment.docker.base_image)

    # Ask the system to provision a new conda environment based on the conda_dependencies.yml file
    run_config.environment.python.user_managed_dependencies = False

    # Prepare the Docker and conda environment automatically when they're used for the first time
    run_config.prepare_environment = True

    # Specify the CondaDependencies object
    run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])

    ```

## <a id="databricks"></a>Azure Databricks

Azure Databricks är en Apache Spark-baserad miljö i Azure-molnet. Miljön kan användas som ett beräkningsmål när du tränar modeller med en Azure Machine Learning-pipeline.

> [!IMPORTANT]
> En Azure Databricks beräkningsmål kan bara användas i en Machine Learning-pipeline.
>
> Du måste skapa en Azure Databricks-arbetsyta innan du kan använda den för att träna din modell. Om du vill skapa dessa resurs [kör ett Spark-jobb på Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal).

Om du vill koppla Azure Databricks som beräkningsmål, måste du använder Azure Machine Learning SDK och ange följande information:

* __Beräkningsnamn__: Namnet ska tilldelas den här beräkningsresursen.
* __Databricks Arbetsytenamn__: Namnet på Azure Databricks-arbetsytan.
* __Åtkomsttoken__: Den åtkomst-token som används för att autentisera till Azure Databricks. Om du vill generera en åtkomsttoken, se [autentisering](https://docs.azuredatabricks.net/api/latest/authentication.html).

Följande kod visar hur du ansluter Azure Databricks som beräkningsmål:

```python
databricks_compute_name = os.environ.get("AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get("AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get("AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get("AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create the attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group = databricks_resource_group,
                                                           workspace_name = databricks_workspace_name,
                                                           access_token = databricks_access_token)
    databricks_compute = ComputeTarget.attach(
             ws,
             databricks_compute_name,
             attach_config
         )
    
    databricks_compute.wait_for_completion(True)
```

## <a id="adla"></a>Azure Data Lake Analytics

Azure Data Lake Analytics är en stordata analysplattform i Azure-molnet. Plattformen kan användas som ett beräkningsmål när du tränar modeller med en Azure Machine Learning-pipeline.

> [!IMPORTANT]
> En Azure Data Lake Analytics beräkningsmål kan bara användas i en Machine Learning-pipeline.
>
> Du måste skapa ett Azure Data Lake Analytics-konto innan du kan använda den för att träna din modell. Om du vill skapa den här resursen [Kom igång med Azure Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal).

Om du vill koppla Data Lake Analytics som beräkningsmål du använder Azure Machine Learning SDK och ange följande information:

* __Beräkningsnamn__: Namnet ska tilldelas den här beräkningsresursen.
* __Resursgrupp__: Den resursgrupp som innehåller Data Lake Analytics-kontot.
* __Kontonamn__: Namnet på Data Lake Analytics-kontot.

Följande kod visar hur du kopplar Data Lake Analytics som beräkningsmål:

```python
adla_compute_name = os.environ.get("AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get("AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get("AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    
    # Create the attach config
    attach_config = AdlaCompute.attach_configuration(resource_group = adla_resource_group,
                                                     account_name = adla_account_name)
    # Attach the ADLA
    adla_compute = ComputeTarget.attach(
             ws,
             adla_compute_name,
             attach_config
         )
    
    adla_compute.wait_for_completion(True)
```

> [!TIP]
> Azure Machine Learning pipelines endast fungerar med data som lagras i datalagret standard för Data Lake Analytics-kontot. Om de data du behöver finns i en icke-standard-store, kan du använda en [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) åtgärd för att kopiera data innan du träna modellen.

## <a id="hdinsight"></a>Azure HDInsight 

Azure HDInsight är en populär plattform för stordataanalys. Plattformen ger Apache Spark, som kan användas för att träna din modell.

> [!IMPORTANT]
> Du måste skapa HDInsight-klustret innan du kan använda den för att träna din modell. För att skapa en Apache Spark på HDInsight-kluster, se [skapa ett Spark-kluster i HDInsight](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql).
>
> När du skapar klustret måste du ange ett SSH-användarnamn och lösenord. Anteckna dessa värden som du behöver dem för att använda HDInsight som en beräkningsmål.
>
> När klustret har skapats kan den har det fullständiga Domännamnet \<klusternamn >. azurehdinsight.net, där \<klusternamn > är namnet som du angav för klustret. Behöver du FQDN-adressen (eller den offentliga IP-adressen för klustret) ska användas i klustret som beräkningsmål.

Om du vill konfigurera HDInsight som en beräkningsmål, måste du ange den fullständigt domännamn, användarnamn och lösenord för HDInsight-klustret. I följande exempel använder SDK för att koppla ett kluster till din arbetsyta. I det här exemplet ersätter \<fqdn > med offentliga FQDN för klustret eller offentliga IP-adress. Ersätt \<användarnamn > och \<lösenord > med SSH-användarnamn och lösenord för klustret.

> [!NOTE]
> Om du vill hitta det fullständiga Domännamnet för klustret, gå till Azure-portalen och välj ditt HDInsight-kluster. Under __översikt__, du kan se det fullständiga Domännamnet i den __URL__ posten. Om du vill ha det fullständiga Domännamnet, ta bort https:\// prefix från början av transaktionen.

![Hämta det fullständiga Domännamnet för ett HDInsight-kluster i Azure portal](./media/how-to-set-up-training-targets/hdinsight-overview.png)

```python
from azureml.core.compute import HDInsightCompute, ComputeTarget

try:
    # Attach an HDInsight cluster as a compute target
    attach_config = HDInsightCompute.attach_configuration(address = "fqdn-or-ipaddress",
                                                          ssh_port = 22,
                                                          username = "username",
                                                          password = None, #if using ssh key
                                                          private_key_file = "path-to-key-file",
                                                          private_key_phrase = "key-phrase")
    compute = ComputeTarget.attach(ws, "myhdi", attach_config)
except UserErrorException as e:
    print("Caught = {}".format(e.message))
    print("Compute config already attached.")

# Configure the HDInsight run
# Load the runconfig object from the myhdi.runconfig file generated in the previous attach operation
run_config = RunConfiguration.load(project_object = project, run_config_name = 'myhdi')

# Ask the system to prepare the conda environment automatically when it's used for the first time
run_config.auto_prepare_environment = True
```

## <a name="submit-training-runs"></a>Skicka träningskörningar

Det finns två sätt att skicka en utbildning körning:

* Skicka ett utbildnings kör genom att använda en `ScriptRunConfig` objekt.
* Skicka ett utbildnings kör genom att använda en `Pipeline` objekt.

> [!IMPORTANT]
> Azure Databricks och Azure-Datalake-Analytics compute mål kan bara användas i en pipeline.
>
> Den lokala beräkningsmål kan inte användas i en pipeline.

### <a name="scriptrunconfig-object"></a>ScriptRunConfig objekt

Mönstret kod för att skicka ett utbildnings kör med den `ScriptRunConfig` objekt är detsamma för alla typer av beräkningsmål:

1. Skapa en `ScriptRunConfig` objekt med hjälp av körningskonfigurationen för beräkningsmål.
1. Skicka in körningen.
1. Vänta tills den kör för att slutföra.

I följande exempel används konfigurationen för hanteras av datorn lokala beräkningsmål skapade tidigare:

```python
src = ScriptRunConfig(source_directory = script_folder, script = 'train.py', run_config = run_config_system_managed)
run = exp.submit(src)
run.wait_for_completion(show_output = True)
```


### <a name="pipeline-object"></a>Pipeline-objekt

Mönstret kod för att skicka ett utbildnings kör med en `Pipeline` objekt är detsamma för alla typer av beräkningsmål:

1. Lägg till ett steg för att den `Pipeline` objekt för beräkningsresursen.
1. Skicka en körning med hjälp av pipelinen.
1. Vänta tills den kör för att slutföra.

I följande exempel används Azure Databricks-beräkningsmål som skapades tidigare:

```python
dbStep = DatabricksStep(
    name="databricksmodule",
    inputs=[step_1_input],
    outputs=[step_1_output],
    num_workers=1,
    notebook_path=notebook_path,
    notebook_params={'myparam': 'testparam'},
    run_name='demo run name',
    databricks_compute=databricks_compute,
    allow_reuse=False
)

# List of steps to run
steps = [dbStep]
pipeline = Pipeline(workspace=ws, steps=steps)
pipeline_run = Experiment(ws, 'Demo_experiment').submit(pipeline)
pipeline_run.wait_for_completion()
```

Mer information om machine learning pipelines finns i [Pipelines och Azure Machine Learning](concept-ml-pipelines.md).

Till exempel Jupyter-anteckningsböcker som visar hur du tränar en modell med hjälp av en pipeline finns i [ https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline ](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline).

## <a name="access-computes-in-the-azure-portal"></a>Åtkomst beräknar i Azure portal

Du kan komma åt beräkningsmål som är associerade med din arbetsyta i Azure-portalen. 

### <a name="view-compute-targets"></a>Visa beräkningsmål

Använd följande steg om du vill se beräkningsmål för din arbetsyta:

1. Navigera till den [Azure-portalen](https://portal.azure.com) och öppna din arbetsyta.
1. Under __program__väljer __Compute__.

    ![Visa beräkningsmål](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)

### <a name="create-a-compute-target"></a>Skapa ett beräkningsmål

Följ de här stegen om du vill visa en lista över beräkningsmål. Använd sedan dessa steg för att skapa ett beräkningsmål:

1. Klicka på plustecknet (+) för att lägga till ett beräkningsmål.

    ![Lägg till ett beräkningsmål](./media/how-to-set-up-training-targets/add-compute-target.png)

1. Ange ett namn för beräkningsmål.
1. Välj **beräkning av Machine Learning** som typ av beräkning som ska användas för __utbildning__.

    > [!IMPORTANT]
    > Du kan bara skapa en Azure Machine Learning-beräkningsmiljö som hanterade beräkningsresurs för utbildning.

1. Fyll i formuläret. Ange värden för de obligatoriska egenskaperna särskilt **VM-serie**, och **högsta antalet noder** du använder för att sätta upp beräkningarna. 
1. Välj __Skapa__.
1. Visa status för åtgärden för att skapa genom att välja beräkningsmål i listan:

    ![Välj ett beräkningsmål för att visa status för Skapa-åtgärd](./media/how-to-set-up-training-targets/View_list.png)

1. Då visas information om beräkningsmål:

    ![Visa datorn målinformation](./media/how-to-set-up-training-targets/compute-target-details.png)

Du kan nu skicka en körning mot datormål enligt beskrivningen ovan.


### <a name="reuse-existing-compute-targets"></a>Återanvända befintliga beräkningsmål

Följ stegen som beskrivs ovan för att visa en lista över beräkningsmål. Sedan använda de här stegen för att återanvända en beräkningsmål:

1. Klicka på plustecknet (+) för att lägga till ett beräkningsmål.
1. Ange ett namn för beräkningsmål.
1. Välj den typ av beräkningsresurser kan bifoga för __utbildning__:

    > [!IMPORTANT]
    > Inte alla beräkningsresurser typer kan kopplas från Azure-portalen.
    > Vilka beräkningstyper av som för närvarande kan kopplas till utbildning är:
    >
    > * En fjärransluten virtuell dator
    > * Azure Databricks
    > * Azure Data Lake Analytics
    > * Azure HDInsight

1. Fyll i formuläret och ange värden för de obligatoriska egenskaperna.

    > [!NOTE]
    > Microsoft rekommenderar att du använder SSH-nycklar, vilket är säkrare än lösenord. Lösenord är sårbara för råstyrkeattacker. SSH-nycklar är beroende av kryptografiska signaturer. Information om hur du skapar SSH-nycklar för användning med Azure virtuella datorer finns i följande dokument:
    >
    > * [Skapa och använda SSH-nycklar på Linux eller macOS](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Skapa och använda SSH-nycklar i Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. Välj __bifoga__.
1. Visa status för åtgärden koppla genom att välja beräkningsmål i listan.

Nu kan du skicka en körning mot dessa beräkningsmål enligt beskrivningen ovan.

## <a name="notebook-examples"></a>Notebook-exempel

Exempel finns i anteckningsböcker på följande platser:

* [How-to-till-användning – azureml/utbildning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [självstudier/img-klassificering – del 1 – training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nästa steg

* [Azure Machine Learning SDK-referens](https://aka.ms/aml-sdk)
* [Självstudier: Träna en modell](tutorial-train-models-with-aml.md)
* [Var du vill distribuera modeller](how-to-deploy-and-where.md)
* [Skapa machine learning pipelines med Azure Machine Learning-tjänsten](concept-ml-pipelines.md)
