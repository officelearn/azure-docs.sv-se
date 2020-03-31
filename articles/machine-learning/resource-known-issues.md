---
title: Kända problem & felsökning
titleSuffix: Azure Machine Learning
description: Få en lista över kända problem, lösningar och felsökning för Azure Machine Learning.
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: d5525c02edb30eff0ee8971a382f2acb8f2e57ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455731"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning"></a>Kända problem och felsökning av Azure Machine Learning

Den här artikeln hjälper dig att hitta och korrigera fel eller fel som påträffats när du använder Azure Machine Learning.

## <a name="sdk-installation-issues"></a>Problem med SDK-installation

**Felmeddelande: Det går inte att avinstallera "PyYAML"**

Azure Machine Learning SDK för Python: PyYAML är ett distutils installerat projekt. Därför kan vi inte exakt avgöra vilka filer som tillhör den om det finns en partiell avinstallation. Om du vill fortsätta installera SDK medan du ignorerar det här felet använder du:

```Python
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

**Felmeddelande:`ERROR: No matching distribution found for azureml-dataprep-native`**

Anacondas Python 3.7.4-distribution har en bugg som bryter azureml-sdk-installationen. Det här problemet beskrivs i det här [problemet med GitHub](https://github.com/ContinuumIO/anaconda-issues/issues/11195) Detta kan bearbetas genom att skapa en ny Conda-miljö med det här kommandot:
```bash
conda create -n <env-name> python=3.7.3
```
Vilket skapar en Conda-miljö med Python 3.7.3, som inte har installationsproblemet som finns i 3.7.4.

## <a name="training-and-experimentation-issues"></a>Utbildning och experimentfrågor

### <a name="metric-document-is-too-large"></a>Metriskt dokument är för stort
Azure Machine Learning har interna gränser för storleken på måttobjekt som kan loggas samtidigt från en utbildningskörning. Om du stöter på ett "Metriskt dokument är för stort" när du loggar ett listvärdesmått kan du prova att dela upp listan i mindre segment, till exempel:

```python
run.log_list("my metric name", my_metric[:N])
run.log_list("my metric name", my_metric[N:])
```

Internt sammanfogar Azure ML blocken med samma måttnamn i en sammanhängande lista.

### <a name="moduleerrors-no-module-named"></a>ModuleErrors (Ingen modul namngiven)
Om du stöter på ModuleErrors när du skickar in experiment i Azure ML betyder det att utbildningsskriptet förväntar sig att ett paket ska installeras men det läggs inte till. När du har ange paketnamnet installerar Azure ML paketet i den miljö som används för din träningskörning. 

Om du använder [Estimator för](concept-azure-machine-learning-architecture.md#estimators) att skicka experiment kan `pip_packages` `conda_packages` du ange ett paketnamn via eller parameter i uppskattningsmannen baserat på från vilken källa du vill installera paketet. Du kan också ange en yml-fil `conda_dependencies_file`med alla dina beroenden med eller `pip_requirements_file` lista alla dina pipkrav i en txt-fil med parametern. Om du har ett eget Azure ML Environment-objekt som du vill åsidosätta standardavbildningen `environment` som används av kalkylatorn kan du ange den miljön via parametern för uppskattningskonstruktorn.

Azure ML tillhandahåller också ramspecifika kalkylatorer för Tensorflow, PyTorch, Chainer och SKLearn. Med hjälp av dessa skattningsatorer kommer att se till att de viktigaste ramverksberoendena installeras för din räkning i den miljö som används för utbildning. Du har möjlighet att ange extra beroenden enligt beskrivningen ovan. 
 
Azure ML underhållna dockeravbildningar och deras innehåll kan ses i [AzureML Containers](https://github.com/Azure/AzureML-Containers).
Ramspecifika beroenden visas i respektive ramdokumentation - [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py#remarks), [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py#remarks), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py#remarks), [SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py#remarks).

> [!Note]
> Om du tror att ett visst paket är tillräckligt vanligt för att läggas till i Azure ML-underhållna avbildningar och miljöer kan du ta upp ett GitHub-problem i [AzureML Containers](https://github.com/Azure/AzureML-Containers). 
 
### <a name="nameerror-name-not-defined-attributeerror-object-has-no-attribute"></a>NameError (Namn har inte definierats), AttributeError (Objektet har inget attribut)
Det här undantaget bör komma från dina utbildningsskript. Du kan titta på loggfilerna från Azure-portalen för att få mer information om det specifika namn som inte har definierats eller attributfel. Från SDK kan du `run.get_details()` använda för att titta på felmeddelandet. Detta kommer också att lista alla loggfiler som genereras för din körning. Se till att ta en titt på ditt träningsskript och åtgärda felet innan du skickar in körningen igen. 

### <a name="horovod-has-been-shut-down"></a>Horovod har stängts av
I de flesta fall om du stöter på "AbortedError: Horovod har stängts av" innebär detta undantag att det fanns ett underliggande undantag i en av de processer som orsakade Horovod att stänga. Varje rang i MPI-jobbet får den egna dedikerade loggfilen i Azure ML. Dessa loggar heter `70_driver_logs`. Vid distribuerad utbildning suffixeras loggnamnen `_rank` med för att göra det lättare att skilja loggarna åt. För att hitta det exakta felet som orsakade Horovod att stänga `Traceback` av, gå igenom alla loggfiler och leta efter i slutet av driver_log filer. En av dessa filer ger dig det faktiska underliggande undantaget. 

### <a name="sr-iov-availability-on-ncv3-machines-in-amlcompute-for-distributed-training"></a>SR-IOV-tillgänglighet på NCv3-maskiner i AmlCompute för distribuerad utbildning
Azure Compute har rullat ut en [SR-IOV-uppgradering](https://azure.microsoft.com/updates/sriov-availability-on-ncv3-virtual-machines-sku/) av NCv3-datorer, som kunder kan utnyttja med Azure ML:s hanterade beräkningserbjudande (AmlCompute). Uppdateringarna kommer att möjliggöra stöd för hela MPI stacken och användningen av Infiniband RDMA-nätverk för förbättrad multi-nod distribuerad utbildning prestanda, särskilt för djupinlärning.

Visa [uppdateringsschemat](https://azure.microsoft.com/updates/sr-iov-availability-schedule-on-ncv3-virtual-machines-sku/) för att se när supporten ska distribueras för din region.

### <a name="run-or-experiment-deletion"></a>Kör eller experimentera borttagning
Experiment kan arkiveras med metoden [Experiment.archive](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#archive--) eller från fliken Experiment i Azure Machine Learning studio-klienten via knappen "Arkivexperiment". Den här åtgärden döljer experimentet från listfrågor och vyer, men tar inte bort det.

Permanent borttagning av enskilda experiment eller körningar stöds för närvarande inte. Mer information om hur du tar bort resurser för arbetsyta finns i [Exportera eller ta bort arbetsytedata för Machine Learning-tjänsten](how-to-export-delete-data.md).

## <a name="azure-machine-learning-compute-issues"></a>Beräkningsproblem för Azure Machine Learning
Kända problem med att använda Azure Machine Learning Compute (AmlCompute).

### <a name="trouble-creating-amlcompute"></a>Problem med att skapa AmlCompute

Det finns en sällsynt chans att vissa användare som har skapat sin Azure Machine Learning-arbetsyta från Azure-portalen innan GA-versionen kanske inte kan skapa AmlCompute på arbetsytan. Du kan antingen höja en supportbegäran mot tjänsten eller skapa en ny arbetsyta via portalen eller SDK för att häva blockeringen omedelbart.

### <a name="outage-sr-iov-upgrade-to-ncv3-machines-in-amlcompute"></a>Avbrott: SR-IOV uppgraderar till NCv3-maskiner i AmlCompute

Azure Compute kommer att uppdatera NCv3 SKU:er från början av november 2019 för att stödja alla MPI-implementeringar och versioner och RDMA-verb för InfiniBand-utrustade virtuella datorer. Detta kommer att kräva en kort driftstopp - [läs mer om SR-IOV uppgradering](https://azure.microsoft.com/updates/sriov-availability-on-ncv3-virtual-machines-sku).

Som kund hos Azure Machine Learnings hanterade beräkningserbjudande (AmlCompute) behöver du inte göra några ändringar just nu. Baserat på [uppdateringsschemat](https://azure.microsoft.com/updates/sr-iov-availability-schedule-on-ncv3-virtual-machines-sku) måste du planera för en kort paus i din träning. Tjänsten tar ansvar för att uppdatera vm-avbildningarna på klusternoderna och automatiskt skala upp klustret. När uppgraderingen är klar kanske du kan använda alla andra MPI-distributioner (som OpenMPI med Pytorch) förutom att få högre InfiniBand bandbredd, lägre latenser och bättre distribuerade programprestanda.

## <a name="azure-machine-learning-designer-issues"></a>Problem med Designern i Azure Machine Learning

Kända problem med designern.

### <a name="long-compute-preparation-time"></a>Lång beräkningsförberedelsetid

Skapa ny beräkning eller framkalla lämnar beräkning tar tid, kan vara några minuter eller ännu längre. Teamet arbetar för optimering.


### <a name="cannot-run-an-experiment-only-contains-a-dataset"></a>Det går inte att köra ett experiment som bara innehåller en datauppsättning 

Du kanske bara vill köra ett experiment innehåller datauppsättning för att visualisera datauppsättningen. Det är dock inte tillåtet att köra ett experiment innehåller bara datauppsättning idag. Vi håller aktivt på att lösa denna fråga.
 
Innan korrigeringen kan du ansluta datauppsättningen till valfri dataomvandlingsmodul (Välj kolumner i datauppsättning, Redigera metadata, dela data etc.) och köra experimentet. Sedan kan du visualisera datauppsättningen. 

Nedanstående bild ![visar hur: visulize-data](./media/resource-known-issues/aml-visualize-data.png)

## <a name="image-building-failure"></a>Fel på bildbygget

Fel på bildbygge vid distribution av webbtjänst. Lösning är att lägga till "pynacl==1.2.1" som pipberoende till Conda-fil för avbildningskonfiguration.

## <a name="deployment-failure"></a>Distributionsfel

Om du `['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`observerar ändrar du SKU för virtuella datorer som används i distributionen till en som har mer minne.

## <a name="fpgas"></a>FPGA:er

Du kommer inte att kunna distribuera modeller på FPGA förrän du har begärt och godkänts för FPGA-kvot. Om du vill begära åtkomst fyller du i formuläret för kvotbegäran:https://aka.ms/aml-real-time-ai

## <a name="automated-machine-learning"></a>Automatiserad maskininlärning

Tensor Flow Automatiserad maskininlärning stöder för närvarande inte tensorflöde version 1.13. Om du installerar den här versionen slutar paketberoenden att fungera. Vi arbetar för att åtgärda problemet i en framtida version.

### <a name="experiment-charts"></a>Experimentdiagram

Binära klassificeringsdiagram (precision-recall, ROC, gain curve etc.) som visas i automatiserade ML-experimentiterationer återges inte korrekt i användargränssnittet sedan 4/12. Diagramdiagram visar för närvarande omvända resultat, där bättre presterande modeller visas med lägre resultat. En resolution är under utredning.

## <a name="datasets-and-data-preparation"></a>Datamängder och databeredning

Dessa är kända problem för Azure Machine Learning Dataset.

### <a name="typeerror-filenotfound-no-such-file-or-directory"></a>TypeError: FileNotFound: Ingen sådan fil eller katalog

Det här felet uppstår om filsökvägen som du anger inte finns där filen finns. Du måste se till att hur du refererar till filen är förenligt med var du monterade datauppsättningen på beräkningsmålet. För att säkerställa ett deterministiskt tillstånd rekommenderar vi att du använder den abstrakta sökvägen när du monterar en datauppsättning till ett beräkningsmål. I följande kod monterar vi till exempel datauppsättningen under roten i `/tmp`beräkningsmålets filsystem. 

```python
# Note the leading / in '/tmp/dataset'
script_params = {
    '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
} 
```

Om du inte inkluderar det inledande snedstrecket framåt, "/", måste du `/mnt/batch/.../tmp/dataset` prefix arbetskatalogen, t.ex.

### <a name="fail-to-read-parquet-file-from-http-or-adls-gen-2"></a>Det går inte att läsa parquet-filen från HTTP eller ADLS Gen 2

Det finns ett känt problem i AzureML DataPrep SDK version 1.1.25 som orsakar ett fel när du skapar en datauppsättning genom att läsa Parquet-filer från HTTP eller ADLS Gen 2. Det kommer `Cannot seek once reading started.`att misslyckas med . Lös problemet genom att `azureml-dataprep` uppgradera till en version som är högre än 1.1.26 eller nedgradera till en version som är lägre än 1.1.24.

```python
pip install --upgrade azureml-dataprep
```

### <a name="typeerror-mount-got-an-unexpected-keyword-argument-invocation_id"></a>TypeError: mount() fick ett oväntat sökordsargument "invocation_id"

Det här felet uppstår om du `azureml-core` `azureml-dataprep`har en inkompatibel version mellan och . Om det här felet `azureml-dataprep` visas uppgraderar du paketet till en nyare version (större än eller lika med 1.1.29).

```python
pip install --upgrade azureml-dataprep
```

## <a name="databricks"></a>Databricks

Problem med Databricks och Azure Machine Learning.

### <a name="failure-when-installing-packages"></a>Fel vid installation av paket

Azure Machine Learning SDK-installationen misslyckas på Azure Databricks när fler paket installeras. Vissa paket, till `psutil`exempel , kan orsaka konflikter. För att undvika installationsfel installerar du paket genom att frysa biblioteksversionen. Det här problemet är relaterat till Databricks och inte till Azure Machine Learning SDK. Det här problemet kan också uppstå med andra bibliotek. Exempel:

```python
psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
```

Du kan också använda init-skript om du fortsätter att ställas inför installationsproblem med Python-bibliotek. Den här metoden stöds inte officiellt. Mer information finns [i Kluster-scoped init-skript](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts).

### <a name="cancel-an-automated-machine-learning-run"></a>Avbryta en automatisk maskininlärningskörning

När du använder automatiserade machine learning-funktioner på Azure Databricks, för att avbryta en körning och starta en ny experimentkörning, starta om ditt Azure Databricks-kluster.

### <a name="10-iterations-for-automated-machine-learning"></a>>10 iterationer för automatiserad maskininlärning

I automatiska maskininlärningsinställningar, om du har `show_output` `False` fler än 10 iterationer, inställd på när du skickar körningen.

### <a name="widget-for-the-azure-machine-learning-sdk-and-automated-machine-learning"></a>Widget för Azure Machine Learning SDK och automatiserad maskininlärning

Azure Machine Learning SDK-widgeten stöds inte i en Databricks-anteckningsbok eftersom anteckningsböckerna inte kan tolka HTML-widgetar. Du kan visa widgeten i portalen med den här Python-koden i din Azure Databricks-anteckningsbokscell:

```
displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
```

### <a name="import-error-cannot-import-name-timedelta-from-pandas_libstslibs"></a>Importfel: kan inte importera namnet "Timedelta" från "pandas._libs.tslibs"

Om det här felet visas när du använder automatisk maskininlärning kör du följande två rader i anteckningsboken:
```
%sh rm -rf /databricks/python/lib/python3.7/site-packages/pandas-0.23.4.dist-info /databricks/python/lib/python3.7/site-packages/pandas
%sh /databricks/python/bin/pip install pandas==0.23.4
```

### <a name="import-error-no-module-named-pandascoreindexes"></a>Importfel: Ingen modul med namnet 'pandas.core.indexes'

Om det här felet visas när du använder automatiserad maskininlärning:

1. Kör det här kommandot om du vill installera två paket i Azure Databricks-klustret:

   ```bash
   scikit-learn==0.19.1
   pandas==0.22.0
   ```

1. Koppla loss och sätt sedan tillbaka klustret i anteckningsboken.

Om de här stegen inte löser problemet kan du prova att starta om klustret.

### <a name="failtosendfeather"></a>FailToSendFeather

Om du `FailToSendFeather` ser ett fel när du läser data i Azure Databricks-klustret läser du följande lösningar:

* Uppgradera `azureml-sdk[automl]` paketet till den senaste versionen.
* Lägg `azureml-dataprep` till version 1.1.8 eller högre.
* Lägg `pyarrow` till version 0.11 eller högre.

## <a name="azure-portal"></a>Azure Portal

Om du går direkt för att visa arbetsytan från en delningslänk från SDK eller portalen kan du inte visa den vanliga översiktssidan med prenumerationsinformation i tillägget. Du kommer inte heller att kunna växla till en annan arbetsyta. Om du behöver visa en annan arbetsyta är lösningen att gå direkt till [Azure Machine Learning studio](https://ml.azure.com) och söka efter arbetsytans namn.

## <a name="diagnostic-logs"></a>Diagnostikloggar

Ibland kan det vara användbart om du kan ange diagnostikinformation när du ber om hjälp. Om du vill se några loggar besöker du [Azure Machine Learning studio](https://ml.azure.com) och går till arbetsytan och väljer Arbetsyta > Experiment > Kör > **loggar**.  

> [!NOTE]
> Azure Machine Learning loggar information från en mängd olika källor under utbildningen, till exempel AutoML eller Docker-behållaren som kör utbildningsjobbet. Många av dessa loggar är inte dokumenterade. Om du stöter på problem och kontaktar Microsoft-supporten kan de kanske använda dessa loggar under felsökningen.

## <a name="activity-logs"></a>Aktivitetsloggar

Vissa åtgärder på arbetsytan Azure Machine Learning loggar inte information till __aktivitetsloggen__. Till exempel starta en träningskörning eller registrera en modell.

Vissa av dessa åtgärder visas i området __Aktiviteter__ på arbetsytan, men de anger inte vem som initierade aktiviteten.

## <a name="resource-quotas"></a>Resurskvoter

Lär dig mer om [de resurskvoter](how-to-manage-quotas.md) du kan stöta på när du arbetar med Azure Machine Learning.

## <a name="authentication-errors"></a>Autentiseringsfel

Om du utför en hanteringsåtgärd på ett beräkningsmål från ett fjärrjobb visas något av följande fel:

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

Du får till exempel ett felmeddelande om du försöker skapa eller koppla ett beräkningsmål från en ML-pipeline som skickas för fjärrkörning.

## <a name="overloaded-azurefile-storage"></a>Överbelastad AzureFile-lagring

Om du får `Unable to upload project files to working directory in AzureFile because the storage is overloaded`ett felmeddelande använder du följande lösningar.

Om du använder filresurs för andra arbetsbelastningar, till exempel dataöverföring, är rekommendationen att använda blobbar så att filresursen är gratis att använda för att skicka körningar. Du kan också dela arbetsbelastningen mellan två olika arbetsytor.

## <a name="webservices-in-azure-kubernetes-service-failures"></a>Webbtjänster i Azure Kubernetes-tjänstfel

Många webbtjänstfel i Azure Kubernetes-tjänsten kan avsökas genom `kubectl`att ansluta till klustret med . Du kan `kubeconfig.json` hämta för ett Azure Kubernetes-tjänstkluster genom att köra

```azurecli-interactive
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="updating-azure-machine-learning-components-in-aks-cluster"></a>Uppdatera Azure Machine Learning-komponenter i AKS-kluster

Uppdateringar av Azure Machine Learning-komponenter som är installerade i ett Azure Kubernetes-tjänstkluster måste tillämpas manuellt. 

Du kan installera dessa uppdateringar genom att koppla från klustret från Azure Machine Learning-arbetsytan och sedan återansluta klustret till arbetsytan. Om SSL är aktiverat i klustret måste du ange SSL-certifikatet och den privata nyckeln när du sätter tillbaka klustret. 

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

Om du inte längre har SSL-certifikatet och den privata nyckeln, eller om du använder ett certifikat som genereras av Azure `kubectl` Machine Learning, kan `azuremlfessl`du hämta filerna innan du kopplar från klustret genom att ansluta till klustret med hjälp av och hämta hemligheten .

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes lagrar hemligheterna i base-64 kodat format. Du kommer att behöva basera-64 avkoda `cert.pem` och `key.pem` komponenter `attach_config.enable_ssl`i hemligheter innan de ger dem till . 

## <a name="labeling-projects-issues"></a>Märka projektfrågor

Kända problem med märkning av projekt.

### <a name="only-datasets-created-on-blob-datastores-can-be-used"></a>Endast datauppsättningar som skapas på blob-datalager kan användas

Detta är en känd begränsning av den aktuella versionen.

### <a name="after-creation-the-project-shows-initializing-for-a-long-time"></a>När projektet har skapats visas "Initiera" under lång tid

Uppdatera sidan manuellt. Initieringen bör fortsätta med ungefär 20 datapunkter per sekund. Bristen på autorefresh är ett känt problem. 

### <a name="when-reviewing-images-newly-labeled-images-are-not-shown"></a>När du granskar bilder visas inte nyligen märkta bilder

Om du vill läsa in alla märkta bilder väljer du knappen **Första.** Den **första** knappen tar dig tillbaka till framsidan av listan, men laddar alla märkta data.

### <a name="pressing-esc-key-while-labeling-for-object-detection-creates-a-zero-size-label-on-the-top-left-corner-submitting-labels-in-this-state-fails"></a>Om du trycker på Esc-tangenten när du etiketterar för objektidentifiering skapas en nollstorleksetikett i det övre vänstra hörnet. Det går inte att skicka etiketter i det här tillståndet.

Ta bort etiketten genom att klicka på korsmarkeringen bredvid den.

## <a name="moving-the-workspace"></a>Flytta arbetsytan

> [!WARNING]
> Det går inte att flytta din Azure Machine Learning-arbetsyta till en annan prenumeration, eller flytta den ägande prenumerationen till en ny klientorganisation. Om du gör det kan det orsaka fel.
