---
title: Samla in data om dina produktionsmodeller
titleSuffix: Azure Machine Learning
description: Lär dig hur du samlar in indatamodelldata för Azure Machine Learning i Azure Blob-lagring.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: laobri
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/12/2019
ms.custom: seodec18
ms.openlocfilehash: 3c481a2e12d83e865025cd90e59e0eba572ad9a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75771401"
---
# <a name="collect-data-for-models-in-production"></a>Samla in data för modeller i produktion

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

>[!IMPORTANT]
> Azure Machine Learning Monitoring SDK kommer snart att dras tillbaka. SDK är fortfarande lämpligt för utvecklare som för närvarande använder SDK för att övervaka datadrift i modeller. Men för nya kunder rekommenderar vi att du använder den förenklade [dataövervakningen med Application Insights](https://docs.microsoft.com/azure/machine-learning/how-to-enable-app-insights).

Den här artikeln visar hur du samlar in indata modelldata från Azure Machine Learning. Den visar också hur du distribuerar indata till ett AKS-kluster (Azure Kubernetes Service) och lagrar utdata i Azure Blob-lagring.

När insamlingen är aktiverad hjälper de data du samlar in dig:

* [Övervaka data driver](how-to-monitor-data-drift.md) när produktionsdata kommer in i din modell.

* Fatta bättre beslut om när du ska omskola eller optimera din modell.

* Omskola din modell med insamlade data.

## <a name="what-is-collected-and-where-it-goes"></a>Vad som samlas in och vart det går

Följande uppgifter kan samlas in:

* Modellera indata från webbtjänster som distribueras i ett AKS-kluster. Röstljud, bilder och video samlas *inte* in.
  
* Modellprognoser med hjälp av produktionsindata.

>[!NOTE]
> Preaggregation och förberäkningar på dessa data är för närvarande inte en del av insamlingstjänsten.

Utdata sparas i Blob-lagring. Eftersom data läggs till i Blob-lagring kan du välja ditt favoritverktyg för att köra analysen.

Sökvägen till utdata i blobben följer den här syntaxen:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

>[!NOTE]
> I versioner av Azure Machine Learning SDK för Python tidigare än version 0.1.0a16 heter `designation` `identifier`argumentet . Om du har utvecklat koden med en tidigare version måste du uppdatera den därefter.

## <a name="prerequisites"></a>Krav

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://aka.ms/AMLFree) konto innan du börjar.

- En AzureMachine Learning-arbetsyta, en lokal katalog som innehåller dina skript och Azure Machine Learning SDK för Python måste installeras. Mer information om hur du installerar dem finns i [Så här konfigurerar du en utvecklingsmiljö](how-to-configure-environment.md).

- Du behöver en tränad maskininlärningsmodell som ska distribueras till AKS. Om du inte har någon modell läser du [självstudiekursen För klassificering av tågavbildning.](tutorial-train-models-with-aml.md)

- Du behöver ett AKS-kluster. Information om hur du skapar en och distribuerar till den finns i [Så här distribuerar och var](how-to-deploy-and-where.md).

- [Konfigurera din miljö](how-to-configure-environment.md) och installera [Azure Machine Learning Monitoring SDK](https://aka.ms/aml-monitoring-sdk).

## <a name="enable-data-collection"></a>Aktivera datainsamling

Du kan aktivera datainsamling oavsett vilken modell du distribuerar via Azure Machine Learning eller andra verktyg.

För att aktivera datainsamling måste du:

1. Öppna bedömningsfilen.

1. Lägg till [följande kod](https://aka.ms/aml-monitoring-sdk) högst upp i filen:

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

1. Deklarera dina datainsamlingsvariabler i din `init` funktion:

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", designation="inputs", feature_names=["feat1", "feat2", "feat3". "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", designation="predictions", feature_names=["prediction1", "prediction2"])
    ```

    *CorrelationId* är en valfri parameter. Du behöver inte använda den om din modell inte kräver det. Användning av *CorrelationId* hjälper dig att enklare mappa med andra data, till exempel *LoanNumber* eller *CustomerId*.
    
    Parametern *Identifier* används senare för att skapa mappstrukturen i blobben. Du kan använda den för att skilja rådata från bearbetade data.

1. Lägg till följande kodrader i `run(input_df)` funktionen:

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

1. Datainsamlingen är *inte* automatiskt inställd på **true** när du distribuerar en tjänst i AKS. Uppdatera konfigurationsfilen, som i följande exempel:

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```

    Du kan också aktivera Application Insights för tjänstövervakning genom att ändra den här konfigurationen:

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ```

1. Information om hur du skapar en ny avbildning och distribuerar maskininlärningsmodellen finns i [Så här distribuerar och var](how-to-deploy-and-where.md).

Om du redan har en tjänst med beroenden installerade i miljöfilen och bedömningsfilen aktiverar du datainsamling genom att följa dessa steg:

1. Gå till [Azure Machine Learning](https://ml.azure.com).

1. Öppna arbetsytan.

1. Välj **Distributioner** > **Välj tjänst** > **Redigera**.

   ![Redigera tjänsten](././media/how-to-enable-data-collection/EditService.PNG)

1. I **Avancerade inställningar**väljer du Aktivera **datainsamling av modell**.

    [![Välj datainsamling](./media/how-to-enable-data-collection/CheckDataCollection.png)](././media/how-to-enable-data-collection/CheckDataCollection.png#lightbox)

   Du kan också välja **Aktivera AppInsights diagnostik** för att spåra hälsotillståndet för din tjänst.

1. Välj **Uppdatera** om du vill tillämpa ändringarna.

## <a name="disable-data-collection"></a>Inaktivera datainsamling

Du kan när som helst sluta samla in data. Använd Python-kod eller Azure Machine Learning för att inaktivera datainsamling.

### <a name="option-1---disable-data-collection-in-azure-machine-learning"></a>Alternativ 1 - Inaktivera datainsamling i Azure Machine Learning

1. Logga in på [Azure Machine Learning](https://ml.azure.com).

1. Öppna arbetsytan.

1. Välj **Distributioner** > **Välj tjänst** > **Redigera**.

   [![Välj alternativet Redigera](././media/how-to-enable-data-collection/EditService.PNG)](./././media/how-to-enable-data-collection/EditService.PNG#lightbox)

1. I **Avancerade inställningar**avmarkerar du Aktivera **datainsamling av modell**.

    [![Avmarkera kryssrutan Datainsamling](./media/how-to-enable-data-collection/UncheckDataCollection.png)](././media/how-to-enable-data-collection/UncheckDataCollection.png#lightbox)

1. Välj **Uppdatera** om du vill tillämpa ändringen.

Du kan också komma åt dessa inställningar på arbetsytan i [Azure Machine Learning](https://ml.azure.com).

### <a name="option-2---use-python-to-disable-data-collection"></a>Alternativ 2 - Använd Python för att inaktivera datainsamling

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-and-analyze-your-data"></a>Validera och analysera dina data

Du kan välja ett verktyg för att analysera de data som samlas in i din Blob-lagring.

### <a name="quickly-access-your-blob-data"></a>Snabbt komma åt dina blob-data

1. Logga in på [Azure Machine Learning](https://ml.azure.com).

1. Öppna arbetsytan.

1. Välj **Lagring**.

    [![Välj alternativet Lagring](./media/how-to-enable-data-collection/StorageLocation.png)](././media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Följ sökvägen till blobens utdata med den här syntaxen:

   ```
   /modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
   # example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
   ```

### <a name="analyze-model-data-using-power-bi"></a>Analysera modelldata med Power BI

1. Ladda ned och öppna [Power BI Desktop](https://www.powerbi.com).

1. Välj **Hämta data** och välj Azure [**Blob Storage**](https://docs.microsoft.com/power-bi/desktop-data-sources).

    [![Power BI-blob-konfiguration](./media/how-to-enable-data-collection/PBIBlob.png)](././media/how-to-enable-data-collection/PBIBlob.png#lightbox)

1. Lägg till ditt lagringskontonamn och ange din lagringsnyckel. Du hittar den här informationen genom att välja **Inställningar** > **Access-nycklar** i din blob.

1. Markera **modelldatabehållaren** och välj **Redigera**.

    [![Power BI-navigator](./media/how-to-enable-data-collection/pbiNavigator.png)](././media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. Klicka under kolumnen **Namn** i frågeredigeraren och lägg till ditt lagringskonto.

1. Ange modellsökvägen i filtret. Om du bara vill titta på filer från ett visst år eller en viss månad expanderar du bara filtersökvägen. Om du till exempel bara vill titta på marsdata använder du den här filtersökvägen:

   /modeldata/\<subscriptionid>/\<resourcegroupname>/\<workspacename>/\<webservicename>/\<modelname>/\<modelversion>/\<beteckning>/\<år>/3

1. Filtrera de data som är relevanta för dig baserat på **namnvärden.** Om du har lagrat förutsägelser och indata måste du skapa en fråga för varje.

1. Markera de nedåtgående dubbelpilarna bredvid kolumnrubriken **Innehåll** för att kombinera filerna.

    [![Power BI-innehåll](./media/how-to-enable-data-collection/pbiContent.png)](././media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. Välj **OK**. Datan förladdar.

    [![Power BI Kombinera filer](./media/how-to-enable-data-collection/pbiCombine.png)](././media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. Välj **Stäng och Använd**.

1. Om du har lagt till indata och förutsägelser ordnas tabellerna automatiskt efter **RequestId-värden.**

1. Börja skapa anpassade rapporter på dina modelldata.

### <a name="analyze-model-data-using-azure-databricks"></a>Analysera modelldata med Azure Databricks

1. Skapa en [Azure Databricks-arbetsyta](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal).

1. Gå till din Databricks-arbetsyta.

1. Välj **Ladda upp data**på arbetsytan Databricks .

    [![Välja alternativet Databricks Upload Data](./media/how-to-enable-data-collection/dbupload.png)](././media/how-to-enable-data-collection/dbupload.png#lightbox)

1. Välj **Skapa ny tabell** och välj Andra **datakällor** > Azure**Blob Storage** > **Create Table i Notebook**.

    [![Skapa databricks-tabell](./media/how-to-enable-data-collection/dbtable.PNG)](././media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. Uppdatera platsen för dina data. Här är ett exempel:

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```

    [![Installation av Databricks](./media/how-to-enable-data-collection/dbsetup.png)](././media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. Följ stegen i mallen för att visa och analysera dina data.
