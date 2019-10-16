---
title: Samla in data på dina produktions modeller
titleSuffix: Azure Machine Learning
description: Lär dig hur du samlar in Azure Machine Learning indata för modell data i Azure Blob Storage.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: marthalc
author: marthalc
ms.date: 07/15/2019
ms.custom: seodec18
ms.openlocfilehash: 109db23976f6332b24bcfa565812bd9491062691
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330736"
---
# <a name="collect-data-for-models-in-production"></a>Samla in data för modeller i produktion

>[!IMPORTANT]
> Detta SDK tas snart ur bruk. Detta SDK är fortfarande lämpligt för utvecklare som övervakar data i modeller, men de flesta utvecklare bör använda den förenklade [data övervakningen med Application Insights](https://docs.microsoft.com/azure/machine-learning/service/how-to-enable-app-insights). 

I den här artikeln får du lära dig hur du samlar in indata från Azure Machine Learning som du har distribuerat till Azure Kubernetes-kluster (AKS) i Azure Blob Storage. 

När den här informationen är aktive rad kan du:
* [Övervaka data](how-to-monitor-data-drift.md) drift som produktions data går in i din modell

* Fatta bättre beslut när du ska träna eller optimera din modell

* Träna din modell med de data som samlas in

## <a name="what-is-collected-and-where-does-it-go"></a>Vad samlas in och var finns det?

Följande data kan samlas in:
* Information om modell **indata** från webb tjänster som distribueras i Azure Kubernetes-kluster (AKS) (röst, bilder och video samlas **inte** in) 
  
* Modell förutsägelser med hjälp av produktions data.

> [!Note]
> Föragg regering eller för beräkningarna av dessa data är inte en del av tjänsten för tillfället.   

Utdata sparas i en Azure-blob. Eftersom data läggs till i en Azure-Blob kan du sedan välja ditt favorit verktyg för att köra analysen. 

Sökvägen till utdata i bloben följer den här syntaxen:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<identifier>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

## <a name="prerequisites"></a>Krav

- Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag.

- En Azure Machine Learning arbets yta, en lokal katalog som innehåller dina skript och Azure Machine Learning SDK för python installerat. Lär dig hur du får dessa krav genom att [Konfigurera ett dokument för utvecklings miljön](how-to-configure-environment.md) .

- En utbildad maskin inlärnings modell som ska distribueras till Azure Kubernetes service (AKS). Om du inte har något kan du läsa själv studie kursen [träna bild klassificerings modell](tutorial-train-models-with-aml.md) .

- Ett Azure Kubernetes service-kluster. Information om hur du skapar och distribuerar till en finns i avsnittet [så här distribuerar du och var](how-to-deploy-and-where.md) dokumentet.

- [Konfigurera din miljö](how-to-configure-environment.md) och installera övervaknings- [SDK: n](https://aka.ms/aml-monitoring-sdk).

## <a name="enable-data-collection"></a>Aktivera datainsamling
Data insamling kan aktive ras oavsett vilken modell som distribueras via Azure Machine Learning eller andra verktyg. 

Om du vill aktivera det måste du:

1. Öppna bedömnings filen. 

1. Lägg till [följande kod](https://aka.ms/aml-monitoring-sdk) högst upp i filen:

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

2. Deklarera dina variabler för data insamling i din `init()`-funktion:

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", identifier="inputs", feature_names=["feat1", "feat2", "feat3". "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", identifier="predictions", feature_names=["prediction1", "prediction2"])
    ```

    *CorrelationId* är en valfri parameter, du behöver inte konfigurera den om din modell inte kräver det. Genom att använda ett correlationId på plats kan du enklare mappa med andra data. (Exempel: LoanNumber, CustomerId osv.)
    
    *Identifieraren* används senare för att skapa mappstrukturen i blobben. den kan användas för att dela upp "rå" data och "bearbetade".

3.  Lägg till följande rader med kod i `run(input_df)`-funktionen:

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

4. Data insamling anges **inte** automatiskt till **Sant** när du distribuerar en tjänst i AKS, så du måste uppdatera konfigurations filen, till exempel: 

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```
    AppInsights för tjänst övervakning kan också aktive ras genom att ändra den här konfigurationen:
    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ``` 

5. Information om hur du skapar en ny avbildning och distribuerar tjänsten finns i [så här distribuerar och var](how-to-deploy-and-where.md) dokumentet.


Om du redan har en tjänst med beroenden som är installerade i **miljö filen** och **bedömnings filen**aktiverar du data insamling genom att:

1. Gå till [Azure Portal](https://portal.azure.com).

1. Öppna din arbets yta.

1. Gå till **distributioner** -> **Välj tjänst** -> **Redigera**.

   ![Redigera tjänst](media/how-to-enable-data-collection/EditService.PNG)

1. Avmarkera **Aktivera modell data insamling**i **Avancerade inställningar**. 

    [Data insamling för @no__t 1check](media/how-to-enable-data-collection/CheckDataCollection.png)](./media/how-to-enable-data-collection/CheckDataCollection.png#lightbox)

   I det här fönstret kan du också välja "Aktivera Appinsights diagnostik" för att spåra hälso tillståndet för din tjänst.  

1. Välj **Uppdatera** för att tillämpa ändringen.


## <a name="disable-data-collection"></a>Inaktivera data insamling
Du kan sluta samla in data när som helst. Använd python-kod eller Azure Portal för att inaktivera data insamling.

+ Alternativ 1 – Inaktivera i Azure Portal: 
  1. Logga in på [Azure-portalen](https://portal.azure.com).

  1. Öppna din arbets yta.

  1. Gå till **distributioner** -> **Välj tjänst** -> **Redigera**.

     [alternativet ![Edit](media/how-to-enable-data-collection/EditService.PNG)](./media/how-to-enable-data-collection/EditService.PNG#lightbox)

  1. Avmarkera **Aktivera modell data insamling**i **Avancerade inställningar**. 

     [Data insamling för @no__t 1Uncheck](media/how-to-enable-data-collection/UncheckDataCollection.png)](./media/how-to-enable-data-collection/UncheckDataCollection.png#lightbox)

  1. Välj **Uppdatera** för att tillämpa ändringen.

  Du kan också komma åt de här inställningarna i din [landnings sida för arbets ytor (för hands version)](https://ml.azure.com).

+ Alternativ 2 – Använd python för att inaktivera data insamling:

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-your-data-and-analyze-it"></a>Verifiera dina data och analysera dem
Du kan välja valfritt verktyg för att analysera de data som samlas in i Azure-blobben. 

För att snabbt komma åt data från din BLOB:
1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Öppna din arbets yta.
1. Klicka på **lagring**.

    [![Storage](media/how-to-enable-data-collection/StorageLocation.png)](./media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Följ sökvägen till utdata i blobben med följande syntax:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<identifier>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```


### <a name="analyzing-model-data-through-power-bi"></a>Analysera modell data via Power BI

1. Ladda ned och öppna [Power BI Desktop](https://www.powerbi.com)

1. Välj **Hämta data** och klicka på [**Azure Blob Storage**](https://docs.microsoft.com/power-bi/desktop-data-sources).

    [![PBI BLOB-installation](media/how-to-enable-data-collection/PBIBlob.png)](./media/how-to-enable-data-collection/PBIBlob.png#lightbox)


1. Lägg till ditt lagrings konto namn och ange din lagrings nyckel. Du hittar den här informationen i blobens **inställningar** > > åtkomst nycklar. 

1. Välj behållaren **modeldata** och klicka på **Redigera**. 

    [![PBI Navigator](media/how-to-enable-data-collection/pbiNavigator.png)](./media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. Klicka under kolumnen namn i Frågeredigeraren och Lägg till ditt lagrings konto 1. Modell Sök väg i filtret. Obs: om du bara vill titta på filer från ett speciellt år eller månad expanderar du bara filter Sök vägen. Titta exempelvis bara på mars-data:/modeldata/subscriptionId >/ResourceGroupName >/workspacename >/webservicename >/ModelName >/modelversion >/Identifier >/Year >/3

1. Filtrera data som är relevanta för dig baserat på **namn**. Om du har lagrat **förutsägelser** och **indata**måste du skapa en fråga för var och en.

1. Klicka på den dubbelriktade pilen för att kombinera filerna i kolumnen **innehåll** . 

    [![PBI innehåll](media/how-to-enable-data-collection/pbiContent.png)](./media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. Klicka på OK så inläsnings data förvaras.

    [![pbiCombine](media/how-to-enable-data-collection/pbiCombine.png)](./media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. Nu kan du klicka på **Stäng och tillämpa** .

1.  Om du har lagt till indata och förutsägelser kommer dina tabeller automatiskt att korreleras med **RequestId**.

1. Börja skapa dina anpassade rapporter på dina modell data.


### <a name="analyzing-model-data-using-databricks"></a>Analysera modell data med Databricks

1. Skapa en [Databricks-arbetsyta](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). 

1. Gå till din Databricks-arbetsyta. 

1. I arbets ytan databricks väljer du **Ladda upp data**.

    [![DB uppladdning](media/how-to-enable-data-collection/dbupload.png)](./media/how-to-enable-data-collection/dbupload.png#lightbox)

1. Skapa en ny tabell och välj **andra data källor** – > Azure Blob Storage-> skapa tabell i antecknings boken.

    [![DB tabell](media/how-to-enable-data-collection/dbtable.PNG)](./media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. Uppdatera platsen för dina data. Här är ett exempel:

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```
 
    [![DBsetup](media/how-to-enable-data-collection/dbsetup.png)](./media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. Följ stegen på mallen för att visa och analysera dina data. 

## <a name="example-notebook"></a>Exempel antecknings bok

Antecknings boken [How-to-use-azureml/Deployment/Enable-data-Collection-for-Models-in-AKS/Enable-data-Collection-for-Models-in-AKS. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-data-collection-for-models-in-aks/enable-data-collection-for-models-in-aks.ipynb) visar begreppen i den här artikeln.  

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
