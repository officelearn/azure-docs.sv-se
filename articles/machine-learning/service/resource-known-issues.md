---
title: Kända problem & fel sökning
titleSuffix: Azure Machine Learning
description: Hämta en lista över kända problem, lösningar och fel sökning för Azure Machine Learning.
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: c16abd02dfef5fb8b74cd5c0cafa97e5f29cc6b2
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74286985"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning"></a>Kända problem och fel söknings Azure Machine Learning

Den här artikeln hjälper dig att hitta och korrigera fel eller fel som uppstår när du använder Azure Machine Learning.

## <a name="outage-sr-iov-upgrade-to-ncv3-machines-in-amlcompute"></a>Avbrott: SR-IOV-uppgradering till NCv3-datorer i AmlCompute

Azure Compute uppdaterar NCv3 SKU: er som startar tidigt november 2019 för att ge stöd för alla MPI-implementeringar och-versioner samt RDMA-verb för InfiniBand-utrustade virtuella datorer. Detta kräver kort stillestånds tid – [Läs mer om SR-IOV-uppgraderingen](https://azure.microsoft.com/updates/sriov-availability-on-ncv3-virtual-machines-sku).

Som kund av Azure Machine Learnings hanterade Compute-erbjudande (AmlCompute) behöver du inte göra några ändringar för tillfället. Utifrån [uppdaterings schemat](https://azure.microsoft.com/updates/sr-iov-availability-schedule-on-ncv3-virtual-machines-sku) skulle du behöva planera för en kort rast i utbildningen. Tjänsten tar ansvar för att uppdatera VM-avbildningarna på klusternoderna och skala automatiskt upp klustret. När uppgraderingen är klar kan du använda alla andra MPI-distributioner (t. ex. OpenMPI med Pytorch), förutom att få större InfiniBand-bandbredd, lägre fördröjning och bättre prestanda för distribuerade program.

## <a name="azure-machine-learning-designer-issues"></a>Problem med Azure Machine Learning designer

Kända problem med designern.

### <a name="long-compute-preparation-time"></a>Förberedelse tid för lång beräkning

Det kan ta några minuter eller till och med längre tid att skapa en ny beräknings-eller använda. Teamet arbetar för optimering.


### <a name="cannot-run-an-experiment-only-contains-a-dataset"></a>Det går inte att köra ett experiment som bara innehåller en data uppsättning 

Du kanske vill köra ett experiment endast innehåller data uppsättning för att visualisera data uppsättningen. Men det är inte tillåtet att köra ett experiment som bara innehåller data uppsättningar idag. Vi åtgärdar det här problemet aktivt.
 
Före korrigeringen kan du ansluta data uppsättningen till en datatransformerings-modul (Välj kolumner i data uppsättning, redigera metadata, dela data osv.) och köra experimentet. Sedan kan du visualisera data uppsättningen. 

Bilden nedan visar hur: ![visulize-data](./media/resource-known-issues/aml-visualize-data.png)

## <a name="sdk-installation-issues"></a>SDK-installationsproblem

**Felmeddelande: Det går inte att avinstallera 'PyYAML'**

Azure Machine Learning-SDK för Python: PyYAML är ett projekt för distutils installerad. Därför kan vi inte korrekt avgöra vilka filer som tillhör den om det finns en delvis avinstallation. Om du vill fortsätta installerar denna SDK när du ignorera det här felet, använder du:

```Python
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

**Felmeddelande: `ERROR: No matching distribution found for azureml-dataprep-native`**

Anacondas python 3.7.4-distribution har ett fel som bryter azureml-SDK-installationen. Det här problemet beskrivs i det här [GitHub-problemet](https://github.com/ContinuumIO/anaconda-issues/issues/11195) som kan åtgärdas genom att skapa en ny Conda-miljö med det här kommandot:
```bash
conda create -n <env-name> python=3.7.3
```
Som skapar en Conda-miljö med python 3.7.3, som inte har installations problemet som finns i 3.7.4.

## <a name="trouble-creating-azure-machine-learning-compute"></a>Problem med att skapa beräkning av Azure Machine Learning

Det finns en ovanligt risk att vissa användare som skapade sin Azure Machine Learning-arbetsyta från Azure-portalen innan GA-versionen kan inte skapa beräkning av Azure Machine Learning på arbetsytan. Du kan generera en supportförfrågan mot tjänsten, eller så kan du skapa en ny arbetsyta via portalen eller SDK, för att avblockera själv omedelbart.

## <a name="image-building-failure"></a>Bild byggnad fel

Bild för att skapa fel när du distribuerar webbtjänsten. Lösningen är att lägga till ”pynacl == 1.2.1” som ett pip beroende till Conda-fil för konfiguration av avbildningen.

## <a name="deployment-failure"></a>Distributions problem

Om du ser `['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`ändrar du SKU: n för virtuella datorer som används i distributionen till en som har mer minne.

## <a name="fpgas"></a>FPGA:er

Du kommer inte att kunna distribuera modeller på FPGA förrän du har begärt och godkänts för FPGA kvot. För att begära åtkomst, fyller du i formuläret för begäran av kvot: https://aka.ms/aml-real-time-ai

## <a name="automated-machine-learning"></a>Automatiserad maskininlärning

Den automatiska maskin inlärningen i styrkorts flöde stöder för närvarande inte flödes flödes version 1,13. Om den här versionen installeras kommer paket beroenden att sluta fungera. Vi arbetar för att åtgärda det här problemet i en framtida version. 

### <a name="experiment-charts"></a>Experiment diagram

Binära klassificerings diagram (precisions återkallning, ROC, kurva osv.) som visas i automatiserade ML experiment-iterationer återges inte korrekt i användar gränssnittet sedan 4/12. Diagram observationer visar för närvarande inversa resultat, där modeller med bättre prestanda visas med lägre resultat. En lösning är under undersökning.

## <a name="datasets-and-data-preparation"></a>Data uppsättningar och förberedelse av data

Detta är kända problem för Azure Machine Learning data uppsättningar.

### <a name="fail-to-read-parquet-file-from-http-or-adls-gen-2"></a>Det gick inte att läsa Parquet-filen från HTTP eller ADLS gen 2

Det finns ett känt problem i AzureML nu SDK version 1.1.25 som orsakar ett fel när du skapar en data uppsättning genom att läsa Parquet-filer från HTTP eller ADLS gen 2. Det går inte att `Cannot seek once reading started.`. Åtgärda problemet genom att uppgradera `azureml-dataprep` till en högre version än 1.1.26 eller nedgradera till en lägre version än 1.1.24.

```python
pip install --upgrade azureml-dataprep
```

### <a name="typeerror-mount-got-an-unexpected-keyword-argument-invocation_id"></a>TypeError: Mount () fick ett oväntat nyckelords argument invocation_id

Det här felet uppstår om du har en inkompatibel version mellan `azureml-core` och `azureml-dataprep`. Om det här felet visas uppgraderar du `azureml-dataprep`-paketet till en nyare version (större än eller lika med 1.1.29).

```python
pip install --upgrade azureml-dataprep
```

## <a name="databricks"></a>Databricks

Databricks och Azure Machine Learning-problem.

### <a name="failure-when-installing-packages"></a>Det gick inte att installera paket

Azure Machine Learning SDK-installationen Miss lyckas på Azure Databricks när fler paket är installerade. Vissa paket, till exempel `psutil`, kan orsaka konflikter. Undvik installations fel genom att installera paket genom att frysa biblioteks versionen. Det här problemet är relaterat till Databricks och inte till Azure Machine Learning SDK. Du kan också uppleva det här problemet med andra bibliotek. Exempel:

```python
psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
```

Du kan också använda init-skript om du behåller problem med att installera mot python-bibliotek. Den här metoden stöds inte officiellt. Mer information finns i [kluster omfång init-skript](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts).

### <a name="cancel-an-automated-machine-learning-run"></a>Avbryta en automatiserad Machine Learning-körning

När du använder automatiserade maskin inlärnings funktioner på Azure Databricks för att avbryta körningen och starta en ny experiment körning startar du om Azure Databricks klustret.

### <a name="10-iterations-for-automated-machine-learning"></a>> 10 iterationer för automatisk maskin inlärning

I automatiska inställningar för maskin inlärning, om du har fler än 10 iterationer, ställer du in `show_output` till `False` när du skickar körningen.

### <a name="widget-for-the-azure-machine-learning-sdkautomated-machine-learning"></a>Widget för Azure Machine Learning SDK/Automatisk maskin inlärning

Widgeten Azure Machine Learning SDK stöds inte i en Databricks Notebook eftersom antecknings böckerna inte kan parsa HTML-widgetar. Du kan visa widgeten i portalen genom att använda den här python-koden i din Azure Databricks Notebook-cell:

```
displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
```

### <a name="import-error-no-module-named-pandascoreindexes"></a>Import fel: ingen modul med namnet Pandas. Core. indexs

Om du ser det här felet när du använder automatisk maskin inlärning:

1. Kör det här kommandot för att installera två paket i Azure Databricks klustret: 

   ```
   scikit-learn==0.19.1
   pandas==0.22.0
   ```

1. Koppla från och återanslut sedan klustret till din bärbara dator. 

Om de här stegen inte löser problemet kan du försöka med att starta om klustret.

### <a name="failtosendfeather"></a>FailToSendFeather

Om du ser ett `FailToSendFeather` fel när du läser data på Azure Databricks kluster kan du läsa följande lösningar:

* Uppgradera `azureml-sdk[automl]`-paketet till den senaste versionen.
* Lägg till `azureml-dataprep` version 1.1.8 eller senare.
* Lägg till `pyarrow` version 0,11 eller senare.

## <a name="azure-portal"></a>Azure portal

Om du går direkt för att visa din arbetsyta från en delningslänk från SDK: N eller portalen kan du inte visa normala översikt översiktssidan med prenumerationsinformation i tillägget. Du kommer inte heller att kunna växla till en annan arbetsyta. Om du behöver visa en annan arbets yta är lösningen att gå direkt till [Azure Machine Learning Studio](https://ml.azure.com) och söka efter namnet på arbets ytan.

## <a name="diagnostic-logs"></a>Diagnostikloggar

Ibland kan det vara bra om du kan ange diagnostisk information när du frågar om du behöver hjälp. Om du vill se några loggar går du till [Azure Machine Learning Studio](https://ml.azure.com) och går till din arbets yta och väljer **arbets yta > Experiment > Kör > loggar**.  

> [!NOTE]
> Azure Machine Learning loggar information från en rad olika källor under utbildningen, till exempel AutoML eller Docker-behållaren som kör övnings jobbet. Många av dessa loggar dokumenteras inte. Om du stöter på problem och kontaktar Microsoft-supporten kan det hända att de kan använda dessa loggar under fel sökning.

## <a name="activity-logs"></a>Aktivitetsloggar

Vissa åtgärder i Azure Machine Learning-arbetsytan loggar inte information i __aktivitets loggen__. Du kan till exempel starta en utbildning för att köra eller registrera en modell.

Några av de här åtgärderna visas i området __aktiviteter__ i din arbets yta, men de visar inte vem som initierade aktiviteten.

## <a name="resource-quotas"></a>Resurskvoter

Lär dig mer om den [resurskvoter](how-to-manage-quotas.md) som kan uppstå när du arbetar med Azure Machine Learning.

## <a name="authentication-errors"></a>Autentiseringsfel

Om du utför en hanterings åtgärd på ett beräknings mål från ett Fjärrjobb får du ett av följande fel:

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

Till exempel visas ett fel meddelande om du försöker skapa eller ansluta ett beräknings mål från en ML-pipeline som skickas för fjärrkörning.

## <a name="overloaded-azurefile-storage"></a>Överlagrad AzureFile-lagring

Använd följande lösningar om du får ett fel meddelande `Unable to upload project files to working directory in AzureFile because the storage is overloaded`.

Om du använder fil resurs för andra arbets belastningar, till exempel data överföring, är rekommendationen att använda blobbar så att fil resursen är kostnads fri att användas för att skicka körningar. Du kan också dela upp arbets belastningen mellan två olika arbets ytor.

## <a name="webservices-in-azure-kubernetes-service-failures"></a>Webservice i Azure Kubernetes service-problem 

Många WebService-fel i Azure Kubernetes-tjänsten kan felsökas genom att ansluta till klustret med hjälp av `kubectl`. Du kan hämta `kubeconfig.json` för ett Azure Kubernetes service-kluster genom att köra

```bash
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="updating-azure-machine-learning-components-in-aks-cluster"></a>Uppdatera Azure Machine Learning-komponenter i AKS-kluster

Uppdateringar av Azure Machine Learning-komponenter som är installerade i ett Azure Kubernetes service-kluster måste appliceras manuellt. 

> [!WARNING]
> Innan du utför följande åtgärder kontrollerar du versionen av Azure Kubernetes service-klustret. Om kluster versionen är lika med eller större än 1,14 kommer du inte att kunna ansluta klustret till Azure Machine Learning-arbetsytan.

Du kan tillämpa dessa uppdateringar genom att koppla från klustret från arbets ytan Azure Machine Learning och sedan koppla klustret till arbets ytan igen. Om SSL är aktiverat i klustret måste du ange SSL-certifikatet och den privata nyckeln när du ansluter klustret igen. 

```python
compute_target = ComputeTarget(workspace=ws, name=clusterWorkspaceName)
compute_target.detach()
compute_target.wait_for_completion(show_output=True)

attach_config = AksCompute.attach_configuration(resource_group=resourceGroup, cluster_name=kubernetesClusterName)

## If SSL is enabled.
attach_config.enable_ssl(
    ssl_cert_pem_file="cert.pem",
    ssl_key_pem_file="key.pem",
    ssl_cname=sslCname)

attach_config.validate_configuration()

compute_target = ComputeTarget.attach(workspace=ws, name=args.clusterWorkspaceName, attach_configuration=attach_config)
compute_target.wait_for_completion(show_output=True)
```

Om du inte längre har SSL-certifikatet och den privata nyckeln, eller om du använder ett certifikat som har genererats av Azure Machine Learning, kan du hämta filerna innan du kopplar från klustret genom att ansluta till klustret med `kubectl` och hämta hemligheten `azuremlfessl`.

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes lagrar hemligheterna i Base-64-kodat format. Du måste basera-64-avkoda `cert.pem` och `key.pem` komponenter i hemligheterna innan de kan `attach_config.enable_ssl`. 

## <a name="recommendations-for-error-fix"></a>Rekommendationer för fel korrigering
Här följer Azure ML-rekommendationer för att åtgärda några av de vanliga felen i Azure ML, baserat på allmän observation.

### <a name="moduleerrors-no-module-named"></a>ModuleErrors (ingen modul med namnet)
Om du kör i ModuleErrors när du skickar experiment i Azure ML, innebär det att utbildnings skriptet förväntar sig att ett paket ska installeras men inte läggs till. När du har angett paket namnet kommer Azure ML att installera paketet i den miljö som används för din utbildning. 

Om du använder [uppskattningar](concept-azure-machine-learning-architecture.md#estimators) för att skicka experiment kan du ange ett paket namn via `pip_packages` eller `conda_packages` parameter i uppskattningen baserat på från vilken källa du vill installera paketet. Du kan också ange en YML-fil med alla dina beroenden med `conda_dependencies_file`eller lista alla dina pip-krav i en txt-fil med hjälp av `pip_requirements_file` parameter.

Azure ML tillhandahåller också Ramverks uppskattningar för Tensorflow, PyTorch, Kedjorer och SKLearn. Genom att använda dessa uppskattningar ser du till att Ramverks beroenden är installerade på din räkning i miljön som används för utbildning. Du kan välja att ange extra beroenden enligt beskrivningen ovan. 
 
 Azure ML-underhållna Docker-avbildningar och deras innehåll kan visas i [azureml-behållare](https://github.com/Azure/AzureML-Containers).
Ramverks beroenden visas i respektive ramverk dokumentation – [kedjor](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py#remarks), [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py#remarks), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py#remarks), [SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py#remarks).

>[OBS!] Om du tror att ett visst paket är tillräckligt vanligt för att läggas till i Azure ML-underhållna bilder och miljöer kan du generera ett GitHub-problem i [azureml-behållare](https://github.com/Azure/AzureML-Containers). 
 
 ### <a name="nameerror-name-not-defined-attributeerror-object-has-no-attribute"></a>NameError (namn har inte definierats), AttributeError (objektet har inget attribut)
Detta undantag bör komma från dina utbildnings skript. Du kan titta på loggfilerna från Azure Portal för att få mer information om det angivna namnet inte är definierat eller ett attributvärde. Från SDK kan du använda `run.get_details()` för att titta på fel meddelandet. Detta visar även alla loggfiler som genererats för din körning. Se till att ta en titt på ditt utbildnings skript och åtgärda felet innan du försöker igen. 

### <a name="horovod-is-shutdown"></a>Horovod är avstängd
I de flesta fall innebär detta undantag att det uppstod ett underliggande undantag i en av de processer som orsakade att horovod stängdes av. Varje rang i MPI-jobbet hämtar den egna dedikerade logg filen i Azure ML. De här loggarna heter `70_driver_logs`. I händelse av distribuerad utbildning suffixs logg namnen med `_rank` för att göra det enkelt att skilja loggarna åt. Om du vill hitta det exakta fel som orsakade horovod avstängning går du igenom alla loggfiler och letar efter `Traceback` i slutet av driver_log-filerna. Med en av de här filerna får du det faktiska underliggande undantaget. 

## <a name="labeling-projects-issues"></a>Problem med att märka projekt

Kända problem med att märka projekt.

### <a name="only-datasets-created-on-blob-datastores-can-be-used"></a>Det går bara att använda data uppsättningar som skapats på BLOB-datalager

Detta är en känd begränsning i den aktuella versionen. 

### <a name="after-creation-the-project-shows-initializing-for-a-long-time"></a>När projektet har skapats visar projektet "initiera" under en längre tid

Uppdatera sidan manuellt. Initieringen bör fortsätta ungefär 20 Datapoints per sekund. Bristen på Autouppdatering är ett känt problem. 

### <a name="bounding-box-cannot-be-drawn-all-the-way-to-right-edge-of-image"></a>Det går inte att rita en markerings ruta på samma sätt som bildens högra kant 

Försök att ändra storlek på webbläsarfönstret. Vi undersöker för att ta reda på orsaken till det här problemet. 

### <a name="when-reviewing-images-newly-labeled-images-are-not-shown"></a>När du visar bilder visas nyligen märkta bilder inte

Om du vill läsa in alla märkta bilder väljer du den **första** knappen. Den **första** knappen tar dig tillbaka till början av listan, men läser in alla etiketterade data.
