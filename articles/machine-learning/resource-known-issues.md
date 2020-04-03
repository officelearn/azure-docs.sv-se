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
ms.date: 03/31/2020
ms.openlocfilehash: 2db7a25f3f463e9210544354395c9d33a75f633c
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619380"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning"></a>Kända problem och felsökning av Azure Machine Learning

Den här artikeln hjälper dig att hitta och korrigera fel eller fel som du kan stöta på när du använder Azure Machine Learning.

## <a name="diagnostic-logs"></a>Diagnostikloggar

Ibland kan det vara användbart om du kan ange diagnostikinformation när du ber om hjälp. Så här visar du några loggar: 
1. Besök [Azure Machine Learning studio](https://ml.azure.com). 
1. Välj **Experimentera** till vänster 
1. Välj ett experiment.
1. Välj en körning.
1. Högst upp väljer du **Utdata + loggar**.

> [!NOTE]
> Azure Machine Learning loggar information från en mängd olika källor under utbildningen, till exempel AutoML eller Docker-behållaren som kör utbildningsjobbet. Många av dessa loggar är inte dokumenterade. Om du stöter på problem och kontaktar Microsoft-supporten kan de kanske använda dessa loggar under felsökningen.


## <a name="resource-quotas"></a>Resurskvoter

Lär dig mer om [de resurskvoter](how-to-manage-quotas.md) du kan stöta på när du arbetar med Azure Machine Learning.

## <a name="installation-and-import"></a>Installation och import

* **Felmeddelande: Det går inte att avinstallera "PyYAML"**

    Azure Machine Learning SDK för Python: `distutils` PyYAML är ett installerat projekt. Därför kan vi inte exakt avgöra vilka filer som tillhör den om det finns en partiell avinstallation. Om du vill fortsätta installera SDK medan du ignorerar det här felet använder du:
    
    ```Python
    pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
    ```

* **Databricks-fel vid installation av paket**

    Azure Machine Learning SDK-installationen misslyckas på Azure Databricks när fler paket installeras. Vissa paket, till `psutil`exempel , kan orsaka konflikter. För att undvika installationsfel installerar du paket genom att frysa biblioteksversionen. Det här problemet är relaterat till Databricks och inte till Azure Machine Learning SDK. Det här problemet kan också uppstå med andra bibliotek. Exempel:
    
    ```python
    psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
    ```

    Du kan också använda init-skript om du fortsätter att ställas inför installationsproblem med Python-bibliotek. Den här metoden stöds inte officiellt. Mer information finns [i Kluster-scoped init-skript](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts).

* **Databricks importfel: kan inte importera namnet "Timedelta" från "pandas._libs.tslibs":** Om du ser det här felet när du använder automatiserad maskininlärning kör du följande två rader i anteckningsboken:
    ```
    %sh rm -rf /databricks/python/lib/python3.7/site-packages/pandas-0.23.4.dist-info /databricks/python/lib/python3.7/site-packages/pandas
    %sh /databricks/python/bin/pip install pandas==0.23.4
    ```

* **Databricks import fel: Ingen modul som heter "pandas.core.indexes":** Om du ser detta fel när du använder automatiserad maskininlärning:

    1. Kör det här kommandot om du vill installera två paket i Azure Databricks-klustret:
    
       ```bash
       scikit-learn==0.19.1
       pandas==0.22.0
       ```
    
    1. Koppla loss och sätt sedan tillbaka klustret i anteckningsboken.
    
    Om de här stegen inte löser problemet kan du prova att starta om klustret.

* **Databricks FailToSendFeather**: Om `FailToSendFeather` du ser ett fel när du läser data i Azure Databricks-klustret läser du följande lösningar:
    
    * Uppgradera `azureml-sdk[automl]` paketet till den senaste versionen.
    * Lägg `azureml-dataprep` till version 1.1.8 eller högre.
    * Lägg `pyarrow` till version 0.11 eller högre.

## <a name="create-and-manage-workspaces"></a>Skapa och hantera arbetsytor

> [!WARNING]
> Det går inte att flytta din Azure Machine Learning-arbetsyta till en annan prenumeration, eller flytta den ägande prenumerationen till en ny klientorganisation. Om du gör det kan det orsaka fel.

* **Azure portal**: Om du går direkt för att visa din arbetsyta från en resurslänk från SDK eller portalen, kommer du inte att kunna visa den normala **översiktssidan** med prenumerationsinformation i tillägget. Du kommer inte heller att kunna växla till en annan arbetsyta. Om du behöver visa en annan arbetsyta går du direkt till [Azure Machine Learning studio](https://ml.azure.com) och söker efter arbetsytans namn.

## <a name="set-up-your-environment"></a>Konfigurera din miljö

* **Problem med att skapa AmlCompute**: Det finns en sällsynt chans att vissa användare som skapade sin Azure Machine Learning-arbetsyta från Azure-portalen innan GA-versionen kanske inte kan skapa AmlCompute på arbetsytan. Du kan antingen höja en supportbegäran mot tjänsten eller skapa en ny arbetsyta via portalen eller SDK för att häva blockeringen omedelbart.

## <a name="work-with-data"></a>Arbeta med data

### <a name="overloaded-azurefile-storage"></a>Överbelastad AzureFile-lagring

Om du får `Unable to upload project files to working directory in AzureFile because the storage is overloaded`ett felmeddelande använder du följande lösningar.

Om du använder filresurs för andra arbetsbelastningar, till exempel dataöverföring, är rekommendationen att använda blobbar så att filresursen är gratis att använda för att skicka körningar. Du kan också dela arbetsbelastningen mellan två olika arbetsytor.

### <a name="passing-data-as-input"></a>Skicka data som indata

*  **TypeError: FileNotFound: Ingen sådan fil eller katalog**: Det här felet uppstår om filsökvägen du anger inte är där filen finns. Du måste se till att hur du refererar till filen är förenligt med var du monterade datauppsättningen på beräkningsmålet. För att säkerställa ett deterministiskt tillstånd rekommenderar vi att du använder den abstrakta sökvägen när du monterar en datauppsättning till ett beräkningsmål. I följande kod monterar vi till exempel datauppsättningen under roten i `/tmp`beräkningsmålets filsystem. 
    
    ```python
    # Note the leading / in '/tmp/dataset'
    script_params = {
        '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
    } 
    ```

    Om du inte inkluderar det inledande snedstrecket framåt, "/", måste du `/mnt/batch/.../tmp/dataset` prefix arbetskatalogen, t.ex.

### <a name="data-labeling-projects"></a>Projekt för datamärkning

|Problem  |Lösning  |
|---------|---------|
|Endast datauppsättningar som skapas på blob-datalager kan användas     |  Detta är en känd begränsning av den aktuella versionen.       |
|När projektet har skapats visas "Initiera" under lång tid     | Uppdatera sidan manuellt. Initieringen bör fortsätta med ungefär 20 datapunkter per sekund. Bristen på autorefresh är ett känt problem.         |
|När du granskar bilder visas inte nyligen märkta bilder     |   Om du vill läsa in alla märkta bilder väljer du knappen **Första.** Den **första** knappen tar dig tillbaka till framsidan av listan, men laddar alla märkta data.      |
|Om du trycker på Esc-tangenten när du etiketterar för objektidentifiering skapas en nollstorleksetikett i det övre vänstra hörnet. Det går inte att skicka etiketter i det här tillståndet.     |   Ta bort etiketten genom att klicka på korsmarkeringen bredvid den.  |

## <a name="azure-machine-learning-designer"></a>Azure Machine Learning-designer

Kända problem:

* **Lång beräkningsförberedelsetid**: Det kan vara några minuter eller ännu längre när du först ansluter till eller skapar ett beräkningsmål. 

## <a name="train-models"></a>Inlärningsmodeller

* **ModuleErrors (Ingen modul namngiven)**: Om du kör in i ModuleErrors medan du skickar in experiment i Azure ML betyder det att utbildningsskriptet förväntar sig att ett paket ska installeras men det läggs inte till. När du har ange paketnamnet installerar Azure ML paketet i den miljö som används för din träningskörning. 

    Om du använder [Estimator för](concept-azure-machine-learning-architecture.md#estimators) att skicka experiment kan `pip_packages` `conda_packages` du ange ett paketnamn via eller parameter i uppskattningsmannen baserat på från vilken källa du vill installera paketet. Du kan också ange en yml-fil `conda_dependencies_file`med alla dina beroenden med eller `pip_requirements_file` lista alla dina pipkrav i en txt-fil med parametern. Om du har ett eget Azure ML Environment-objekt som du vill åsidosätta standardavbildningen `environment` som används av kalkylatorn kan du ange den miljön via parametern för uppskattningskonstruktorn.

    Azure ML tillhandahåller också ramspecifika kalkylatorer för Tensorflow, PyTorch, Chainer och SKLearn. Med hjälp av dessa skattningsatorer kommer att se till att de viktigaste ramverksberoendena installeras för din räkning i den miljö som används för utbildning. Du har möjlighet att ange extra beroenden enligt beskrivningen ovan. 
 
    Azure ML underhållna dockeravbildningar och deras innehåll kan ses i [AzureML Containers](https://github.com/Azure/AzureML-Containers).
    Ramspecifika beroenden visas i respektive ramdokumentation - [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py#remarks), [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py#remarks), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py#remarks), [SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py#remarks).

    > [!Note]
    > Om du tror att ett visst paket är tillräckligt vanligt för att läggas till i Azure ML-underhållna avbildningar och miljöer kan du ta upp ett GitHub-problem i [AzureML Containers](https://github.com/Azure/AzureML-Containers). 
 
* **NameError (Namn har inte definierats), AttributeError (Objektet har inget attribut)**: Det här undantaget ska komma från dina träningsskript. Du kan titta på loggfilerna från Azure-portalen för att få mer information om det specifika namn som inte har definierats eller attributfel. Från SDK kan du `run.get_details()` använda för att titta på felmeddelandet. Detta kommer också att lista alla loggfiler som genereras för din körning. Se till att ta en titt på ditt träningsskript och åtgärda felet innan du skickar in körningen igen. 

* **Horovod har stängts**av : I de flesta fall om du stöter på "AbortedError: Horovod har stängts" innebär detta undantag att det fanns ett underliggande undantag i en av de processer som orsakade Horovod att stänga. Varje rang i MPI-jobbet får den egna dedikerade loggfilen i Azure ML. Dessa loggar heter `70_driver_logs`. Vid distribuerad utbildning suffixeras loggnamnen `_rank` med för att göra det lättare att skilja loggarna åt. För att hitta det exakta felet som orsakade Horovod att stänga `Traceback` av, gå igenom alla loggfiler och leta efter i slutet av driver_log filer. En av dessa filer ger dig det faktiska underliggande undantaget. 

* **Körning eller experimentborttagning:** Experiment kan arkiveras med metoden [Experiment.archive](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#archive--) eller från fliken Experiment i Azure Machine Learning studio-klienten via knappen "Arkivexperiment". Den här åtgärden döljer experimentet från listfrågor och vyer, men tar inte bort det.

    Permanent borttagning av enskilda experiment eller körningar stöds för närvarande inte. Mer information om hur du tar bort resurser för arbetsyta finns i [Exportera eller ta bort arbetsytedata för Machine Learning-tjänsten](how-to-export-delete-data.md).

* **Måttdokument är för stort:** Azure Machine Learning har interna gränser för storleken på måttobjekt som kan loggas samtidigt från en utbildningskörning. Om du stöter på ett "Metriskt dokument är för stort" när du loggar ett listvärdesmått kan du prova att dela upp listan i mindre segment, till exempel:

    ```python
    run.log_list("my metric name", my_metric[:N])
    run.log_list("my metric name", my_metric[N:])
    ```

    Internt sammanfogar Azure ML blocken med samma måttnamn i en sammanhängande lista.

## <a name="automated-machine-learning"></a>Automatiserad maskininlärning

* **Tensor Flow**: Automatiserad maskininlärning stöder för närvarande inte tensorflöde version 1.13. Om du installerar den här versionen slutar paketberoenden att fungera. Vi arbetar för att åtgärda problemet i en framtida version.

* **Experiment diagram:** Binära klassificering diagram (precision-recall, ROC, vinst kurva etc.) som visas i automatiserade ML experiment iterationer inte återges korrekt i användargränssnittet sedan 4 / 12. Diagramdiagram visar för närvarande omvända resultat, där bättre presterande modeller visas med lägre resultat. En resolution är under utredning.

* **Databricks avbryter en automatisk maskininlärningskörning:** När du använder automatiserade maskininlärningsfunktioner på Azure Databricks, för att avbryta en körning och starta en ny experimentkörning, starta om ditt Azure Databricks-kluster.

* **Databricks >10 iterationer för automatiserad maskininlärning:** I automatiska maskininlärningsinställningar, om du har fler än 10 iterationer, inställd på `show_output` `False` när du skickar in körningen.

* **Databricks-widgeten för Azure Machine Learning SDK och automatiserad maskininlärning:** Azure Machine Learning SDK-widgeten stöds inte i en Databricks-anteckningsbok eftersom anteckningsböckerna inte kan tolka HTML-widgetar. Du kan visa widgeten i portalen med den här Python-koden i din Azure Databricks-anteckningsbokscell:

    ```
    displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
    ```

## <a name="deploy--serve-models"></a>Distribuera och hantera modeller

Vidta följande åtgärder för följande fel:

|Fel  | Lösning  |
|---------|---------|
|Fel på bildbygge vid distribution av webbtjänst     |  Lägg till "pynacl==1.2.1" som pipberoende i Conda-filen för bildkonfiguration       |
|`['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`     |   Ändra SKU för virtuella datorer som används i distributionen till en som har mer minne. |
|FPGA-fel     |  Du kommer inte att kunna distribuera modeller på FPGA förrän du har begärt och godkänts för FPGA-kvot. Om du vill begära åtkomst fyller du i formuläret för kvotbegäran:https://aka.ms/aml-real-time-ai       |

### <a name="updating-azure-machine-learning-components-in-aks-cluster"></a>Uppdatera Azure Machine Learning-komponenter i AKS-kluster

Uppdateringar av Azure Machine Learning-komponenter som är installerade i ett Azure Kubernetes-tjänstkluster måste tillämpas manuellt. 

Du kan installera dessa uppdateringar genom att koppla från klustret från Azure Machine Learning-arbetsytan och sedan återansluta klustret till arbetsytan. Om TLS är aktiverat i klustret måste du ange TLS/SSL-certifikatet och den privata nyckeln när du sätter tillbaka klustret. 

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

Om du inte längre har TLS/SSL-certifikatet och den privata nyckeln, eller om du använder ett certifikat som genereras av Azure `kubectl` Machine Learning, kan `azuremlfessl`du hämta filerna innan du kopplar från klustret genom att ansluta till klustret med hjälp av och hämta hemligheten .

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes lagrar hemligheterna i base-64 kodat format. Du kommer att behöva basera-64 avkoda `cert.pem` och `key.pem` komponenter `attach_config.enable_ssl`i hemligheter innan de ger dem till . 

### <a name="webservices-in-azure-kubernetes-service-failures"></a>Webbtjänster i Azure Kubernetes-tjänstfel

Många webbtjänstfel i Azure Kubernetes-tjänsten kan avsökas genom `kubectl`att ansluta till klustret med . Du kan `kubeconfig.json` hämta för ett Azure Kubernetes-tjänstkluster genom att köra

```azurecli-interactive
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="authentication-errors"></a>Autentiseringsfel

Om du utför en hanteringsåtgärd på ett beräkningsmål från ett fjärrjobb visas något av följande fel:

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

Du får till exempel ett felmeddelande om du försöker skapa eller koppla ett beräkningsmål från en ML-pipeline som skickas för fjärrkörning.
