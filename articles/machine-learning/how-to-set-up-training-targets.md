---
title: Använda beräknings mål för modell träning
titleSuffix: Azure Machine Learning
description: Konfigurera utbildnings miljöer (beräknings mål) för maskin inlärnings modell träning. Du kan enkelt växla mellan utbildnings miljöer. Börja träna lokalt. Om du behöver skala ut växlar du till ett moln baserat beräknings mål.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/13/2020
ms.custom: seodec18
ms.openlocfilehash: 209ed755a7ef83b67170ef75911f93cdda742caa
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/14/2020
ms.locfileid: "79368204"
---
# <a name="set-up-and-use-compute-targets-for-model-training"></a>Konfigurera och Använd Compute-mål för modell träning 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Med Azure Machine Learning kan du träna din modell på en mängd olika resurser eller miljöer, som sammankallas för [__beräknings mål__](concept-azure-machine-learning-architecture.md#compute-targets). Ett beräknings mål kan vara en lokal dator eller en moln resurs, till exempel en Azure Machine Learning beräkning, Azure HDInsight eller en virtuell dator för virtuella datorer.  Du kan också skapa beräknings mål för modell distribution enligt beskrivningen i ["var och hur du distribuerar dina modeller"](how-to-deploy-and-where.md).

Du kan skapa och hantera ett beräknings mål med hjälp av Azure Machine Learning SDK, Azure Machine Learning Studio, Azure CLI eller Azure Machine Learning VS Code Extension. Om du har beräknings mål som har skapats via en annan tjänst (till exempel ett HDInsight-kluster) kan du använda dem genom att koppla dem till din Azure Machine Learning-arbetsyta.
 
I den här artikeln får du lära dig hur du använder olika beräknings mål för modell träning.  Stegen för alla beräknings mål följer samma arbets flöde:
1. __Skapa__ ett beräknings mål om du inte redan har ett.
2. __Koppla__ beräknings målet till din arbets yta.
3. __Konfigurera__ beräknings målet så att det innehåller python-miljön och paket beroenden som krävs av ditt skript.


>[!NOTE]
> Koden i den här artikeln har testats med Azure Machine Learning SDK-version 1.0.74.

## <a name="compute-targets-for-training"></a>Compute-mål för utbildning

Azure Machine Learning har varierande stöd för olika beräknings mål. En typisk modellen för säkerhetsutveckling börjar med utveckling/experimentering på en liten mängd data. I det här skedet bör du använda en lokal miljö. Den lokala datorn eller en molnbaserad VM. När du skalar upp utbildning på större datauppsättningar eller göra distribuerad utbildning, bör du använda beräkning av Azure Machine Learning för att skapa ett enda eller flera node kluster som skalar varje gång du skickar en körning. Du kan även bifoga dina egna beräkningsresurs, även om stöd för olika scenarier kan variera som beskrivs nedan:

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]


> [!NOTE]
> Azure Machine Learning Compute kan skapas som en permanent resurs eller skapas dynamiskt när du begär en körning. Med körnings-baserad skapande tas beräknings målet bort när övnings körningen är klar, så du kan inte återanvända beräknings målen som skapats på det här sättet.

## <a name="whats-a-run-configuration"></a>Vad är en körnings konfiguration?

När det gäller utbildning är det vanligt att starta på den lokala datorn och senare köra det utbildnings skriptet på ett annat Compute-mål. Med Azure Machine Learning kan du köra skriptet på olika beräknings mål utan att behöva ändra ditt skript.

Allt du behöver göra är att definiera miljön för varje beräknings mål i en **körnings konfiguration**.  När du sedan vill köra ditt utbildnings experiment på ett annat beräknings mål anger du körnings konfigurationen för den beräkningen. Information om hur du anger en miljö och binder den till att köra konfigurationen finns i [skapa och hantera miljöer för utbildning och distribution](how-to-use-environments.md).

Läs mer om hur du [skickar experiment](#submit) i slutet av den här artikeln.

## <a name="whats-an-estimator"></a>Vad är en uppskattning?

För att under lätta modell utbildningen med populära ramverk, tillhandahåller Azure Machine Learning python SDK en alternativ högre abstraktion, klassen uppskattning.  Med den här klassen kan du enkelt konstruera kör konfigurationer. Du kan skapa och använda en generisk [uppskattning](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) för att skicka utbildnings skript som använder valfritt ramverk för inlärning (till exempel scikit – lära). Vi rekommenderar att du använder en uppskattning för utbildning eftersom den automatiskt konstruerar inbäddade objekt som en miljö eller RunConfiguration objekt åt dig. Om du vill ha mer kontroll över hur dessa objekt skapas och ange vilka paket som ska installeras för att köra experimentet, följer du [dessa steg](#amlcompute) för att skicka dina utbildnings experiment med ett RunConfiguration-objekt på en Azure Machine Learning-beräkning.

För PyTorch-, TensorFlow-och kedje uppgifter-aktiviteter tillhandahåller Azure Machine Learning även de olika uppskattningarna [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)och [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) för att förenkla användningen av dessa ramverk.

Mer information finns i [träna ml-modeller med uppskattningar](how-to-train-ml-models.md).

## <a name="whats-an-ml-pipeline"></a>Vad är en ML-pipeline?

Med ML-pipelines kan du optimera arbets flödet med enkelhet, hastighet, portabilitet och åter användning. När du skapar pipeliner med Azure Machine Learning kan du fokusera på din expertis, maskin inlärning i stället för infrastruktur och automatisering.

ML pipelines skapas från flera **steg**, som är distinkta beräknings enheter i pipelinen. Varje steg kan köras oberoende och använda isolerade beräknings resurser. Detta gör det möjligt för flera data forskare att arbeta med samma pipeline samtidigt utan beskattnings beräknings resurser och gör det också enkelt att använda olika beräknings typer/storlekar för varje steg.

> [!TIP]
> ML pipelines kan använda kör konfiguration eller uppskattningar när du tränar modeller.

Även om ML pipelines kan träna modeller, kan de också förbereda data innan de tränas och distribuera modeller efter utbildning. En av de främsta användnings fallen för pipelines är batch-poäng. Mer information finns i [pipelines: optimera Machine Learning-arbetsflöden](concept-ml-pipelines.md).

## <a name="set-up-in-python"></a>Konfigurera i python

Använd avsnitten nedan för att konfigurera dessa beräknings mål:

* [Lokal dator](#local)
* [Azure Machine Learning Compute](#amlcompute)
* [Virtuella fjärrdatorer](#vm)
* [Azure HDInsight](#hdinsight)


### <a id="local"></a>Lokal dator

1. **Skapa och koppla**: du behöver inte skapa eller ansluta ett beräknings mål för att använda din lokala dator som tränings miljö.  

1. **Konfigurera**: när du använder den lokala datorn som ett beräknings mål körs inlärnings koden i [utvecklings miljön](how-to-configure-environment.md).  Om den miljön redan har de python-paket som du behöver använder du den användar hanterade miljön.

 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=run_local)]

Nu när du har kopplat beräkningen och konfigurerat din körning är nästa steg att [Skicka utbildningen](#submit).

### <a id="amlcompute"></a>Azure Machine Learning Compute

Azure Machine Learning Compute är en hanterad beräknings infrastruktur som gör det möjligt för användaren att enkelt skapa en enda eller beräkning med flera noder. Beräkningen skapas i arbets ytans region som en resurs som kan delas med andra användare i din arbets yta. Beräkningen skalas upp automatiskt när ett jobb skickas och kan placeras i ett Azure-Virtual Network. Beräkningen körs i en behållare miljö och paketerar dina modell beroenden i en [Docker-behållare](https://www.docker.com/why-docker).

Du kan använda beräkning av Azure Machine Learning för att distribuera träningsprocess över ett kluster med CPU eller GPU-beräkningsnoder i molnet. Mer information om de VM-storlekar som innehåller GPU: er finns i [GPU-optimerade storlekar för virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu).

Azure Machine Learning Compute har standard gränser, till exempel antalet kärnor som kan allokeras. Mer information finns i [Hantera och begära kvoter för Azure-resurser](https://docs.microsoft.com/azure/machine-learning/how-to-manage-quotas).


Du kan skapa en Azure Machine Learning beräknings miljö på begäran när du schemalägger en körning eller som en beständig resurs.

#### <a name="run-based-creation"></a>Kör-baserade skapas

Du kan skapa Azure Machine Learning Compute som ett beräknings mål vid körning. Beräkningen skapas automatiskt för din körning. Beräkningen tas bort automatiskt när körningen är klar. 

> [!IMPORTANT]
> Körning-baserad skapande av Azure Machine Learning Compute är för närvarande en för hands version. Använd inte körnings-baserad generering om du använder automatisk justering av en parameter eller Automatisk maskin inlärning. Om du vill använda en inställning för min parameter eller Automatisk maskin inlärning skapar du i stället ett [beständigt beräknings](#persistent) mål.

1.  **Skapa, ansluta och konfigurera**: den körning som skapas utför alla nödvändiga steg för att skapa, ansluta och konfigurera beräknings målet med körnings konfigurationen.  

  [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute.py?name=run_temp_compute)]


Nu när du har kopplat beräkningen och konfigurerat din körning är nästa steg att [Skicka utbildningen](#submit).

#### <a id="persistent"></a>Beständig beräkning

En beständig Azure Machine Learning beräkning kan återanvändas mellan jobb. Beräkningen kan delas med andra användare i arbets ytan och bevaras mellan jobben.

1. **Skapa och koppla**: om du vill skapa en beständig Azure Machine Learning beräknings resurs i python anger du **vm_size** och **max_nodes** egenskaper. Azure Machine Learning använder sedan smarta standardinställningar för de andra egenskaperna. Beräkningen skalas ned till noll noder när den inte används.   Dedikerade virtuella datorer skapas för att köra jobben efter behov.
    
    * **vm_size**: VM-serien för noderna som skapats av Azure Machine Learning Compute.
    * **max_nodes**: det högsta antalet noder som autoskalar upp till när du kör ett jobb på Azure Machine Learning beräkning.
    
   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

   Du kan också konfigurera flera avancerade egenskaper när du skapar Azure Machine Learning Compute. Med egenskaperna kan du skapa ett beständigt kluster med fast storlek eller inom en befintlig Azure-Virtual Network i din prenumeration.  Mer information finns i [AmlCompute-klassen](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py
    ) .
    
   Eller så kan du skapa och koppla en beständig Azure Machine Learning beräknings resurs i [Azure Machine Learning Studio](#portal-create).

1. **Konfigurera**: skapa en körnings konfiguration för det beständiga beräknings målet.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=run_amlcompute)]

Nu när du har kopplat beräkningen och konfigurerat din körning är nästa steg att [Skicka utbildningen](#submit).


### <a id="vm"></a>Virtuella fjärrdatorer

Azure Machine Learning har även stöd för att få ut dina egna beräkningsresurs och kopplar den till din arbetsyta. En sådan resurs typ är en godtycklig virtuell fjärrdator, så länge den är tillgänglig från Azure Machine Learning. Resursen kan vara en virtuell Azure-dator, en fjärrserver i din organisation eller lokalt. Mer specifikt, baserat på IP-adress och autentiseringsuppgifter (användar namn och lösen ord eller SSH-nyckel), kan du använda valfri tillgänglig virtuell dator för fjärrkörningar.

Du kan använda en inbyggd system conda-miljö, en redan befintlig Python-miljö eller en Docker-behållare. Om du vill köra på en Docker-behållare måste du ha en Docker-motor som körs på den virtuella datorn. Den här funktionen är särskilt användbart när du vill att en mer flexibel, molnbaserad utveckling/experimentmiljön än den lokala datorn.

Använd Azure-Data Science Virtual Machine (DSVM) som den virtuella Azure-dator som du väljer för det här scenariot. Den här virtuella datorn är en förkonfigurerad miljö för data vetenskap och AI-utveckling i Azure. Den virtuella datorn innehåller ett granskat val av verktyg och ramverk för hela livs cykeln för Machine Learning-utveckling. Mer information om hur du använder DSVM med Azure Machine Learning finns i [Konfigurera en utvecklings miljö](https://docs.microsoft.com/azure/machine-learning/how-to-configure-environment#dsvm).

1. **Skapa**: skapa en DSVM innan du använder den för att träna din modell. Information om hur du skapar den här resursen finns i [etablera data science Virtual Machine för Linux (Ubuntu)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro).

    > [!WARNING]
    > Azure Machine Learning stöder bara virtuella datorer som kör Ubuntu. När du skapar en virtuell dator eller väljer en befintlig virtuell dator måste du välja en virtuell dator som använder Ubuntu.

1. **Bifoga**: om du vill koppla en befintlig virtuell dator som ett beräknings mål måste du ange det fullständigt kvalificerade domän namnet (FQDN), användar namnet och lösen ordet för den virtuella datorn. I exemplet ersätter du \<FQDN > med det offentliga domän namnet för den virtuella datorn eller den offentliga IP-adressen. Ersätt \<användar namn > och \<lösen ord > med SSH-användarnamnet och lösen ordet för den virtuella datorn.

    > [!IMPORTANT]
    > Följande Azure-regioner har inte stöd för att koppla en virtuell dator med den offentliga IP-adressen för den virtuella datorn. Använd i stället Azure Resource Manager-ID: t för den virtuella datorn med parametern `resource_id`:
    >
    > * USA, Östra
    > * USA, västra 2
    > * USA, södra centrala
    >
    > Resurs-ID: t för den virtuella datorn kan konstrueras med prenumerations-ID, resurs grupp namn och namn på virtuell dator med följande sträng format: `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Compute/virtualMachines/<vm_name>`.


   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   attach_config = RemoteCompute.attach_configuration(address='<fqdn>',
                                                    ssh_port=22,
                                                    username='<username>',
                                                    password="<password>")
   # If in US East, US West 2, or US South Central, use the following instead:
   # attach_config = RemoteCompute.attach_configuration(resource_id='<resource_id>',
   #                                                 ssh_port=22,
   #                                                 username='<username>',
   #                                                 password="<password>")

   # If you authenticate with SSH keys instead, use this code:
   #                                                  ssh_port=22,
   #                                                  username='<username>',
   #                                                  password=None,
   #                                                  private_key_file="<path-to-file>",
   #                                                  private_key_passphrase="<passphrase>")

   # Attach the compute
   compute = ComputeTarget.attach(ws, compute_target_name, attach_config)

   compute.wait_for_completion(show_output=True)
   ```

   Eller så kan du koppla DSVM till din arbets yta [med Azure Machine Learning Studio](#portal-reuse).

1. **Konfigurera**: skapa en körnings konfiguration för DSVM Compute Target. Docker och Conda används för att skapa och konfigurera utbildnings miljön på DSVM.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/dsvm.py?name=run_dsvm)]


Nu när du har kopplat beräkningen och konfigurerat din körning är nästa steg att [Skicka utbildningen](#submit).

### <a id="hdinsight"></a>Azure HDInsight 

Azure HDInsight är en populär plattform för stor data analys. Plattformen ger Apache Spark som kan användas för att träna din modell.

1. **Skapa**: skapa HDInsight-klustret innan du använder det för att träna din modell. Information om hur du skapar ett spark på HDInsight-kluster finns i [skapa ett Spark-kluster i HDInsight](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql). 

    När du skapar klustret måste du ange ett SSH-användarnamn och-lösen ord. Anteckna dessa värden eftersom du behöver dem för att använda HDInsight som ett beräknings mål.
    
    När klustret har skapats ansluter du till det med värd namnet \<kluster namn >-ssh.azurehdinsight.net, där \<kluster namn > är det namn som du har angett för klustret. 

1. **Bifoga**: om du vill ansluta ett HDInsight-kluster som ett beräknings mål måste du ange värdnamn, användar namn och lösen ord för HDInsight-klustret. I följande exempel använder SDK för att koppla ett kluster till din arbetsyta. I exemplet ersätter du \<kluster namn > med namnet på klustret. Ersätt \<användar namn > och \<lösen ord > med SSH-användarnamnet och lösen ordet för klustret.

    > [!IMPORTANT]
    > Följande Azure-regioner har inte stöd för att bifoga ett HDInsight-kluster med den offentliga IP-adressen för klustret. Använd i stället Azure Resource Manager-ID: t för klustret med parametern `resource_id`:
    >
    > * USA, Östra
    > * USA, västra 2
    > * USA, södra centrala
    >
    > Resurs-ID för klustret kan konstrueras med prenumerations-ID, resurs grupps namn och kluster namn med följande sträng format: `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.HDInsight/clusters/<cluster_name>`.

   ```python
   from azureml.core.compute import ComputeTarget, HDInsightCompute
   from azureml.exceptions import ComputeTargetException

   try:
    # if you want to connect using SSH key instead of username/password you can provide parameters private_key_file and private_key_passphrase
    attach_config = HDInsightCompute.attach_configuration(address='<clustername>-ssh.azurehdinsight.net', 
                                                          ssh_port=22, 
                                                          username='<ssh-username>', 
                                                          password='<ssh-pwd>')
    # If you are in US East, US West 2, or US South Central, use the following instead:
    # attach_config = HDInsightCompute.attach_configuration(resource_id='<resource_id>',
    #                                                      ssh_port=22, 
    #                                                      username='<ssh-username>', 
    #                                                      password='<ssh-pwd>')
    hdi_compute = ComputeTarget.attach(workspace=ws, 
                                       name='myhdi', 
                                       attach_configuration=attach_config)

   except ComputeTargetException as e:
    print("Caught = {}".format(e.message))

   hdi_compute.wait_for_completion(show_output=True)
   ```

   Eller så kan du ansluta HDInsight-klustret till din arbets yta [med Azure Machine Learning Studio](#portal-reuse).

1. **Konfigurera**: skapa en körnings konfiguration för HDI Compute Target. 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


Nu när du har kopplat beräkningen och konfigurerat din körning är nästa steg att [Skicka utbildningen](#submit).


### <a id="azbatch"></a>Azure Batch 

Azure Batch används för att köra storskaliga parallella program och HPC-program (data behandling med höga prestanda) i molnet. AzureBatchStep kan användas i en Azure Machine Learning pipeline för att skicka jobb till en Azure Batch pool med datorer.

Om du vill bifoga Azure Batch som ett beräknings mål måste du använda Azure Machine Learning SDK och ange följande information:

-    **Azure Batch Compute Name**: ett eget namn som ska användas för data bearbetningen inom arbets ytan
-    **Azure Batch konto namn**: namnet på det Azure Batch kontot
-    **Resurs grupp**: den resurs grupp som innehåller det Azure Batch kontot.

Följande kod visar hur du kopplar Azure Batch som ett beräknings mål:

```python
from azureml.core.compute import ComputeTarget, BatchCompute
from azureml.exceptions import ComputeTargetException

# Name to associate with new compute in workspace
batch_compute_name = 'mybatchcompute'

# Batch account details needed to attach as compute to workspace
batch_account_name = "<batch_account_name>"  # Name of the Batch account
# Name of the resource group which contains this account
batch_resource_group = "<batch_resource_group>"

try:
    # check if the compute is already attached
    batch_compute = BatchCompute(ws, batch_compute_name)
except ComputeTargetException:
    print('Attaching Batch compute...')
    provisioning_config = BatchCompute.attach_configuration(
        resource_group=batch_resource_group, account_name=batch_account_name)
    batch_compute = ComputeTarget.attach(
        ws, batch_compute_name, provisioning_config)
    batch_compute.wait_for_completion()
    print("Provisioning state:{}".format(batch_compute.provisioning_state))
    print("Provisioning errors:{}".format(batch_compute.provisioning_errors))

print("Using Batch compute:{}".format(batch_compute.cluster_resource_id))
```

## <a name="set-up-in-azure-machine-learning-studio"></a>Konfigurera i Azure Machine Learning Studio

Du kan komma åt de beräknings mål som är associerade med din arbets yta i Azure Machine Learning Studio.  Du kan använda Studio för att:

* [Visa beräknings mål](#portal-view) som är kopplade till din arbets yta
* [Skapa ett beräknings mål](#portal-create) på din arbets yta
* [Bifoga ett beräknings mål](#portal-reuse) som har skapats utanför arbets ytan


När ett mål har skapats och kopplats till din arbets yta, kommer du att använda det i din körnings konfiguration med ett `ComputeTarget`-objekt: 

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

### <a id="portal-view"></a>Visa beräknings mål


Använd följande steg för att se beräknings målen för din arbets yta:

1. Navigera till [Azure Machine Learning Studio](https://ml.azure.com).
 
1. Under __program__väljer du __beräkning__.

    [![Visa fliken Compute](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace-expanded.png)

### <a id="portal-create"></a>Skapa ett beräknings mål

Följ föregående steg för att visa listan över beräknings mål. Använd sedan de här stegen för att skapa ett beräknings mål: 

1. Välj plus tecknet (+) för att lägga till ett beräknings mål.

    ![Lägg till ett beräknings mål](./media/how-to-set-up-training-targets/add-compute-target.png) 

1. Ange ett namn för beräknings målet. 

1. Välj **Machine Learning-beräkning** som den typ av beräkning som ska användas för __träning__. 

    >[!NOTE]
    >Azure Machine Learning Compute är den enda hanterade beräknings resursen som du kan skapa i Azure Machine Learning Studio.  Alla andra beräknings resurser kan bifogas när de har skapats.

1. Fyll i formuläret. Ange värden för de obligatoriska egenskaperna, särskilt **VM-serien**och de **maximalt antal noder** som ska användas för att öka beräkningen.  

1. Välj __Skapa__.


1. Visa status för åtgärden Skapa genom att välja Compute-målet i listan:

    ![Välj ett beräknings mål för att visa status för att skapa åtgärd](./media/how-to-set-up-training-targets/View_list.png)

1. Sedan kan du se information om Compute-målet: 

    ![Visa information om dator mål](./media/how-to-set-up-training-targets/compute-target-details.png) 

### <a id="portal-reuse"></a>Bifoga Compute-mål

Om du vill använda beräknings mål som skapats utanför arbets ytan Azure Machine Learning måste du koppla dem. Om du kopplar ett beräknings mål blir det tillgängligt för din arbets yta.

Följ stegen som beskrivs ovan för att visa listan över beräknings mål. Använd sedan följande steg för att ansluta ett beräknings mål: 

1. Välj plus tecknet (+) för att lägga till ett beräknings mål. 
1. Ange ett namn för beräknings målet. 
1. Välj den typ av beräkning som ska bifogas för __träning__:

    > [!IMPORTANT]
    > Alla beräknings typer kan inte kopplas från Azure Machine Learning Studio. De beräknings typer som för närvarande kan bifogas för utbildning är:
    >
    > * En virtuell fjärrdator
    > * Azure Databricks (används i maskin inlärnings pipeliner)
    > * Azure Data Lake Analytics (används i maskin inlärnings pipeliner)
    > * Azure HDInsight

1. Fyll i formuläret och ange värden för de obligatoriska egenskaperna.

    > [!NOTE]
    > Microsoft rekommenderar att du använder SSH-nycklar, som är säkrare än lösen ord. Lösen ord är utsatta för angrepp med brute force. SSH-nycklar är beroende av kryptografiska signaturer. Information om hur du skapar SSH-nycklar för användning med Azure Virtual Machines finns i följande dokument:
    >
    > * [Skapa och använda SSH-nycklar på Linux eller macOS](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Skapa och Använd SSH-nycklar i Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. Välj __bifoga__. 
1. Visa status för åtgärden Anslut genom att välja Compute-målet i listan.

## <a name="set-up-with-cli"></a>Konfigurera med CLI

Du kan komma åt de beräknings mål som är kopplade till din arbets yta med [CLI-tillägget](reference-azure-machine-learning-cli.md) för Azure Machine Learning.  Du kan använda CLI för att:

* Skapa ett hanterat beräknings mål
* Uppdatera ett hanterat beräknings mål
* Koppla ett ohanterat beräknings mål

Mer information finns i [resurs hantering](reference-azure-machine-learning-cli.md#resource-management).

## <a name="set-up-with-vs-code"></a>Konfigurera med VS Code

Du kan komma åt, skapa och hantera de beräknings mål som är kopplade till din arbets yta med [vs Code-tillägget](tutorial-train-deploy-image-classification-model-vscode.md#configure-compute-targets) för Azure Machine Learning.

## <a id="submit"></a>Skicka utbildnings körning med hjälp av Azure Machine Learning SDK

När du har skapat en körnings konfiguration använder du den för att köra experimentet.  Kod mönstret för att skicka en tränings körning är detsamma för alla typer av beräknings mål:

1. Skapa ett experiment att köra
1. Skicka in körningen.
1. Vänta tills den kör för att slutföra.

> [!IMPORTANT]
> När du skickar utbildningen skapas en ögonblicks bild av den katalog som innehåller dina utbildnings skript och skickas till beräknings målet. Den lagras också som en del av experimentet i din arbets yta. Om du ändrar filer och skickar körningen igen kommer bara de ändrade filerna att överföras.
>
> Om du vill förhindra att filer tas med i ögonblicks bilden skapar du en [. gitignore](https://git-scm.com/docs/gitignore) -eller `.amlignore`-fil i katalogen och lägger till filerna i den. `.amlignore`-filen använder samma syntax och mönster som [. gitignore](https://git-scm.com/docs/gitignore) -filen. Om båda filerna finns har `.amlignore`-filen företräde.
> 
> Mer information finns i [ögonblicks bilder](concept-azure-machine-learning-architecture.md#snapshots).

### <a name="create-an-experiment"></a>Skapa ett experiment

Börja med att skapa ett experiment i din arbets yta.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=experiment)]

### <a name="submit-the-experiment"></a>Skicka experimentet

Skicka experimentet med ett `ScriptRunConfig`-objekt.  Det här objektet innehåller:

* **source_directory**: käll katalogen som innehåller ditt utbildnings skript
* **skript**: identifiera utbildnings skriptet
* **run_config**: kör konfigurationen, som i sin tur definierar var träningen ska ske.

Om du till exempel vill använda [den lokala mål](#local) konfigurationen:

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=local_submit)]

Växla samma experiment för att köra i ett annat beräknings mål genom att använda en annan körnings konfiguration, till exempel [amlcompute-målet](#amlcompute):

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=amlcompute_submit)]

> [!TIP]
> I det här exemplet används som standard bara en nod i Compute-målet för träning. Om du vill använda mer än en nod anger du `node_count` för körnings konfigurationen till önskat antal noder. Följande kod anger till exempel antalet noder som används för utbildning till fyra:
>
> ```python
> src.run_config.node_count = 4
> ```

Eller så kan du:

* Skicka experimentet med ett `Estimator`-objekt som visas i [träna ml-modeller med uppskattningar](how-to-train-ml-models.md).
* Skicka en HyperDrive-körning för inställning av min [parameter](how-to-tune-hyperparameters.md).
* Skicka ett experiment via [vs Code-tillägget](tutorial-train-deploy-image-classification-model-vscode.md#train-the-model).

Mer information finns i dokumentationen om [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) och [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) .

## <a name="create-run-configuration-and-submit-run-using-azure-machine-learning-cli"></a>Skapa kör konfiguration och skicka körning med Azure Machine Learning CLI

Du kan använda [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) och [Machine Learning CLI-tillägget](reference-azure-machine-learning-cli.md) för att skapa kör konfigurationer och skicka körningar på olika Compute-mål. Följande exempel förutsätter att du har en befintlig Azure Machine Learning-arbetsyta och att du har loggat in på Azure med `az login` CLI-kommandot. 

### <a name="create-run-configuration"></a>Skapa körnings konfiguration

Det enklaste sättet att skapa kör konfiguration är att navigera i mappen som innehåller dina Python-skript för Machine Learning och använda kommandot CLI

```azurecli
az ml folder attach
```

Det här kommandot skapar en undermapp `.azureml` som innehåller mallar kör konfigurationsfiler för olika beräknings mål. Du kan kopiera och redigera dessa filer för att anpassa konfigurationen, till exempel för att lägga till python-paket eller ändra Docker-inställningar.  

### <a name="structure-of-run-configuration-file"></a>Struktur för körnings konfigurations filen

Kör konfigurations filen är YAML formaterad, med följande avsnitt
 * Skriptet som ska köras och dess argument
 * Compute Target Name, antingen "Local" eller namnet på en beräkning under arbets ytan.
 * Parametrar för att köra körning: Framework, Communicator för distribuerade körningar, maximal varaktighet och antal datornoder.
 * Miljö avsnitt. Mer information om fälten i det här avsnittet finns i [skapa och hantera miljöer för utbildning och distribution](how-to-use-environments.md) .
   * Om du vill ange python-paket som ska installeras för kör, skapa [Conda-miljöfilen](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#create-env-file-manually)och ange __condaDependenciesFile__ -fältet.
 * Kör historik information för att ange loggmappen och aktivera eller inaktivera insamlingen av utdata och kör historik ögonblicks bilder.
 * Konfigurations information som är speciell för det valda ramverket.
 * Data referens och data lager information.
 * Konfigurations information som är speciell för Machine Learning-beräkning för att skapa ett nytt kluster.

Se JSON- [filen](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json) för ett fullständigt runconfig-schema.

### <a name="create-an-experiment"></a>Skapa ett experiment

Börja med att skapa ett experiment för dina körningar

```azurecli
az ml experiment create -n <experiment>
```

### <a name="script-run"></a>Skript körning

Kör ett kommando om du vill skicka en skript körning

```azurecli
az ml run submit-script -e <experiment> -c <runconfig> my_train.py
```

### <a name="hyperdrive-run"></a>HyperDrive körning

Du kan använda HyperDrive med Azure CLI för att utföra parameter justerings körningar. Börja med att skapa en HyperDrive-konfigurations fil i följande format. Se [Justera egenskaper för en modell](how-to-tune-hyperparameters.md) artikel för information om parametrar för parameter justering.

```yml
# hdconfig.yml
sampling: 
    type: random # Supported options: Random, Grid, Bayesian
    parameter_space: # specify a name|expression|values tuple for each parameter.
    - name: --penalty # The name of a script parameter to generate values for.
      expression: choice # supported options: choice, randint, uniform, quniform, loguniform, qloguniform, normal, qnormal, lognormal, qlognormal
      values: [0.5, 1, 1.5] # The list of values, the number of values is dependent on the expression specified.
policy: 
    type: BanditPolicy # Supported options: BanditPolicy, MedianStoppingPolicy, TruncationSelectionPolicy, NoTerminationPolicy
    evaluation_interval: 1 # Policy properties are policy specific. See the above link for policy specific parameter details.
    slack_factor: 0.2
primary_metric_name: Accuracy # The metric used when evaluating the policy
primary_metric_goal: Maximize # Maximize|Minimize
max_total_runs: 8 # The maximum number of runs to generate
max_concurrent_runs: 2 # The number of runs that can run concurrently.
max_duration_minutes: 100 # The maximum length of time to run the experiment before cancelling.
```

Lägg till den här filen tillsammans med konfigurationsfilerna för körning. Skicka sedan en HyperDrive-körning med:
```azurecli
az ml run submit-hyperdrive -e <experiment> -c <runconfig> --hyperdrive-configuration-name <hdconfig> my_train.py
```

Observera avsnittet *argument* i runconfig och *parameter utrymme* i HyperDrive config. De innehåller kommando rads argument som ska skickas till övnings skriptet. Värdet i runconfig förblir detsamma för varje iteration, medan intervallet i HyperDrive-config upprepas. Ange inte samma argument i båda filerna.

Mer information om dessa ```az ml``` CLI-kommandon och en fullständig uppsättning argument finns i [referens dokumentationen](reference-azure-machine-learning-cli.md).

<a id="gitintegration"></a>

## <a name="git-tracking-and-integration"></a>Git-spårning och integrering

När du startar en utbildning som kör där käll katalogen är en lokal git-lagringsplats, lagras information om lagrings platsen i körnings historiken. Mer information finns i [git-integrering för Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="notebook-examples"></a>Exempel på bärbara datorer

Se dessa antecknings böcker för exempel på utbildning med olika beräknings mål:
* [instruktion för att använda – azureml/utbildning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [Självstudier/img-Classification-part1-Training. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nästa steg

* [Självstudie: träna en modell](tutorial-train-models-with-aml.md) använder ett hanterat beräknings mål för att träna en modell.
* Lär dig hur du [effektivt justerar disponeringsparametrarna](how-to-tune-hyperparameters.md) för att bygga bättre modeller.
* När du har en tränad modell lär du dig [hur och var modeller ska distribueras](how-to-deploy-and-where.md).
* Visa SDK-referens för [RunConfiguration-klass](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) .
* [Använda Azure Machine Learning med virtuella Azure-nätverk](how-to-enable-virtual-network.md)
