---
title: Beräkningsmål för modellträning
titleSuffix: Azure Machine Learning service
description: Konfigurera utbildning-miljöer (beräkningsmål) för machine learning-modellen. Du kan enkelt växla miljöer för utbildning. Starta utbildning lokalt och om du vill skala ut kan växla till en molnbaserad beräkningsmål. Databricks
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
ms.openlocfilehash: 664d56daf3e70e2e5699d0c07331c466c60e06c5
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2018
ms.locfileid: "53338870"
---
# <a name="set-up-compute-targets-for-model-training"></a>Konfigurera beräkningsmål för modellträning

Med Azure Machine Learning-tjänsten kan du träna din modell på olika beräkningsresurser. Dessa beräkningsresurser, kallas __beräkningsmål__, kan vara lokala eller i molnet. I det här dokumentet lär du dig att stöds beräkningsmål och hur de används.

Beräkningsmål är en resurs där dina utbildningsskript körs eller är värd för din modell när de distribueras som en webbtjänst. Du kan skapa och hantera ett beräkningsmål som använder Azure Machine Learning SDK, Azure-portalen eller Azure CLI. Om du har beräkningsmål som har skapats via en annan tjänst (till exempel ett HDInsight-kluster), kan du använda dem genom att koppla dem till din arbetsyta för Azure Machine Learning-tjänsten.

Det finns tre olika kategorier av beräkningsmål som har stöd för Azure Machine Learning:

* __Lokala__: Den lokala datorn eller en molnbaserad VM som du använder som en utvecklings-/ experimentmiljön. 

* __Hanterade beräkning__: Beräkning av Azure Machine Learning är en beräkning som hanteras av Azure Machine Learning-tjänsten. Det kan du enkelt skapa en eller flera node beräkning för utbildning, testa och batch inferensjobb.

* __Ansluten beräkning__: Du kan också ta med din egen Azure-molnet beräkning och koppla den till Azure Machine Learning. Läs mer nedan på stöds beräkningstyper och hur de används.


## <a name="supported-compute-targets"></a>Stöds beräkningsmål

Azure Machine Learning-tjänsten har olika stöd för olika beräkningsmål. En typisk modellen för säkerhetsutveckling börjar med utveckling/experimentering på en liten mängd data. I det här skedet bör du använda en lokal miljö. Den lokala datorn eller en molnbaserad VM. När du skalar upp utbildning på större datauppsättningar eller göra distribuerad utbildning, bör du använda beräkning av Azure Machine Learning för att skapa ett enda eller flera node kluster som skalar varje gång du skickar en körning. Du kan även bifoga dina egna beräkningsresurs, även om stöd för olika scenarier kan variera som beskrivs nedan:

|Beräkningsmål| GPU-acceleration | Automatiserad finjustering av hyperparametrar | Automatiserad maskininlärning | Pipeline-vänlig|
|----|:----:|:----:|:----:|:----:|
|[Lokal dator](#local)| Kanske | &nbsp; | ✓ | &nbsp; |
|[Azure Machine Learning-beräkning](#amlcompute)| ✓ | ✓ | ✓ | ✓ |
|[Fjärransluten virtuell dator](#vm) | ✓ | ✓ | ✓ | ✓ |
|[Azure Databricks](#databricks)| &nbsp; | &nbsp; | ✓ | ✓[*](#pipeline-only) |
|[Azure Data Lake Analytics](#adla)| &nbsp; | &nbsp; | &nbsp; | ✓[*](#pipeline-only) |
|[Azure HDInsight](#hdinsight)| &nbsp; | &nbsp; | &nbsp; | ✓ |

> [!IMPORTANT]
> <a id="pipeline-only"></a>__*__ Azure Databricks och Azure Data Lake Analytics kan __endast__ användas i en pipeline. Mer information om pipelines finns i den [Pipelines i Azure Machine Learning](concept-ml-pipelines.md) dokumentet.

> [!IMPORTANT]
> Beräkning av Azure Machine Learning måste skapas från en arbetsyta. Du kan inte koppla befintliga instanser till en arbetsyta.
>
> Andra beräkningsmål måste skapas utanför Azure Machine Learning och sedan ansluts till din arbetsyta.

> [!NOTE]
> Vissa compute mål förlitar sig på Docker-behållaravbildningar när träna en modell. GPU-basavbildningen måste bara användas på Microsoft Azure-tjänster. För modellträning är dessa tjänster:
>
> * Azure Machine Learning-beräkning
> * Azure Kubernetes Service
> * Den virtuella datorn för datavetenskap.

## <a name="workflow"></a>Arbetsflöde

Arbetsflöde för att utveckla och distribuera en modell med Azure Machine Learning följer de här stegen:

1. Utveckla maskininlärning utbildning skript i Python.
1. Skapa och konfigurera eller bifoga en befintlig beräkningsmål.
1. Skicka utbildningsskript till beräkningsmål.
1. Granska resultaten för att hitta den bästa modellen.
1. Registrera modellen i registret för modellen.
1. Distribuera modellen.

> [!IMPORTANT]
> Utbildning skriptet inte är kopplat till en specifik beräkningsmål. Du kan träna från början på den lokala datorn och sedan växla beräkningsmål utan att behöva skriva om skriptet utbildning.

> [!TIP]
> När du kopplar ett beräkningsmål med din arbetsyta, antingen genom att skapa hanterade compute eller koppla befintliga beräkning, måste du ange ett namn som din databearbetning. Detta bör vara mellan 2 och 16 tecken.

Växla mellan en beräkningsmål innebär att du skapar en [körningskonfigurationen](concept-azure-machine-learning-architecture.md#run-configuration). Körningskonfigurationen definierar hur du kör skriptet på beräkningsmål.

## <a name="training-scripts"></a>Utbildningsskript

När du startar en körning för utbildning, skapas en ögonblicksbild av den katalog som innehåller dina utbildningsskript och skickas till beräkningsmål. Mer information finns i [ögonblicksbilder](concept-azure-machine-learning-architecture.md#snapshot).

## <a id="local"></a>Lokal dator

När utbildning lokalt, använder du SDK: N för att skicka utbildning-åtgärd. Du kan träna med hjälp av en miljö som hanteras av användaren eller systemet.

### <a name="user-managed-environment"></a>Användarhanterade miljö

I en miljö med användarhanterade är du ansvarig för att säkerställa att alla nödvändiga paket är tillgängliga i Python-miljö som du väljer att köra skriptet. Följande kodavsnitt är ett exempel på hur du konfigurerar utbildning för en användarhanterade miljö:

```python
from azureml.core.runconfig import RunConfiguration

# Editing a run configuration property on-fly.
run_config_user_managed = RunConfiguration()

run_config_user_managed.environment.python.user_managed_dependencies = True

# You can choose a specific Python environment by pointing to a Python path 
#run_config.environment.python.interpreter_path = '/home/ninghai/miniconda3/envs/sdk2/bin/python'
```

  
### <a name="system-managed-environment"></a>System-hanterad miljö

Hanteras av datorn miljöer är beroende av conda att hantera beroenden. Conda skapar en fil med namnet `conda_dependencies.yml` som innehåller en lista över beroenden. Sedan kan du be systemet för att skapa en ny conda-miljö och kör skripten i den. Hanteras av datorn miljöer kan vara återanvända senare, så länge som den `conda_dependencies.yml` filer förblir oförändrat. 

Den första konfigurationen upp av en ny miljö kan ta flera minuter att slutföra beroende på storleken på de nödvändiga beroendena. Följande kodfragment visar hur du skapar en miljö som hanteras av datorn som är beroende av scikit-Läs:

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

run_config_system_managed = RunConfiguration()

run_config_system_managed.environment.python.user_managed_dependencies = False
run_config_system_managed.auto_prepare_environment = True

# Specify conda dependencies with scikit-learn

run_config_system_managed.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
```

## <a id="amlcompute"></a>Azure Machine Learning-beräkning

Beräkning av Azure Machine Learning är en beräkningsinfrastruktur för hanterade som används att enkelt skapa en - till Multi-Factor - node beräkning. Den har skapats __inom din arbetsyteregion__ och är en resurs som kan delas med andra användare i din arbetsyta. Det kan skalas automatiskt när ett jobb skickas och kan placeras i en Azure-nätverk. Den kan köras i en __behållare miljö__, paketera din modell beroenden i en Docker-behållare.

Du kan använda beräkning av Azure Machine Learning för att distribuera träningsprocess över ett kluster med CPU eller GPU-beräkningsnoder i molnet. Mer information om de storlekar som innehåller GPU: er finns i den [GPU-optimerad storlekar för virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu) dokumentation.

> [!NOTE]
> Beräkning av Azure Machine Learning har standardgränser på t.ex. antalet kärnor som kan allokeras. Mer information finns i den [hantera och begära kvoter för Azure-resurser](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas) dokumentet.

Du kan skapa beräkning av Azure Machine Learning på begäran när du schemalägger en körning eller som en beständig resurs.

### <a name="run-based-creation"></a>Kör-baserade skapas

Du kan skapa en beräkning av Azure Machine Learning som beräkningsmål vid körning. I det här fallet beräkningen som skapas automatiskt för din körning, skalar upp max_nodes som du anger i kör config, och sedan __tas bort automatiskt__ när körningen har slutförts.

> [!IMPORTANT]
> Kör-baserade skapandet av beräkning av Azure Machine Learning är för närvarande i förhandsversion. Använd inte kör-baserade skapas om du använder finjustering av Hyperparametrar eller automatiserade Machine Learning. Om du vill använda finjustering av Hyperparametrar eller automatiserade Machine Learning kan skapa Azure Machine Learning-beräkning innan du skickar en körning.

```python
from azureml.core.compute import ComputeTarget, AmlCompute

#Let us first list the supported VM families for Azure Machine Learning Compute
AmlCompute.supported_vmsizes()

from azureml.core.runconfig import RunConfiguration

# create a new runconfig object
run_config = RunConfiguration()

# signal that you want to use AmlCompute to execute script.
run_config.target = "amlcompute"

# AmlCompute will be created in the same region as workspace. Set vm size for AmlCompute from the list returned above
run_config.amlcompute.vm_size = 'STANDARD_D2_V2'

```

### <a name="persistent-compute-basic"></a>Beständiga beräkning (grundläggande)

En beständig Azure beräkning av Machine Learning kan återanvändas i flera jobb. Det kan också delas med andra användare i arbetsytan och sparas mellan jobb.

Om du vill skapa en beständig beräkning av Azure Machine Learning-resurs som du anger den `vm_size` och `max_nodes` parametrar. Azure Machine Learning använder smarta standardvärden för resten av parametrarna.  Beräkningen är till exempel ange att automatiskt skala ned till noll noder när de inte används och att skapa dedikerade virtuella datorer att köra dina jobb efter behov. 

* **vm_size**: VM-serie med noder som skapats av beräkning av Azure Machine Learning.
* **max_nodes**: Högsta antalet noder för automatisk skalning i när du kör ett jobb på beräkning av Azure Machine Learning.

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                           max_nodes=4)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)

```

### <a name="persistent-compute-advanced"></a>Beständiga beräkning (Avancerat)

Du kan också konfigurera flera avancerade egenskaper när du skapar beräkning av Azure Machine Learning.  Här kan du skapa ett beständiga kluster med fast storlek eller i ett befintligt virtuellt Azure-nätverk i din prenumeration.

Förutom `vm_size` och `max_nodes`, du kan använda följande egenskaper:

* **min_nodes**: Lägsta antal noder (standard 0 noder) att skala ned medan köra ett jobb på beräkning av Azure Machine Learning.
* **vm_priority**: Välj mellan ”dedikerad” (standard) och ”lowpriority” virtuella datorer när du skapar beräkning av Azure Machine Learning. Virtuella datorer med låg prioritet använda Azures överflödig kapacitet och är därför billigare men riskerar din körning som återtas.
* **idle_seconds_before_scaledown**: Inaktiv tid (som standard 120 sekunder) att vänta efter körning är klart innan du automatisk skalning till min_nodes.
* **vnet_resourcegroup_name**: Resursgruppen för den __befintliga__ virtuellt nätverk. Beräkning av Azure Machine Learning har skapats i det här virtuella nätverket.
* **vnet_name**: Namn på virtuellt nätverk. Det virtuella nätverket måste vara i samma region som din Azure Machine Learning-arbetsyta.
* **subnet_name**: Namnet på undernätet i det virtuella nätverket. Azure beräkning av Machine Learning-resurser kommer att tilldelas IP-adresser från det här intervallet i undernätet.

> [!TIP]
> När du skapar en beständig beräkning av Azure Machine Learning-resurs har också möjlighet att uppdatera dess egenskaper, till exempel min_nodes eller max_nodes. Du bara anropa den `update()` funktionen för den.

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
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

Azure Machine Learning har även stöd för att få ut dina egna beräkningsresurs och kopplar den till din arbetsyta. En sådan resurstypen är ett godtyckligt fjärransluten virtuell dator så länge den kan nås från Azure Machine Learning-tjänsten. Det kan vara en Azure-dator eller en fjärransluten server i din organisation eller lokalt. Mer specifikt, beroende på IP-adress och autentiseringsuppgifter (användarnamn/lösenord eller SSH-nyckel) som du kan använda alla tillgängliga virtuella datorer för fjärranslutna körs.
Du kan använda en inbyggd system conda-miljö, en redan befintlig Python-miljö eller en Docker-behållare. Körning med Docker-behållare kräver att du har Docker-motorn körs på den virtuella datorn. Den här funktionen är särskilt användbart när du vill att en mer flexibel, molnbaserad utveckling/experimentmiljön än den lokala datorn.

> [!TIP]
> Vi rekommenderar att du använder Data Science Virtual Machine som virtuell Azure-dator med det här scenariot. Det är en förkonfigurerad datavetenskap och AI utvecklingsmiljö i Azure ett granskad urval av verktyg och ramverk för hela livscykeln för ML-utveckling. Mer information om hur du använder Data Science Virtual Machine med Azure Machine Learning finns det [konfigurera en utvecklingsmiljö](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-environment#dsvm) dokumentet.

> [!WARNING]
> Azure Machine Learning har endast stöd för virtuella datorer som kör Ubuntu. När skapar en virtuell dator eller välja en befintlig, du måste välja en som använder Ubuntu.

Följande steg använder SDK för att konfigurera en virtuell dator på datavetenskap (DSVM) som utbildning mål:

1. Om du vill koppla en befintlig virtuell dator som en beräkningsmål, måste du ange det fullständigt kvalificerade domännamnet, inloggningsnamn och lösenord för den virtuella datorn.  I det här exemplet ersätter ```<fqdn>``` med offentliga fullständigt kvalificerade domännamnet för den virtuella datorn eller den offentliga IP-adressen. Ersätt ```<username>``` och ```<password>``` med SSH-användare och lösenord för den virtuella datorn:

    ```python
    from azureml.core.compute import RemoteCompute, ComputeTarget
    
    # Create compute config.
    attach_config = RemoteCompute.attach_configuration(address = "ipaddress",
                                                       ssh_port=22,
                                                       username='<username>',
                                                       password="<password>")
    # If using SSH instead of a password, use this:
    #                                                  ssh_port=22,
    #                                                   username='<username>',
    #                                                   password=None,
    #                                                   private_key_file="path-to-file",
    #                                                   private_key_passphrase="passphrase")

    # Attach the compute
    compute = ComputeTarget.attach(ws, "attach-dsvm", attach_config)

    compute.wait_for_completion(show_output=True)
    ```

1. Skapa en konfiguration för beräkningsmål DSVM. Docker och conda används för att skapa och konfigurera miljön på DSVM:

    ```python
    from azureml.core.runconfig import RunConfiguration
    from azureml.core.conda_dependencies import CondaDependencies

    # Load the "cpu-dsvm.runconfig" file (created by the above attach operation) in memory
    run_config = RunConfiguration(framework = "python")

    # Set compute target to the Linux DSVM
    run_config.target = compute_target_name

    # Use Docker in the remote VM
    run_config.environment.docker.enabled = True

    # Use CPU base image
    # If you want to use GPU in DSVM, you must also use GPU base Docker image azureml.core.runconfig.DEFAULT_GPU_IMAGE
    run_config.environment.docker.base_image = azureml.core.runconfig.DEFAULT_CPU_IMAGE
    print('Base Docker image is:', run_config.environment.docker.base_image)

    # Ask system to provision a new one based on the conda_dependencies.yml file
    run_config.environment.python.user_managed_dependencies = False

    # Prepare the Docker and conda environment automatically when used the first time.
    run_config.prepare_environment = True

    # specify CondaDependencies obj
    run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])

    ```

## <a id="databricks"></a>Azure Databricks

Azure Databricks är en Apache Spark-baserad miljö i Azure-molnet. Det kan användas som ett beräkningsmål vid utbildning av modeller med en Azure Machine Learning-pipeline.

> [!IMPORTANT]
> En Azure Databricks beräkningsmål kan bara användas i en Machine Learning-pipeline.
>
> Du måste skapa en Azure Databricks-arbetsyta innan du använder den för att träna din modell. Om du vill skapa dessa resurs den [kör ett Spark-jobb på Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) dokumentet.

Om du vill koppla Azure Databricks som beräkningsmål, måste du använder Azure Machine Learning SDK och ange följande information:

* __Beräkningsnamn__: Namnet som du vill tilldela till den här beräkningsresursen.
* __Databricks Arbetsytenamn__: Namnet på Azure Databricks-arbetsytan.
* __Åtkomsttoken__: Den åtkomst-token som används för att autentisera till Azure Databricks. Generera en åtkomsttoken genom att se den [autentisering](https://docs.azuredatabricks.net/api/latest/authentication.html) dokumentet.

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

    # Create attach config
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

Azure Data Lake Analytics är en analysplattform med stordata i Azure-molnet. Det kan användas som ett beräkningsmål vid utbildning av modeller med en Azure Machine Learning-pipeline.

> [!IMPORTANT]
> En Azure Data Lake Analytics beräkningsmål kan bara användas i en Machine Learning-pipeline.
>
> Du måste skapa ett Azure Data Lake Analytics-konto innan du använder den för att träna din modell. Om du vill skapa den här resursen, den [Kom igång med Azure Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal) dokumentet.

Om du vill koppla Data Lake Analytics som beräkningsmål du använder Azure Machine Learning SDK och ange följande information:

* __Beräkningsnamn__: Namnet som du vill tilldela till den här beräkningsresursen.
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
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group = adla_resource_group,
                                                     account_name = adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
             ws,
             adla_compute_name,
             attach_config
         )
    
    adla_compute.wait_for_completion(True)
```

> [!TIP]
> Azure Machine Learning pipelines fungerar bara med data som lagras i datalagret standard för Data Lake Analytics-kontot. Om data som du vill arbeta med är i en icke-standard-store kan du använda en [ `DataTransferStep` ](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) att kopiera data innan utbildning.

## <a id="hdinsight"></a>Azure HDInsight 

HDInsight är en populär plattform för stordataanalys. Den innehåller Apache Spark, som kan användas för att träna din modell.

> [!IMPORTANT]
> Du måste skapa HDInsight-klustret innan du använder den för att träna din modell. Om du vill skapa en Apache Spark på HDInsight-kluster finns i den [skapa ett Spark-kluster i HDInsight](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql) dokumentet.
>
> När du skapar klustret, måste du ange ett SSH-användarnamn och lösenord. Notera dessa värden som du behöver dem när du använder HDInsight som en beräkningsmål.
>
> När klustret har skapats kan den har ett fullständigt kvalificerat domännamn (FQDN) för `<clustername>.azurehdinsight.net`, där `<clustername>` är det namn du angav för klustret. Du behöver den här adressen (eller den offentliga IP-adressen för klustret) för att använda det som ett beräkningsmål

Om du vill konfigurera HDInsight som en beräkningsmål, måste du ange det fullständigt kvalificerade domännamnet, klustrets inloggningsnamn och lösenord för HDInsight-klustret. I följande exempel använder SDK för att koppla ett kluster till din arbetsyta. I det här exemplet ersätter `<fqdn>` med offentliga fullständigt kvalificerade domännamnet för klustret eller offentliga IP-adress. Ersätt `<username>` och `<password>` med SSH-användare och lösenord för klustret:

> [!NOTE]
> Om du vill hitta det fullständiga Domännamnet för klustret, öppnar du Azure portal och välj ditt HDInsight-kluster. Från den __översikt__ avsnittet FQDN är en del av den __URL__ posten. Ta bara bort det `https://` från början av värdet.
>
> ![Skärmbild av HDInsight-kluster översikt med URL-posten markerat](./media/how-to-set-up-training-targets/hdinsight-overview.png)

```python
from azureml.core.compute import HDInsightCompute, ComputeTarget

try:
    # Attaches a HDInsight cluster as a compute target.
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

# Configure HDInsight run
# load the runconfig object from the "myhdi.runconfig" file generated by the attach operaton above.
run_config = RunConfiguration.load(project_object = project, run_config_name = 'myhdi')

# ask system to prepare the conda environment automatically when used for the first time
run_config.auto_prepare_environment = True
```

## <a name="submit-training-run"></a>Skicka utbildning som kör

Det finns två sätt att skicka en utbildning körning:

* Skicka en `ScriptRunConfig` objekt.
* Skicka en `Pipeline` objekt.

> [!IMPORTANT]
> Azure Databricks och Azure-Datalake-Analytics compute mål kan bara användas i en pipeline.
> Den lokala beräkningsmål kan inte användas i en Pipeline.

### <a name="submit-using-scriptrunconfig"></a>Skicka in via `ScriptRunConfig`

Mönstret kod för att skicka ett utbildnings körs med hjälp av `ScriptRunConfig` är detsamma oavsett beräkningsmål:

* Skapa en `ScriptRunConfig` objekt med hjälp av körningskonfigurationen för beräkningsmål.
* Skicka in körningen.
* Vänta tills den kör för att slutföra.

I följande exempel används konfigurationen för hanteras av datorn lokala beräkningsmål skapade tidigare i det här dokumentet:

```python
src = ScriptRunConfig(source_directory = script_folder, script = 'train.py', run_config = run_config_system_managed)
run = exp.submit(src)
run.wait_for_completion(show_output = True)
```


### <a name="submit-using-a-pipeline"></a>Skicka in via en pipeline

Koden mönstret för att skicka ett utbildnings körs med hjälp av en pipeline är detsamma oavsett beräkningsmål:

* Lägga till ett steg i pipeline för beräkningsresursen.
* Skicka en körning med hjälp av pipelinen.
* Vänta tills den kör för att slutföra.

I följande exempel används Azure Databricks-beräkningsmål som skapats tidigare i det här dokumentet:

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
# list of steps to run
steps = [dbStep]
pipeline = Pipeline(workspace=ws, steps=steps)
pipeline_run = Experiment(ws, 'Demo_experiment').submit(pipeline)
pipeline_run.wait_for_completion()
```

Mer information om machine learning pipelines finns i den [Pipelines och Azure Machine Learning](concept-ml-pipelines.md) dokumentet.

Till exempel Jupyter-anteckningsböcker som visar utbildning med en pipeline finns i [ https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline ](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline).

## <a name="view-and-set-up-compute-using-the-azure-portal"></a>Visa och Ställ in beräkning med Azure portal

Du kan visa vad compute mål är kopplade till din arbetsyta från Azure-portalen. Gå till listan genom att använda följande steg:

1. Gå till den [Azure-portalen](https://portal.azure.com) och navigera till din arbetsyta.
2. Klicka på den __Compute__ länka den __program__ avsnittet.

    ![Visa beräkning fliken](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)

### <a name="create-a-compute-target"></a>Skapa ett beräkningsmål

Följ stegen för att visa en lista över beräkningsmål och Använd sedan följande steg för att skapa ett beräkningsmål:

1. Klicka på den __+__ logga att lägga till ett beräkningsmål.

    ![Lägg till beräkning ](./media/how-to-set-up-training-targets/add-compute-target.png)

1. Ange ett namn för beräkningsmål
1. Välj **beräkning av Machine Learning** som typ av beräkning som ska användas för __utbildning__

    > [!IMPORTANT]
    > Du kan bara skapa beräkning av Azure Machine Learning som hanterade beräkningarna för träning

1. Fyll i formuläret krävs, särskilt i VM-familjen och det högsta antalet noder ska användas för att skapa beräkningar 
1. Välj __Skapa__
1. Du kan visa status för åtgärden för att skapa genom att välja beräkningsmål i listan

    ![Visa lista med beräkning](./media/how-to-set-up-training-targets/View_list.png)

1. Därefter visas information om beräkningsmål.

    ![Visa information](./media/how-to-set-up-training-targets/compute-target-details.png)

1. Nu kan du skicka en körning mot dessa mål som beskrivs ovan


### <a name="reuse-existing-compute-in-your-workspace"></a>Återanvända befintliga beräkning i din arbetsyta

Följ stegen för att visa en lista över beräkningsmål och klicka sedan återanvända beräkningsmål med hjälp av följande steg:

1. Klicka på den **+** logga att lägga till ett beräkningsmål
2. Ange ett namn för beräkningsmål
3. Välj den typ av beräkningsresurser kan bifoga för __utbildning__

    > [!IMPORTANT]
    > Inte alla beräkningsresurser typer kan kopplas med hjälp av portalen.
    > De typer som kan kopplas till utbildning finns för närvarande:
    > 
    > * Fjärransluten virtuell dator
    > * Databricks
    > * Data Lake Analytics
    > * HDInsight

1. Fyll i formuläret krävs

    > [!NOTE]
    > Microsoft rekommenderar att du använder SSH-nycklar som de är säkrare än lösenord. Lösenord är sårbara för råstyrkeattacker mot attacker, medan SSH-nycklar är beroende av kryptografiska signaturer. Information om hur du skapar SSH-nycklar för användning med Azure virtuella datorer finns i följande dokument:
    >
    > * [Skapa och använda SSH-nycklar på Linux eller macOS]( https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Skapa och använda SSH-nycklar i Windows]( https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. Välj Koppla
1. Du kan visa status för åtgärden koppla genom att välja beräkningsmål i listan
1. Nu kan du skicka en körning mot dessa mål som beskrivs ovan

## <a name="examples"></a>Exempel
Referera till anteckningsböcker på följande platser:
* [How-to-till-användning – azureml/utbildning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)

* [självstudier/img-klassificering – del 1 – training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nästa steg

* [Azure Machine Learning SDK-referens](https://aka.ms/aml-sdk)
* [Självstudiekurs: Träna en modell](tutorial-train-models-with-aml.md)
* [Var du vill distribuera modeller](how-to-deploy-and-where.md)
* [Skapa machine learning pipelines med Azure Machine Learning-tjänsten](concept-ml-pipelines.md)
