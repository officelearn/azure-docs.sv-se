---
title: Konfigurera beräkningsmål för modellträning med Azure Machine Learning-tjänsten | Microsoft Docs
description: Lär dig mer om att välja och konfigurera utbildning miljöer (beräkningsmål) används för att träna dina maskininlärningsmodeller. Azure Machine Learning-tjänsten kan du enkelt växla miljöer för utbildning. Starta utbildning lokalt och om du vill skala ut kan växla till en molnbaserad beräkningsmål.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.reviewer: larryfr
manager: cgronlun
ms.service: machine-learning
ms.component: core
ms.topic: article
ms.date: 09/24/2018
ms.openlocfilehash: 99d85c769b876baa360628bfc20ad5b1563a0a3f
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/03/2018
ms.locfileid: "48237338"
---
# <a name="select-and-use-a-compute-target-to-train-your-model"></a>Använd ett beräkningsmål träna din modell

Med Azure Machine Learning-tjänsten kan du träna din modell i flera olika miljöer. Dessa miljöer, kallas __beräkningsmål__, kan vara lokala eller i molnet. I det här dokumentet lär du dig att stöds beräkningsmål och hur de används.

Beräkningsmål är den resurs som kör dina utbildningsskript eller värdar modellen när den distribueras som en webbtjänst. De kan skapas och hanteras med hjälp av Azure Machine Learning SDK eller CLI. Om du har beräkningsmål som har skapats av en annan process (till exempel Azure portal eller Azure CLI) kan använda du dem genom att koppla dem till din arbetsyta för Azure Machine Learning-tjänsten.

Du kan börja med lokala körs på din dator och sedan skala uppåt och utåt till andra miljöer, till exempel remote Data Science virtuella datorer med GPU- eller Azure Batch AI. 

## <a name="supported-compute-targets"></a>Stöds beräkningsmål

Azure Machine Learning-tjänsten stöder följande beräkningsmål:

|Beräkningsmål| GPU-acceleration | Automatiserad finjustering av hyperparametrar | Vald automatiserade modell | Kan användas i pipelines|
|----|:----:|:----:|:----:|:----:|
|[Lokal dator](#local)| Kanske | &nbsp; | ✓ | &nbsp; |
|[Virtuell dator för datavetenskap (DSVM)](#dsvm) | ✓ | ✓ | ✓ | ✓ |
|[Azure Batch AI](#batch)| ✓ | ✓ | ✓ | ✓ | ✓ |
|[Azure HDInsight](#hdinsight)| &nbsp; | &nbsp; | &nbsp; | ✓ |

__[Azure Container Instances (ACI)](#aci)__  kan också användas för att träna modeller. Det är en serverlös molntjänst som är kostnadseffektiv och enkel att skapa och arbeta med. ACI har inte stöd för GPU-acceleration, automatiserade hyper parametern inställning, eller automatiserade vald modell. Det kan dessutom inte användas i en pipeline.

Viktiga skillnaderna mellan beräkningsmål är:
* __GPU-acceleration__: GPU: er är tillgängliga med den virtuella datorn för datavetenskap och Azure Batch AI. Du kan ha åtkomst till en GPU på din lokala dator, beroende på maskinvara, drivrutiner och ramverk som är installerade.
* __Automatiserad finjustering av hyperparametrar__: Azure Machine Learning automatiserad finjustering optimering hjälper dig att hitta den bästa hyperparametrar för din modell.
* __Automatiserad vald modell__: Azure Machine Learning-tjänsten kan smart rekommenderar algoritmen och finjustering markerad när du skapar en modell. Vald automatiserade modell hjälper dig att Konvergera till en modell med hög kvalitet snabbare än att försöka manuellt olika kombinationer. Mer information finns i den [självstudie: automatiskt tränar en modell för klassificering med Azure automatiserad Machine Learning](tutorial-auto-train-models.md) dokumentet.
* __Pipelines__: Azure Machine Learning-tjänsten gör att du kan kombinera olika uppgifter, till exempel inlärning och distribuering i en pipeline. Pipelines kan vara körde parallellt eller i följd och tillhandahålla en tillförlitlig automation-metod. Mer information finns i den [skapa machine learning pipelines med Azure Machine Learning-tjänsten](concept-ml-pipelines.md) dokumentet.

Du kan använda SDK för Azure Machine Learning, Azure CLI eller Azure-portalen för att skapa beräkningsmål. Du kan också använda befintliga beräkningsmål genom att lägga till (koppla) dem till din arbetsyta.

> [!IMPORTANT]
> Du kan inte koppla en befintlig instans av Azure-behållare till din arbetsyta. I stället måste du skapa en ny instans.
>
> Du kan inte skapa ett Azure HDInsight-kluster i en arbetsyta. I stället måste du koppla ett befintligt kluster.

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

Växla mellan en beräkningsmål innebär att du skapar en [körningskonfigurationen](concept-azure-machine-learning-architecture.md#run-configuration). Körningskonfigurationen definierar hur du kör skriptet på beräkningsmål.

## <a name="training-scripts"></a>Utbildningsskript

När du startar en utbildning körning har hela katalogen som innehåller dina utbildningsskript skickats. En ögonblicksbild skapas och skickas till beräkningsmål. Mer information finns i [ögonblicksbilder](concept-azure-machine-learning-architecture.md#snapshot).

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

En Jupyter-anteckningsbok som visar utbildning i en miljö med användarhanterade, se [ https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/02.train-on-local/02.train-on-local.ipynb ](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/02.train-on-local/02.train-on-local.ipynb).
  
### <a name="system-managed-environment"></a>System-hanterad miljö

Hanteras av datorn miljöer är beroende av conda att hantera beroenden. Conda skapar en fil med namnet `conda_dependencies.yml` som innehåller en lista över beroenden. Sedan kan du be systemet för att skapa en ny conda-miljö och kör skripten i den. Hanteras av datorn miljöer kan vara återanvända senare, så länge som den `conda_dependencies.yml` filer förblir oförändrat. 

Den första konfigurationen upp av en ny miljö kan ta flera minuter att slutföra beroende på storleken på de nödvändiga beroendena. Följande kodfragment visar hur du skapar en miljö som hanteras av datorn som är beroende av scikit-Läs:

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

run_config_system_managed = RunConfiguration()

run_config_system_managed.environment.python.user_managed_dependencies = False
run_config_system_managed.prepare_environment = True

# Specify conda dependencies with scikit-learn

run_config_system_managed.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
```

En Jupyter-anteckningsbok som visar utbildning i en miljö som hanteras av datorn, se [ https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/02.train-on-local/02.train-on-local.ipynb ](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/02.train-on-local/02.train-on-local.ipynb).

## <a id="dsvm"></a>Virtuell dator för datavetenskap

Den lokala datorn kanske inte har beräkning eller GPU-resurser som krävs för att träna modellen. I så fall kan du kan skala upp eller skala ut träningsprocess genom att lägga till ytterligare beräkningsmål, till exempel en Data Science Virtual Machines (DSVM).

> [!WARNING]
> Azure Machine Learning har endast stöd för virtuella datorer som kör Ubuntu. När skapar en virtuell dator eller välja en befintlig, du måste välja en som använder Ubuntu.

Följande steg använder SDK för att konfigurera en virtuell dator på datavetenskap (DSVM) som utbildning mål:

1. Skapa eller koppla en virtuell dator
    
    * Om du vill skapa en ny DSVM, kontrollera först för att se om du har en DSVM med samma namn, om inte skapa en ny virtuell dator:
    
        ```python
        from azureml.core.compute import DsvmCompute
        from azureml.core.compute_target import ComputeTargetException

        compute_target_name = 'mydsvm'

        try:
            dsvm_compute = DsvmCompute(workspace = ws, name = compute_target_name)
            print('found existing:', dsvm_compute.name)
        except ComputeTargetException:
            print('creating new.')
            dsvm_config = DsvmCompute.provisioning_configuration(vm_size = "Standard_D2_v2")
            dsvm_compute = DsvmCompute.create(ws, name = compute_target_name, provisioning_configuration = dsvm_config)
            dsvm_compute.wait_for_completion(show_output = True)
        ```
    * Om du vill koppla en befintlig virtuell dator som en beräkningsmål, måste du ange det fullständigt kvalificerade domännamnet, inloggningsnamn och lösenord för den virtuella datorn.  I det här exemplet ersätter ```<fqdn>``` med offentliga fullständigt kvalificerade domännamnet för den virtuella datorn eller den offentliga IP-adressen. Ersätt ```<username>``` och ```<password>``` med SSH-användare och lösenord för den virtuella datorn:

        ```python
        from azureml.core.compute import RemoteCompute

        dsvm_compute = RemoteCompute.attach(ws,
                                        name="attach-dsvm",
                                        username='<username>',
                                        address="<fqdn>",
                                        ssh_port=22,
                                        password="<password>")

        dsvm_compute.wait_for_completion(show_output=True)
    
   It takes around 5 minutes to create the DSVM instance.

1. Create a configuration for the DSVM compute target. Docker and conda are used to create and configure the training environment on DSVM:

    ```python
    from azureml.core.runconfig import RunConfiguration
    from azureml.core.conda_dependencies import CondaDependencies

    # Load the "cpu-dsvm.runconfig" file (created by the above attach operation) in memory
    run_config = RunConfiguration(framework = "python")

    # Set compute target to the Linux DSVM
    run_config.target = compute_target_name

    # Use Docker in the remote VM
    run_config.environment.docker.enabled = True

    # Use CPU base image from DockerHub
    run_config.environment.docker.base_image = azureml.core.runconfig.DEFAULT_CPU_IMAGE
    print('Base Docker image is:', run_config.environment.docker.base_image)

    # Ask system to provision a new one based on the conda_dependencies.yml file
    run_config.environment.python.user_managed_dependencies = False

    # Prepare the Docker and conda environment automatically when used the first time.
    run_config.prepare_environment = True

    # specify CondaDependencies obj
    run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])

    ```

1. Om du vill ta bort beräkningsresurser när du är klar, Använd följande kod:

    ```python
    dsvm_compute.delete()
    ```

En Jupyter-anteckningsbok som visar utbildning på en virtuell dator för datavetenskap, se [ https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/04.train-on-remote-vm/04.train-on-remote-vm.ipynb ](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/04.train-on-remote-vm/04.train-on-remote-vm.ipynb).

## <a id="batch"></a>Azure Batch AI

Om det tar lång tid att träna din modell kan du använda Azure Batch AI för att distribuera utbildningen i ett kluster med beräkningsresurser i molnet. Batch AI kan också konfigureras för att aktivera en GPU-resurs.

I följande exempel söker efter ett befintligt Batch AI-kluster efter namn. Om en hittas, skapas den:

```python
from azureml.core.compute import BatchAiCompute
from azureml.core.compute import ComputeTarget

# choose a name for your cluster
batchai_cluster_name = ws.name + "cpu"

found = False
# see if this compute target already exists in the workspace
for ct in ws.compute_targets():
    print(ct.name, ct.type)
    if (ct.name == batchai_cluster_name and ct.type == 'BatchAI'):
        found = True
        print('found compute target. just use it.')
        compute_target = ct
        break
        
if not found:
    print('creating a new compute target...')
    provisioning_config = BatchAiCompute.provisioning_configuration(vm_size = "STANDARD_D2_V2", # for GPU, use "STANDARD_NC6"
                                                                #vm_priority = 'lowpriority', # optional
                                                                autoscale_enabled = True,
                                                                cluster_min_nodes = 1, 
                                                                cluster_max_nodes = 4)

    # create the cluster
    compute_target = ComputeTarget.create(ws,batchai_cluster_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current BatchAI cluster status, use the 'status' property    
    print(compute_target.status.serialize())
```

Om du vill koppla ett befintligt Batch AI-kluster som en beräkningsmål, måste du ange Azure-resurs-ID. Hämta resurs-ID från Azure-portalen med följande steg:
1. Sök efter `Batch AI` tjänsten under **alla tjänster**
1. Klicka på arbetsytans namn som klustret tillhör
1. Välj klustret
1. Klicka på **egenskaper**
1. Kopiera den **ID**

I följande exempel använder SDK för att koppla ett kluster till din arbetsyta. I det här exemplet ersätter `<name>` med ett namn för beräkningen. Namnet behöver inte matcha namnet på klustret. Ersätt `<resource-id>` med Azure-resursen ID som beskrivs ovan:

```python
from azureml.core.compute import BatchAiCompute
BatchAiCompute.attach(workspace=ws,
                      name=<name>,
                      resource_id=<resource-id>)
```

Du kan också kontrollera Batch AI-kluster eller status med hjälp av följande Azure CLI-kommandon:

- Kontrollera statusen för klustret. Du kan se hur många noder körs med hjälp av `az batchai cluster list`.
- Kontrollera jobbstatus. Du kan se hur många jobb körs med hjälp av `az batchai job list`.

Det tar cirka 5 minuter för att skapa Batch AI-kluster.

En Jupyter-anteckningsbok som visar utbildning i ett Batch AI-kluster, se [ https://github.com/Azure/MachineLearningNotebooks/blob/master/training/03.train-hyperparameter-tune-deploy-with-tensorflow/03.train-hyperparameter-tune-deploy-with-tensorflow.ipynb ](https://github.com/Azure/MachineLearningNotebooks/blob/master/training/03.train-hyperparameter-tune-deploy-with-tensorflow/03.train-hyperparameter-tune-deploy-with-tensorflow.ipynb).

## <a name='aci'></a>Azure Container-instans (ACI)

Azure Container Instances är isolerad behållare som har snabbare starttider och kräver inte användaren att hantera några virtuella datorer. Azure Machine Learning-tjänsten använder Linux-behållare som är tillgängliga i usavästra, eastus, europavästra, europanorra, västra USA 2 och southeastasia regioner. Mer information finns i [regiontillgänglighet](https://docs.microsoft.com/azure/container-instances/container-instances-quotas#region-availability). 

I följande exempel visas hur du använder SDK för att skapa ett beräkningsmål för ACI och träna en modell: 

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

# create a new runconfig object
run_config = RunConfiguration()

# signal that you want to use ACI to run script.
run_config.target = "containerinstance"

# ACI container group is only supported in certain regions, which can be different than the region the Workspace is in.
run_config.container_instance.region = 'eastus'

# set the ACI CPU and Memory 
run_config.container_instance.cpu_cores = 1
run_config.container_instance.memory_gb = 2

# enable Docker 
run_config.environment.docker.enabled = True

# set Docker base image to the default CPU-based image
run_config.environment.docker.base_image = azureml.core.runconfig.DEFAULT_CPU_IMAGE
#run_config.environment.docker.base_image = 'microsoft/mmlspark:plus-0.9.9'

# use conda_dependencies.yml to create a conda environment in the Docker image
run_config.environment.python.user_managed_dependencies = False

# auto-prepare the Docker image when used for the first time (if it is not already prepared)
run_config.auto_prepare_environment = True

# specify CondaDependencies obj
run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
```

Det kan ta från några sekunder till några minuter att skapa ett beräkningsmål för ACI.

En Jupyter-anteckningsbok som visar utbildning om Azure Container Instance, se [ https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/03.train-on-aci/03.train-on-aci.ipynb ](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/03.train-on-aci/03.train-on-aci.ipynb).

## <a id="hdinsight"></a>Koppla ett HDInsight-kluster 

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
from azureml.core.compute import HDInsightCompute

try:
    # Attaches a HDInsight cluster as a compute target.
    HDInsightCompute.attach(ws,name = "myhdi",
                            address = "<fqdn>",
                            username = "<username>",
                            password = "<password>")
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
    
Koden för att skicka en utbildning körning är detsamma oavsett beräkningsmål:

* Skapa en `ScriptRunConfig` objekt med hjälp av körningskonfigurationen för beräkningsmål.
* Skicka in körningen.
* Vänta tills den kör för att slutföra.

I följande exempel används konfigurationen för hanteras av datorn lokala beräkningsmål skapade tidigare i det här dokumentet:

```pyghon
src = ScriptRunConfig(source_directory = script_folder, script = 'train.py', run_config = run_config_system_managed)
run = exp.submit(src)
run.wait_for_completion(show_output = True)
```

En Jupyter-anteckningsbok som visar utbildning med Spark på HDInsight, se [ https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/05.train-in-spark/05.train-in-spark.ipynb ](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/05.train-in-spark/05.train-in-spark.ipynb).

## <a name="view-and-set-up-compute-using-the-azure-portal"></a>Visa och Ställ in beräkning med Azure portal

Du kan visa vad compute mål är kopplade till din arbetsyta från Azure-portalen. Gå till listan genom att använda följande steg:

1. Gå till den [Azure-portalen](https://portal.azure.com) och navigera till din arbetsyta.
2. Klicka på den __Compute__ länka den __program__ avsnittet.

    ![Visa beräkning fliken](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)

### <a name="create-a-compute-target"></a>Skapa ett beräkningsmål

Följ stegen för att visa en lista över beräkningsmål och Använd sedan följande steg för att skapa ett beräkningsmål:

1. Klicka på den __+__ logga att lägga till ett beräkningsmål.

    ![Lägg till compute ](./media/how-to-set-up-training-targets/add-compute-target.png)

1. Ange ett namn för beräkningsmål.
1. Välj den typ av beräkningsresurser kan bifoga för __utbildning__. 
1. Välj __Skapa ny__ och fylla i nödvändig information. 
1. Välj __Skapa__
1. Du kan visa statusen för att skapa genom att välja beräkningsmål i listan.

    ![Visa beräkning lista](./media/how-to-set-up-training-targets/View_list.png) därefter visas information för den beräkningen.
    ![Visa information](./media/how-to-set-up-training-targets/vm_view.PNG)
1. Du kan nu skicka en körning mot dessa mål som beskrivs ovan.

### <a name="reuse-existing-compute-in-your-workspace"></a>Återanvända befintliga beräkning i din arbetsyta

Följ stegen för att visa en lista över beräkningsmål och klicka sedan återanvända beräkningsmål med hjälp av följande steg:

1. Klicka på den **+** logga att lägga till ett beräkningsmål.
2. Ange ett namn för beräkningsmål.
3. Välj typ av beräkningsresurser kan bifoga för utbildning. Batch AI och virtuella datorer stöds för närvarande i portalen för utbildning.
4. Välj Använd befintlig.
    - När du ansluter Batch AI-kluster, Välj beräkningsmål i listrutan, väljer arbetsytan Batch AI och Batch AI-kluster och klickar **skapa**.
    - När du ansluter en virtuell dator, ange IP-adressen, användarnamnet och lösenordet, privata/offentliga nycklar och porten och klicka på Skapa.

    > [!NOTE]
    > Microsoft rekommenderar att du använder SSH-nycklar som de är säkrare än lösenord. Lösenord är sårbara för råstyrkeattacker mot attacker, medan SSH-nycklar är beroende av kryptografiska signaturer. Information om hur du skapar SSH-nycklar för användning med Azure virtuella datorer finns i följande dokument:
    >
    > * [Skapa och använda SSH-nycklar på Linux eller macOS]( https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Skapa och använda SSH-nycklar i Windows]( https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

5. Du kan visa statusen för Etableringsstatus genom att välja beräkningsmål i listan med beräkningar.
6. Du kan nu skicka en körning mot dessa mål.

## <a name="examples"></a>Exempel
Följande anteckningsböcker demonstrera begreppen i den här artikeln:
* `01.getting-started/02.train-on-local/02.train-on-local.ipynb`
* `01.getting-started/04.train-on-remote-vm/04.train-on-remote-vm.ipynb`
* `01.getting-started/03.train-on-aci/03.train-on-aci.ipynb`
* `01.getting-started/05.train-in-spark/05.train-in-spark.ipynb`
* `01.getting-started/07.hyperdrive-with-sklearn/07.hyperdrive-with-sklearn.ipynb`

Hämta dessa anteckningsböcker: [!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nästa steg

* [Azure Machine Learning SDK-referens](http://aka.ms/aml-sdk)
* [Självstudie: Träna en modell](tutorial-train-models-with-aml.md)
* [Var du vill distribuera modeller](how-to-deploy-and-where.md)
* [Skapa machine learning pipelines med Azure Machine Learning-tjänsten](concept-ml-pipelines.md)
