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
ms.date: 08/09/2019
ms.custom: seodec18
ms.openlocfilehash: b69eda59c9c8032510df036d3aa0d160105fbc16
ms.sourcegitcommit: 6eecb9a71f8d69851bc962e2751971fccf29557f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2019
ms.locfileid: "72533173"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning"></a>Kända problem och fel söknings Azure Machine Learning

Den här artikeln hjälper dig att hitta och korrigera fel eller fel som uppstår när du använder Azure Machine Learning.

## <a name="visual-interface-issues"></a>Problem med visuella gränssnitt

Visuellt gränssnitt för Machine Learning service-problem.

### <a name="long-compute-preparation-time"></a>Förberedelse tid för lång beräkning

Det kan ta några minuter eller till och med längre tid att skapa en ny beräknings-eller använda. Teamet arbetar för optimering.


### <a name="cannot-run-an-experiment-only-contains-dataset"></a>Det går inte att köra ett experiment som bara innehåller data uppsättning 

Du kanske vill köra ett experiment endast innehåller data uppsättning för att visualisera data uppsättningen. Men det är inte tillåtet att köra ett experiment som bara innehåller data uppsättningar idag. Vi åtgärdar det här problemet aktivt.
 
Före korrigeringen kan du ansluta data uppsättningen till en datatransformerings-modul (Välj kolumner i data uppsättning, redigera metadata, dela data osv.) och köra experimentet. Sedan kan du visualisera data uppsättningen. 

Bilden nedan visar hur: ![visulize-data ](./media/resource-known-issues/aml-visualize-data.png)

## <a name="sdk-installation-issues"></a>Installations problem för SDK

**Fel meddelande: det går inte att avinstallera ' PyYAML '**

Azure Machine Learning SDK för python: PyYAML är ett distutils-installerat projekt. Därför kan vi inte korrekt avgöra vilka filer som tillhör den om det finns en delvis avinstallation. Om du vill fortsätta att installera SDK och ignorera det här felet använder du:

```Python
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

**Felmeddelande: `ERROR: No matching distribution found for azureml-dataprep-native`**

Anacondas python 3.7.4-distribution har ett fel som bryter azureml-SDK-installationen. Det här problemet beskrivs i det här [GitHub-problemet](https://github.com/ContinuumIO/anaconda-issues/issues/11195) som kan åtgärdas genom att skapa en ny Conda-miljö med det här kommandot:
```bash
conda create -n <env-name> python=3.7.3
```
Som skapar en Conda-miljö med python 3.7.3, som inte har installations problemet som finns i 3.7.4.

## <a name="trouble-creating-azure-machine-learning-compute"></a>Problem med att skapa Azure Machine Learning Compute

Det är en sällsynt risk att vissa användare som har skapat sin Azure Machine Learning-arbetsyta från Azure Portal innan GA-versionen inte kan skapa Azure Machine Learning beräkning på den arbets ytan. Du kan antingen utlösa en supportbegäran mot tjänsten eller skapa en ny arbets yta via portalen eller SDK för att häva blockeringen direkt.

## <a name="image-building-failure"></a>Det gick inte att skapa bild

Det gick inte att skapa bild när du distribuerar webb tjänsten. Lösning är att lägga till "pynacl = = 1.2.1" som ett pip-beroende av Conda-filen för avbildnings konfiguration.

## <a name="deployment-failure"></a>Distributions problem

Om du ser `['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>` ändrar du SKU: n för virtuella datorer som används i distributionen till en som har mer minne.

## <a name="fpgas"></a>FPGA:er

Du kommer inte att kunna distribuera modeller på FPGAs förrän du har begärt och godkänts för FPGA-kvoten. Om du vill begära åtkomst fyller du i formuläret kvot förfrågan: https://aka.ms/aml-real-time-ai

## <a name="automated-machine-learning"></a>Automatiserad maskininlärning

Den automatiska maskin inlärningen i styrkorts flöde stöder för närvarande inte flödes flödes version 1,13. Om den här versionen installeras kommer paket beroenden att sluta fungera. Vi arbetar för att åtgärda det här problemet i en framtida version. 

### <a name="experiment-charts"></a>Experiment diagram

Binära klassificerings diagram (precisions återkallning, ROC, kurva osv.) som visas i automatiserade ML experiment-iterationer återges inte korrekt i användar gränssnittet sedan 4/12. Diagram observationer visar för närvarande inversa resultat, där modeller med bättre prestanda visas med lägre resultat. En lösning är under undersökning.

## <a name="databricks"></a>Databricks

Databricks-och Azure Machine Learning problem.

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

Om du ser ett `FailToSendFeather`-fel vid läsning av data på Azure Databricks-kluster, se följande lösningar:

* Uppgradera `azureml-sdk[automl]`-paketet till den senaste versionen.
* Lägg till `azure-dataprep` version 1.1.8 eller senare.
* Lägg till `pyarrow` version 0,11 eller senare.

## <a name="azure-portal"></a>Azure portal

Om du går direkt till att visa din arbets yta från en resurs länk från SDK eller portalen, kan du inte visa sidan för normal översikt med prenumerations information i tillägget. Du kommer inte heller att kunna byta till en annan arbets yta. Om du behöver visa en annan arbets yta är lösningen att gå direkt till [Azure Portal](https://portal.azure.com) och söka efter namnet på arbets ytan.

## <a name="diagnostic-logs"></a>Diagnostikloggar

Ibland kan det vara bra om du kan ange diagnostikinformation när du ber om hjälp. Om du vill se några loggar går du till [Azure Portal](https://portal.azure.com) och går till din arbets yta och väljer **arbets yta > Experiment > Kör > loggar**.  Du kan också hitta den här informationen i avsnittet **experiment** i [landnings sidan för arbets ytan (för hands version)](https://ml.azure.com).

> [!NOTE]
> Azure Machine Learning loggar information från en rad olika källor under utbildningen, till exempel AutoML eller Docker-behållaren som kör övnings jobbet. Många av dessa loggar dokumenteras inte. Om du stöter på problem och kontaktar Microsoft-supporten kan det hända att de kan använda dessa loggar under fel sökning.

## <a name="activity-logs"></a>Aktivitetsloggar

Vissa åtgärder i Azure Machine Learning-arbetsytan loggar inte information i __aktivitets loggen__. Du kan till exempel starta en utbildning för att köra eller registrera en modell.

Några av de här åtgärderna visas i området __aktiviteter__ i din arbets yta, men de visar inte vem som initierade aktiviteten.

## <a name="resource-quotas"></a>Resurskvoter

Lär dig mer om [resurs kvoter](how-to-manage-quotas.md) som du kan stöta på när du arbetar med Azure Machine Learning.

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

Om du använder [uppskattningar](https://docs.microsoft.com/en-us/azure/machine-learning/service/concept-azure-machine-learning-architecture#estimators) för att skicka experiment kan du ange ett paket namn via `pip_packages` eller `conda_packages` parameter i uppskattningen baserat på från vilken källa du vill installera paketet. Du kan också ange en YML-fil med alla dina beroenden med `conda_dependencies_file`or lista alla dina pip-krav i en txt-fil med hjälp av `pip_requirements_file` parameter.

Azure ML tillhandahåller också Ramverks uppskattningar för Tensorflow, PyTorch, Kedjorer och SKLearn. Genom att använda dessa uppskattningar ser du till att Ramverks beroenden är installerade på din räkning i miljön som används för utbildning. Du kan välja att ange extra beroenden enligt beskrivningen ovan. 
 
 Azure ML-underhållna Docker-avbildningar och deras innehåll kan visas i [azureml-behållare](https://github.com/Azure/AzureML-Containers).
Ramverks beroenden visas i respektive ramverk dokumentation – [kedjor](https://docs.microsoft.com/en-us/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py#remarks), [PyTorch](https://docs.microsoft.com/en-us/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py#remarks), [TensorFlow](https://docs.microsoft.com/en-us/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py#remarks), [SKLearn](https://docs.microsoft.com/en-us/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py#remarks).

>[OBS!] Om du tror att ett visst paket är tillräckligt vanligt för att läggas till i Azure ML-underhållna bilder och miljöer kan du generera ett GitHub-problem i [azureml-behållare](https://github.com/Azure/AzureML-Containers). 
 
 ### <a name="nameerror-name-not-defined-attributeerror-object-has-no-attribute"></a>NameError (namn har inte definierats), AttributeError (objektet har inget attribut)
Detta undantag bör komma från dina utbildnings skript. Du kan titta på loggfilerna från Azure Portal för att få mer information om det angivna namnet inte är definierat eller ett attributvärde. Från SDK kan du använda `run.get_details()` för att titta på fel meddelandet. Detta visar även alla loggfiler som genererats för din körning. Se till att ta en titt på ditt utbildnings skript och åtgärda felet innan du försöker igen. 

### <a name="horovod-is-shutdown"></a>Horovod är avstängd
I de flesta fall innebär detta undantag att det uppstod ett underliggande undantag i en av de processer som orsakade att horovod stängdes av. Varje rang i MPI-jobbet hämtar den egna dedikerade logg filen i Azure ML. De här loggarna heter `70_driver_logs`. I händelse av distribuerad utbildning suffixs logg namnen med `_rank` för att göra det enkelt att skilja loggarna åt. Om du vill hitta det exakta fel som orsakade horovod avstängning går du igenom alla loggfiler och letar efter `Traceback` i slutet av driver_log-filerna. Med en av de här filerna får du det faktiska underliggande undantaget. 
