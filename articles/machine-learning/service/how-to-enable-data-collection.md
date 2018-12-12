---
title: Aktivera datainsamling för modeller i produktion
titleSuffix: Azure Machine Learning service
description: Lär dig mer om att samla in Azure Machine Learning indatamodellen data i Azure Blob storage.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: marthalc
author: marthalc
ms.date: 11/08/2018
ms.custom: seodec18
ms.openlocfilehash: e6b6a8e1dd1b3746977062341013b92acff132c4
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53100553"
---
# <a name="collect-data-for-models-in-production"></a>Samla in data för modeller i produktion

I den här artikeln får du lära dig att samla in indatamodellen data från Azure Machine Learning-tjänster som du har distribuerat i Azure Kubernetes kluster (AKS) i Azure Blob storage. 

När du har aktiverat, hjälper den här informationen som samlas in dig att:
* Övervaka data drifts produktionsdata försätts i din modell

* Fatta bättre beslut om när du ska omtrimma eller optimera din modell

* Träna din modell med data som samlas in

## <a name="what-is-collected-and-where-does-it-go"></a>Det här samlas och där går det?

Följande data kan samlas in:
* Modellen **inkommande** data från webbtjänster som distribueras i Azure Kubernetes kluster (AKS) (ljud, bilder och video är **inte** som samlats in) 
  
* Modeller förutsägelser med indata för produktion.

> [!Note]
> Aggregering eller före beräkningar på dessa data är inte en del av tjänsten just nu.   

Utdata hämtar sparas i en Azure-Blob. Eftersom data läggs till en Azure Blob, kan du sedan välja ditt favorit verktyg för att köra analysen. 

Sökvägen till utdata i blob följande följande syntax:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<identifier>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Om du inte har ett konto kan du skapa ett [kostnadsfritt konto](https://aka.ms/AMLfree) innan du börjar.

- En Azure Machine Learning-tjänsten arbetsyta, en lokal katalog som innehåller dina skript och Azure Machine Learning-SDK för Python installerat. Lär dig hur du hämtar dessa krav med hjälp av den [så här konfigurerar du en utvecklingsmiljö](how-to-configure-environment.md) dokumentet.

- En tränad modell för maskininlärning som ska distribueras till Azure Kubernetes Service (AKS). Om du inte har någon kan se den [träna bild klassificeringsmodellen](tutorial-train-models-with-aml.md) självstudien.

- Ett Azure Kubernetes Service-kluster. Information om hur du skapar och distribuerar till en finns i den [hur du distribuerar och var](how-to-deploy-and-where.md) dokumentet.

- [Konfigurera din miljö](how-to-configure-environment.md) och installera den [övervakning SDK](https://aka.ms/aml-monitoring-sdk).

## <a name="enable-data-collection"></a>Aktivera datainsamling
Insamling av data kan aktiveras oavsett modellen som distribueras via Azure Machine Learning-tjänsten eller andra verktyg. 

För att göra det, måste du:

1. Öppna bedömningsfil. 

1. Lägg till den [efter kod](https://aka.ms/aml-monitoring-sdk) överst i filen:

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

2. Deklarera dina variabler för insamling av data i din `init()` funktionen:

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", identifier="inputs", feature_names=["feat1", "feat2", "feat3". "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", identifier="predictions", feature_names=["prediction1", "prediction2"])
    ```

    *CorrelationId* är en valfri parameter, du behöver inte konfigurera den om din modell inte kräver den. Att ha en correlationId hjälper dig för enklare mappning med andra data. (Exempel: LoanNumber, CustomerId, osv.)
    
    *Identifieraren* senare används för att skapa mappstrukturen i din Blob, den kan användas för att dela upp ”” rådata jämfört med ”bearbetad”.

3.  Lägg till följande rader med kod till den `run(input_df)` funktionen:

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

4. Insamling av data är **inte** automatiskt ange värdet som **SANT** när du distribuerar en tjänst i AKS, så måste du uppdatera konfigurationsfilen som: 

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```
    AppInsights för tjänstövervakning av kan också aktiveras genom att ändra den här konfigurationen:
    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ``` 

5. Om du vill skapa en ny avbildning och distribuera tjänsten, se den [hur du distribuerar och var](how-to-deploy-and-where.md) dokumentet.


Om du redan har en tjänst med beroenden som installerats i din **miljöfil** och **bedömningsfil**, aktivera insamling av data genom att:

1. Gå till [Azure-portalen](https://portal.azure.com).

1. Öppna din arbetsyta.

1. Gå till **distributioner** -> **Välj tjänst** -> **redigera**.

   ![Redigera tjänst](media/how-to-enable-data-collection/EditService.PNG)

1. I **avancerade inställningar**, avmarkera **aktivera modelldatasamling**. 

    [![Kontrollera datainsamling](media/how-to-enable-data-collection/CheckDataCollection.png)](./media/how-to-enable-data-collection/CheckDataCollection.png#lightbox)

   I det här fönstret kan du också välja att ”aktivera Appinsights-diagnostik” att spåra hälsotillståndet för din tjänst.  

1. Välj **uppdatering** tillämpa ändringen.


## <a name="disable-data-collection"></a>Inaktivera datainsamling
Du kan stoppa insamling av data som helst. Använda Python-kod eller Azure-portalen för att inaktivera insamling av data.

+ Alternativ 1 – inaktivera i Azure portal: 
  1. Logga in på [Azure-portalen](https://portal.azure.com).

  1. Öppna din arbetsyta.

  1. Gå till **distributioner** -> **Välj tjänst** -> **redigera**.

    [![Redigera alternativ](media/how-to-enable-data-collection/EditService.PNG)](./media/how-to-enable-data-collection/EditService.PNG#lightbox)

  1. I **avancerade inställningar**, avmarkera **aktivera modelldatasamling**. 

    [![Avmarkera datainsamling](media/how-to-enable-data-collection/UncheckDataCollection.png)](./media/how-to-enable-data-collection/UncheckDataCollection.png#lightbox)

  1. Välj **uppdatering** tillämpa ändringen.

* Alternativ 2 – använda Python för att inaktivera datainsamling:

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-your-data-and-analyze-it"></a>Validera dina data och analysera dem
Du kan välja alla verktyg du föredrar att analysera data som samlats in i din Azure-Blob. 

Att snabbt komma åt data från din blob:
1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Öppna din arbetsyta.
1. Klicka på **Storage**.

    [![Storage](media/how-to-enable-data-collection/StorageLocation.png)](./media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Följ väg till utdata i blobben med följande syntax:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<identifier>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```


### <a name="analyzing-model-data-through-power-bi"></a>Analysera modelldata via Power BI

1. Ladda ned och öppna [Power BI Desktop](https://www.powerbi.com)

1. Välj **hämta Data** och klicka på [ **Azure Blob Storage**](https://docs.microsoft.com/power-bi/desktop-data-sources).

    [![Installationsprogrammet för PBI-Blob](media/how-to-enable-data-collection/PBIBlob.png)](./media/how-to-enable-data-collection/PBIBlob.png#lightbox)


1. Lägg till namnet på ditt lagringskonto och ange lagringskontots åtkomstnyckel. Du hittar den här informationen i din blob **inställningar** >> åtkomstnycklar. 

1. Välj behållaren **modeldata** och klicka på **redigera**. 

    [![PBI navigatör](media/how-to-enable-data-collection/pbiNavigator.png)](./media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. Klicka på under ”Name”-kolumnen i frågeredigeraren och lägga till ditt Storage-konto 1. Modellen sökväg i filtret. Obs: Om du vill att bara söka i filer från ett visst år eller månad, bara Expandera filter-sökväg. Titta exempelvis bara i mars data: / modeldata/prenumerations-ID > / resourcegroupname > / workspacename > / webservicename > / %{ModelName/ > / modelversion > / ID > / år > / 3

1. Filtrera de data som är relevant för dig baserat på **namn**. Om du har lagrat **förutsägelser** och **indata** måste du skapa en fråga var och en.

1. Klicka på den dubbla pilen tagits ur bruk den **innehåll** kolumn att kombinera filer. 

    [![PBI-innehåll](media/how-to-enable-data-collection/pbiContent.png)](./media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. Klicka på OK och data kommer förinstallera.

    [![pbiCombine](media/how-to-enable-data-collection/pbiCombine.png)](./media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. Du kan klicka på **Stäng och tillämpa** .

1.  Om du har lagt till indata och förutsägelser dina tabeller kommer automatiskt korrelera av **RequestId**.

1. Börja skapa anpassade rapporter på dina modelldata.


### <a name="analyzing-model-data-using-databricks"></a>Analysera modelldata med Databricks

1. Skapa en [Databricks-arbetsyta](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). 

1. Gå till din Databricks-arbetsyta. 

1. I din databricks väljer du arbetsytan **ladda upp Data**.

    [![Ladda upp DB](media/how-to-enable-data-collection/dbupload.png)](./media/how-to-enable-data-collection/dbupload.png#lightbox)

1. Skapa ny tabell och välj **andra datakällor** -> Azure Blob Storage -> Create Table i anteckningsboken.

    [![DB-tabell](media/how-to-enable-data-collection/dbtable.PNG)](./media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. Uppdatera platsen för dina data. Här är ett exempel:

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```
 
    [![Med DBsetup](media/how-to-enable-data-collection/dbsetup.png)](./media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. Följ anvisningarna på mallen för att visa och analysera dina data. 

## <a name="example-notebook"></a>Exempel-anteckningsbok

Den [how-to-use-azureml/deployment/enable-data-collection-for-models-in-aks/enable-data-collection-for-models-in-aks.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-data-collection-for-models-in-aks/enable-data-collection-for-models-in-aks.ipynb) notebook demonstrerar begreppen i den här artikeln.  

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
