---
title: Skapa och använda beräkningsmål för modellträning
titleSuffix: Azure Machine Learning service
description: Konfigurera utbildning-miljöer (beräkningsmål) för machine learning-modellen. Du kan enkelt växla mellan miljöer för utbildning. Starta utbildning lokalt. Om du vill skala ut kan växla till en molnbaserad beräkningsmål.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.reviewer: sgilley
ms.service: machine-learning
ms.component: core
ms.topic: article
ms.date: 01/07/2019
ms.custom: seodec18
ms.openlocfilehash: 75a1a8763125e1e93691e2a28bc90a6d02ed7c40
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2019
ms.locfileid: "54246338"
---
# <a name="set-up-compute-targets-for-model-training"></a>Konfigurera beräkningsmål för modellträning

Med Azure Machine Learning-tjänsten kan du träna din modell på en mängd olika resurser eller miljöer, samlingsnamnet [ __beräkningsmål__](concept-azure-machine-learning-architecture.md#compute-target). Beräkningsmål kan vara en lokal dator eller en molnresurs, till exempel ett Azure beräkning av Machine Learning, Azure HDInsight eller en fjärransluten virtuell dator.  

Du kan skapa och hantera ett beräkningsmål som använder Azure Machine Learning SDK, Azure-portalen eller Azure CLI. Om du har beräkningsmål som har skapats via en annan tjänst (till exempel ett HDInsight-kluster), kan du använda dem genom att koppla dem till din arbetsyta för Azure Machine Learning-tjänsten.
 
I den här artikeln får du lära dig hur du använder olika beräkningsmål.  Stegen för alla beräkningsmål följer samma arbetsflöde:
1. __Skapa__ beräkningsmål om du inte redan har ett.
2. __Bifoga__ beräkningsmål till din arbetsyta.
3. __Konfigurera__ beräkningarna rikta så att den innehåller Python-miljön och paketet beroenden som krävs av skriptet.

>[!NOTE]
> Koden i den här artikeln har testats med Azure Machine Learning SDK version 1.0.6.

## <a name="supported-compute-targets"></a>Stöds beräkningsmål

Azure Machine Learning-tjänsten har olika stöd för olika beräkningsmål. En typisk modellen för säkerhetsutveckling börjar med utveckling/experimentering på en liten mängd data. I det här skedet bör du använda en lokal miljö. Den lokala datorn eller en molnbaserad VM. När du skalar upp utbildning på större datauppsättningar eller göra distribuerad utbildning, bör du använda beräkning av Azure Machine Learning för att skapa ett enda eller flera node kluster som skalar varje gång du skickar en körning. Du kan även bifoga dina egna beräkningsresurs, även om stöd för olika scenarier kan variera som beskrivs nedan:


|Beräkningsmål| GPU-acceleration | Automatiserad<br/> finjustering av hyperparametrar | Automatiserad</br> maskininlärning | Pipeline-vänlig|
|----|:----:|:----:|:----:|:----:|
|[Lokal dator](#local)| Kanske | &nbsp; | ✓ | &nbsp; |
|[Azure Machine Learning-beräkning](#amlcompute)| ✓ | ✓ | ✓ | ✓ |
|[Fjärransluten virtuell dator](#vm) | ✓ | ✓ | ✓ | ✓ |
|[Azure Databricks](how-to-create-your-first-pipeline.md#databricks)| &nbsp; | &nbsp; | ✓ | ✓[*](#pipeline-only) |
|[Azure Data Lake Analytics](how-to-create-your-first-pipeline.md#adla)| &nbsp; | &nbsp; | &nbsp; | ✓[*](#pipeline-only) |
|[Azure HDInsight](#hdinsight)| &nbsp; | &nbsp; | &nbsp; | ✓ |

<a id="pipeline-only"></a>__*__ Azure Databricks och Azure Data Lake Analytics kan __endast__ användas i en pipeline. 

>Du skapar beräkningsmål för machine learning pipelines som visas i den här artikeln, men du kan använda dessa beräkningar i pipeline-stegen i stället för de metoder som beskrivs här.  Endast några steg för pipeline använder dessutom kör konfigurationen som beskrivs i den här artikeln.  Läs mer om hur du använder beräkningsmål i en pipeline [skapa och köra en maskininlärningspipeline](how-to-create-your-first-pipeline.md).

## <a name="whats-a-run-configuration"></a>Vad är en körningskonfiguration?

När utbildning, är det vanligt att starta på den lokala datorn och kör senare utbildning skriptet på en andra beräkningsmål. Med Azure Machine Learning-tjänsten kan du köra skriptet på olika beräkningsmål utan att behöva ändra ditt skript. 

Allt du behöver göra är att definiera miljön för varje beräkningsmål med en **körningskonfigurationen**.  När du vill köra experimentet utbildning på en andra beräkningsmål du sedan ange körningskonfigurationen för den beräkningen. 

Läs mer om [skickar experiment](#submit) i slutet av den här artikeln.

### <a name="manage-environment-and-dependencies"></a>Hantera miljön och beroenden

När du skapar en körningskonfiguration, måste du bestämma hur du hanterar miljön och beroenden på beräkningsmål. 

#### <a name="system-managed-environment"></a>System-hanterad miljö

Använd en miljö som hanteras av datorn när du vill [Conda](https://conda.io/docs/) att hantera Python-miljön och skriptberoenden för dig. En miljö som hanteras av datorn antas som standard och de vanligaste valet. Det är användbart på fjärranslutna beräkningsmål, särskilt när du inte kan konfigurera som mål. 

Allt du behöver göra är att ange varje paket beroende med hjälp av den [CondaDependency klass](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) sedan Conda skapar en fil med namnet **conda_dependencies.yml** i den **aml_config** katalogen i din arbetsyta med din lista över paketberoenden och ställer in Python-miljön när du skickar in din träningsexperiment. 

Den första konfigureringen av en ny miljö kan ta flera minuter beroende på storleken på de nödvändiga beroendena. Så länge listan över paket förblir oförändrat, sker Ställ in tid bara en gång.
  
Följande kod visar ett exempel på en system-hanterad miljö kräver scikit-Läs:
    
[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/runconfig.py?name=run_system_managed)]

#### <a name="user-managed-environment"></a>Användarhanterade miljö

För en användarhanterade miljöer är du ansvarig för att konfigurera din miljö och installerar varje paket utbildning-skriptet på beräkningsmål. Om din miljö redan har konfigurerats (till exempel på den lokala datorn), kan du hoppa över Ställ in steg genom att ange `user_managed_dependencies` till True. Conda kommer inte att kontrollera din miljö eller installera något för dig.

Följande kod visar ett exempel på hur du konfigurerar träningskörningar för en användarhanterade miljö:

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/runconfig.py?name=run_user_managed)]
  
## <a name="set-up-compute-targets-with-python"></a>Konfigurera beräkningsmål med Python

Använd nedanstående avsnitt för att konfigurera dessa beräkningsmål:

* [Lokal dator](#local)
* [Azure Machine Learning-beräkning](#amlcompute)
* [Fjärranslutna virtuella datorer](#vm)
* [Azure HDInsight](#hdinsight)


### <a id="local"></a>Lokal dator

1. **Skapa och koppla**: Det finns inget behov att skapa eller koppla en beräkningsmål för att använda din lokala dator som miljön.  

1. **Konfigurera**:  När du använder den lokala datorn som en beräkningsmål utbildning koden körs din [utvecklingsmiljö](how-to-configure-environment.md).  Om miljön har redan Python-paket som du behöver använda användarhanterade-miljö.

 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=run_local)]

Nu när du har kopplat beräkningarna och konfigurerat din körning, nästa steg är att [skicka utbildning kör](#submit).

### <a id="amlcompute"></a>Azure Machine Learning-beräkning

Beräkning av Azure Machine Learning är en infrastruktur för hanterade beräkning som används att enkelt skapa en beräkning för en eller flera noder. Beräkningen har skapats i din arbetsyteregion som en resurs som kan delas med andra användare i din arbetsyta. Beräkningen som skalar upp automatiskt när ett jobb skickas och kan placeras i en Azure-nätverk. Beräkningen som körs i en miljö med behållare och paketerar din modell beroenden i en [dockerbehållare](https://www.docker.com/why-docker).

Du kan använda beräkning av Azure Machine Learning för att distribuera träningsprocess över ett kluster med CPU eller GPU-beräkningsnoder i molnet. Mer information om de storlekar som innehåller GPU: er finns i [GPU-optimerade virtuella datorstorlekar](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu).

Beräkning av Azure Machine Learning har standardgränser, till exempel antalet kärnor som kan allokeras. Mer information finns i [hantera och begära kvoter för Azure-resurser](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas).


Du kan skapa en Azure Machine Learning compute-miljö på begäran när du schemalägger en körning eller som en beständig resurs.

#### <a name="run-based-creation"></a>Kör-baserade skapas

Du kan skapa beräkning av Azure Machine Learning som beräkningsmål för vid körning. Beräkningen som skapas automatiskt för din körning. Klustret skalas upp till antalet **max_nodes** som du anger i kör config. Beräkningarna tas bort automatiskt när körningen har slutförts.

> [!IMPORTANT]
> Kör-baserade skapandet av Azure Machine Learning beräkning förhandsvisas just nu. Använd inte kör-baserade skapas om du använder automatisk finjustering av hyperparametrar eller automatiserade maskininlärning. Om du vill använda finjustering av hyperparametrar eller automatiserade maskininlärning, skapa en [beständiga beräkning](#persistent) rikta istället.

1.  **Skapa, Anslut och konfigurera**: Skapa kör-baserade utför de nödvändiga stegen för att skapa, Anslut och konfigurera beräkningsmål med körningskonfigurationen.  

  [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute.py?name=run_temp_compute)]


Nu när du har kopplat beräkningarna och konfigurerat din körning, nästa steg är att [skicka utbildning kör](#submit).

#### <a id="persistent"></a>Beständiga beräkning

En beständig Azure beräkning av Machine Learning kan återanvändas i jobb. Beräkningarna kan delas med andra användare i arbetsytan och sparas mellan jobb.

1. **Skapa och koppla**: Om du vill skapa en beständig beräkning av Azure Machine Learning-resurs i Python, ange den **vm_size** och **max_nodes** egenskaper. Azure Machine Learning använder sedan smarta standardvärden för de andra egenskaperna. Beräkning skalar ned till noll noder när det inte används.   Dedikerade virtuella datorer skapas för att köra dina jobb efter behov.
    
    * **vm_size**: VM-familj med noder som skapats av beräkning av Azure Machine Learning.
    * **max_nodes**: Max antal noder att automatiskt skala upp till när du kör ett jobb på beräkning av Azure Machine Learning.
    
 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

  Du kan också konfigurera flera avancerade egenskaper när du skapar beräkning av Azure Machine Learning. Egenskaperna kan du skapa ett beständiga kluster med fast storlek eller i ett befintligt virtuellt Azure-nätverk i din prenumeration.  Se den [AmlCompute klass](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py
    ) mer information.
    
 Du kan skapa och koppla en beständig beräkning av Azure Machine Learning-resurs [i Azure-portalen](#portal-create).

1. **Konfigurera**: Skapa en körningskonfiguration för beständiga beräkningsmål.

 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=run_amlcompute)]

Nu när du har kopplat beräkningarna och konfigurerat din körning, nästa steg är att [skicka utbildning kör](#submit).


### <a id="vm"></a>Fjärranslutna virtuella datorer

Azure Machine Learning har även stöd för att få ut dina egna beräkningsresurs och kopplar den till din arbetsyta. En sådan resurstypen är ett godtyckligt fjärransluten virtuell dator, så länge den kan nås från Azure Machine Learning-tjänsten. Resursen kan vara en Azure-dator, en fjärrserver i din organisation eller lokalt. Mer specifikt kan baserat på IP-adress och autentiseringsuppgifter (användarnamn och lösenord eller SSH-nyckel), du använda alla tillgängliga virtuella datorer för fjärranslutna körningar.

Du kan använda en inbyggd system conda-miljö, en redan befintlig Python-miljö eller en Docker-behållare. Du måste ha en Docker-motor som körs på den virtuella datorn för att köra på en Docker-behållare. Den här funktionen är särskilt användbart när du vill att en mer flexibel, molnbaserad utveckling/experimentmiljön än den lokala datorn.

Använda Azure Data Science Virtual Machine (DSVM) som virtuell Azure-dator med det här scenariot. Den här virtuella datorn är en förkonfigurerad datavetenskap och AI-utvecklingsmiljö i Azure. Den virtuella datorn erbjuder en granskad val av verktyg och ramverk för fullständig livscykel machine learning-utveckling. Mer information om hur du använder DSVM med Azure Machine Learning finns [konfigurera en utvecklingsmiljö](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-environment#dsvm).

1. **Skapa**: Skapa en DSVM innan du använder den för att träna din modell. Om du vill skapa den här resursen [etablera den virtuella datorn för datavetenskap för Linux (Ubuntu)](https://docs.microsoft.com/en-us/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro).

    > [!WARNING]
    > Azure Machine Learning stöder endast virtuella datorer som kör Ubuntu. När du skapar en virtuell dator eller välj en befintlig virtuell dator, väljer du en virtuell dator med Ubuntu.

1. **Bifoga**: Om du vill koppla en befintlig virtuell dator som en beräkningsmål, måste du ange det fullständigt kvalificerade domännamnet (FQDN), användarnamn och lösenord för den virtuella datorn. I det här exemplet ersätter \<fqdn > med offentliga FQDN för den virtuella datorn eller den offentliga IP-adressen. Ersätt \<användarnamn > och \<lösenord > med SSH-användarnamn och lösenord för den virtuella datorn.

 ```python
 from azureml.core.compute import RemoteCompute, ComputeTarget

 # Create the compute config 
 compute_target_name = "attach-dsvm"
 attach_config = RemoteCompute.attach_configuration(address = "<fqdn>",
                                                    ssh_port=22,
                                                    username='<username>',
                                                    password="<password>")

 # If you use SSH instead of a password, use this code:
 #                                                  ssh_port=22,
 #                                                  username='<username>',
 #                                                  password=None,
 #                                                  private_key_file="<path-to-file>",
 #                                                  private_key_passphrase="<passphrase>")

 # Attach the compute
 compute = ComputeTarget.attach(ws, compute_target_name, attach_config)

 compute.wait_for_completion(show_output=True)
 ```

 Alternativt kan du koppla DSVM till din arbetsyta [med Azure portal](#portal-reuse).

1. **Konfigurera**: Skapa en körningskonfiguration för beräkningsmål DSVM. Docker och conda används för att skapa och konfigurera miljön på DSVM.

 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/dsvm.py?name=run_dsvm)]


Nu när du har kopplat beräkningarna och konfigurerat din körning, nästa steg är att [skicka utbildning kör](#submit).

### <a id="hdinsight"></a>Azure HDInsight 

Azure HDInsight är en populär plattform för stordataanalys. Plattformen ger Apache Spark, som kan användas för att träna din modell.

1. **Skapa**:  Skapa HDInsight-klustret innan du kan använda den för att träna din modell. För att skapa en Apache Spark på HDInsight-kluster, se [skapa ett Spark-kluster i HDInsight](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql). 

    När du skapar klustret måste du ange ett SSH-användarnamn och lösenord. Anteckna dessa värden som du behöver dem för att använda HDInsight som en beräkningsmål.
    
    När klustret har skapats kan du ansluta till det med värdnamnet \<klusternamn >-ssh.azurehdinsight.net, där \<klusternamn > är namnet som du angav för klustret. 

1. **Bifoga**: Om du vill koppla ett HDInsight-kluster som en beräkningsmål, måste du ange den värddatornamn, användarnamn och lösenord för HDInsight-klustret. I följande exempel använder SDK för att koppla ett kluster till din arbetsyta. I det här exemplet ersätter \<klusternamn > med namnet på klustret. Ersätt \<användarnamn > och \<lösenord > med SSH-användarnamn och lösenord för klustret.

  ```python
 from azureml.core.compute import ComputeTarget, HDInsightCompute
 from azureml.exceptions import ComputeTargetException

 try:
    # if you want to connect using SSH key instead of username/password you can provide parameters private_key_file and private_key_passphrase
    attach_config = HDInsightCompute.attach_configuration(address='<clustername>-ssh.azureinsight.net', 
                                                          ssh_port=22, 
                                                          username='<ssh-username>', 
                                                          password='<ssh-pwd>')
    hdi_compute = ComputeTarget.attach(workspace=ws, 
                                       name='myhdi', 
                                       attach_configuration=attach_config)

 except ComputeTargetException as e:
    print("Caught = {}".format(e.message))

 hdi_compute.wait_for_completion(show_output=True)
  ```

  Alternativt kan du koppla HDInsight-klustret till din arbetsyta [med Azure portal](#portal-reuse).

1. **Konfigurera**: Skapa en körningskonfiguration för beräkningsmål HDI. 

 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


Nu när du har kopplat beräkningarna och konfigurerat din körning, nästa steg är att [skicka utbildning kör](#submit).


## <a name="set-up-compute-in-the-azure-portal"></a>Konfigurera beräkning i Azure portal

Du kan komma åt beräkningsmål som är associerade med din arbetsyta i Azure-portalen.  Du kan använda portalen för att:

* [Visa beräkningsmål](#portal-view) kopplad till din arbetsyta
* [Skapa ett beräkningsmål](#portal-create) i din arbetsyta
* [Återanvända befintliga beräkningsmål](#portal-reuse)

När ett mål skapas och ansluts till din arbetsyta, kommer du använda den i din kör konfiguration med en `ComputeTarget` objekt: 

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

### <a id="portal-view"></a>Visa beräkningsmål


Använd följande steg om du vill se beräkningsmål för din arbetsyta:

1. Navigera till den [Azure-portalen](https://portal.azure.com) och öppna din arbetsyta. 
1. Under __program__väljer __Compute__.

    ![Visa beräkning fliken](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)

### <a id="portal-create"></a>Skapa ett beräkningsmål

Följ de här stegen om du vill visa en lista över beräkningsmål. Använd sedan dessa steg för att skapa ett beräkningsmål: 

1. Klicka på plustecknet (+) för att lägga till ett beräkningsmål.

    ![Lägg till ett beräkningsmål](./media/how-to-set-up-training-targets/add-compute-target.png) 

1. Ange ett namn för beräkningsmål. 

1. Välj **beräkning av Machine Learning** som typ av beräkning som ska användas för __utbildning__. 

    >[!NOTE]
    >Beräkning av Azure Machine Learning är hanteras endast-compute-resursen som du kan skapa i Azure-portalen.  Alla andra beräkningsresurser kan kopplas när de skapas.

1. Fyll i formuläret. Ange värden för de obligatoriska egenskaperna särskilt **VM-serie**, och **högsta antalet noder** du använder för att sätta upp beräkningarna.  

    ![Fyll i formuläret](./media/how-to-set-up-training-targets/add-compute-form.png) 

1. Välj __Skapa__.


1. Visa status för åtgärden för att skapa genom att välja beräkningsmål i listan:

    ![Välj ett beräkningsmål för att visa status för Skapa-åtgärd](./media/how-to-set-up-training-targets/View_list.png)

1. Då visas information om beräkningsmål: 

    ![Visa datorn målinformation](./media/how-to-set-up-training-targets/compute-target-details.png) 



### <a id="portal-reuse"></a>Återanvända befintliga beräkningsmål

Följ stegen som beskrivs ovan för att visa en lista över beräkningsmål. Sedan använda de här stegen för att återanvända en beräkningsmål: 

1. Klicka på plustecknet (+) för att lägga till ett beräkningsmål. 
1. Ange ett namn för beräkningsmål. 
1. Välj den typ av beräkningsresurser kan bifoga för __utbildning__:

    > [!IMPORTANT]
    > Inte alla beräkningsresurser typer kan kopplas från Azure-portalen. Vilka beräkningstyper av som för närvarande kan kopplas till utbildning är:
    >
    > * En fjärransluten virtuell dator
    > * Azure Databricks (för användning i maskininlärning pipelines)
    > * Azure Data Lake Analytics (för användning i maskininlärning pipelines)
    > * Azure HDInsight

1. Fyll i formuläret och ange värden för de obligatoriska egenskaperna.

    > [!NOTE]
    > Microsoft rekommenderar att du använder SSH-nycklar, vilket är säkrare än lösenord. Lösenord är sårbara för råstyrkeattacker. SSH-nycklar är beroende av kryptografiska signaturer. Information om hur du skapar SSH-nycklar för användning med Azure virtuella datorer finns i följande dokument:
    >
    > * [Skapa och använda SSH-nycklar på Linux eller macOS](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Skapa och använda SSH-nycklar i Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. Välj __bifoga__. 
1. Visa status för åtgärden koppla genom att välja beräkningsmål i listan.

## <a name="set-up-compute-with-the-cli"></a>Konfigurera beräkning med CLI

Du kan komma åt beräkningsmål som är associerade med din arbetsyta med hjälp av den [CLI-tillägg](reference-azure-machine-learning-cli.md) för Azure Machine Learning-tjänsten.  Du kan använda CLI för att:

* Skapa en hanterad beräkningsmål
* Uppdatera hanterade beräkningsmål
* Koppla en ohanterad beräkningsmål

Mer information finns i [resurshantering](reference-azure-machine-learning-cli.md#resource-management).


## <a id="submit"></a>Skicka utbildning som kör

När du skapar en körningskonfiguration kan du bara använda den för att köra experimentet.  Mönstret kod för att skicka en utbildning körning är samma för alla typer av beräkningsmål:

1. Skapa ett experiment att köra
1. Skicka in körningen.
1. Vänta tills den kör för att slutföra.

### <a name="create-an-experiment"></a>Skapa ett experiment

Skapa först ett experiment i din arbetsyta.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=experiment)]

### <a name="submit-the-experiment"></a>Skicka experimentet

Skicka experiment med en `ScriptRunConfig` objekt.  Det här objektet innehåller de:

* **källkatalog**: Källkatalogen som innehåller skriptet utbildning
* **skriptet**: Identifiera skriptet utbildning
* **run_config**: Körningskonfigurationen, som i sin tur definierar var utbildningen kommer att göras.

När du skickar in ett utbildning kör en ögonblicksbild av den katalog som innehåller dina utbildningsskript skapas och skickas till beräkningsmål. Mer information finns i [ögonblicksbilder](concept-azure-machine-learning-architecture.md#snapshot).

Till exempel vill använda [lokala målet](#local) konfiguration:

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=local_submit)]

Växla samma experiment att köra i en annan beräkningsmål genom att använda en annan kör konfiguration som den [amlcompute target](#amlcompute):

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=amlcompute_submit)]

Eller så kan du:

* Skicka experiment med en `Estimator` objekt enligt [träna ML-modeller med estimators](how-to-train-ml-models.md). 
* Skicka ett experiment [med hjälp av CLI-tillägget](reference-azure-machine-learning-cli.md#experiments).

## <a name="notebook-examples"></a>Notebook-exempel

Se dessa anteckningsböcker för exempel på utbildning med olika beräkningsmål:
* [How-to-till-användning – azureml/utbildning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [självstudier/img-klassificering – del 1 – training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nästa steg

* [Självstudier: Träna en modell](tutorial-train-models-with-aml.md) använder hanterade beräkningsmål för att träna en modell.
* När du har en tränad modell, lär du dig [hur och var du vill distribuera modeller](how-to-deploy-and-where.md).
* Visa den [RunConfiguration klass](https://docs.microsoft.com/en-us/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) SDK-referens.
* [Använda Azure Machine Learning-tjänsten med Azure-nätverk](how-to-enable-virtual-network.md)
