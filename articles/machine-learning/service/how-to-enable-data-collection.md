---
title: Aktivera datainsamling för modeller i produktion – Azure Machine Learning
description: Lär dig mer om att samla in Azure Machine Learning indatamodellen data i Azure Blob storage.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: marthalc
author: marthalc
ms.date: 09/24/2018
ms.openlocfilehash: 412871c0c692f60e690f61fa4e6f67f836cd3ef7
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47158215"
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

- En Azure-prenumeration. Om du inte har ett konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

- En lokal katalog som innehåller dina skript och Azure Machine Learning-SDK för Python installerat en Azure Machine Learning-arbetsyta. Lär dig hur du hämtar dessa krav med hjälp av den [så här konfigurerar du en utvecklingsmiljö](how-to-configure-environment.md) dokumentet.

- En tränad modell för maskininlärning som ska distribueras till Azure Kubernetes Service (AKS). Om du inte har någon kan se den [träna bild klassificeringsmodellen](tutorial-train-models-with-aml.md) självstudien.

- En [AKS-kluster](how-to-deploy-to-aks.md).

- Följande beroenden och installerat modulen [i din miljö](how-to-configure-environment.md):
  + I Linux:
    ```shell
    sudo apt-get install libxml++2.6-2v5
    pip install azureml-monitoring
    ```

  + I Windows:
    ```shell
    pip install azureml-monitoring
    ```

## <a name="enable-data-collection"></a>Aktivera datainsamling
Insamling av data kan aktiveras oavsett modellen som distribueras via Azure Machine Learning-tjänsten eller andra verktyg. 

För att göra det, måste du:

1. Öppna bedömningsfil. 

1. Lägg till följande kod högst upp i filen:

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

5. [Skapa ny avbildning och distribuera din tjänst.](how-to-deploy-to-aks.md) 


Om du redan har en tjänst med beroenden som installerats i din **miljöfil** och **bedömningsfil**, aktivera insamling av data genom att:

1. Gå till [Azure-portalen](https://portal.azure.com).

1. Öppna din arbetsyta.

1. Gå till **distributioner** -> **Välj tjänst** -> **redigera**.

   ![Redigera tjänst](media/how-to-enable-data-collection/EditService.png)

1. I **avancerade inställningar**, avmarkera **aktivera modelldatasamling**. 

   ![Avmarkera datainsamling](media/how-to-enable-data-collection/CheckDataCollection.png)

   I det här fönstret kan du också välja att ”aktivera Appinsights-diagnostik” att spåra hälsotillståndet för din tjänst.  

1. Välj **uppdatering** tillämpa ändringen.


## <a name="disable-data-collection"></a>Inaktivera datainsamling
Du kan stoppa insamling av data som helst. Använda Python-kod eller Azure-portalen för att inaktivera insamling av data.

+ Alternativ 1 – inaktivera i Azure portal: 
  1. Logga in på [Azure-portalen](https://portal.azure.com).

  1. Öppna din arbetsyta.

  1. Gå till **distributioner** -> **Välj tjänst** -> **redigera**.

     ![Redigera tjänst](media/how-to-enable-data-collection/EditService.png)

  1. I **avancerade inställningar**, avmarkera **aktivera modelldatasamling**. 

     ![Avmarkera datainsamling](media/how-to-enable-data-collection/UncheckDataCollection.png) 

  1. Välj **uppdatering** tillämpa ändringen.

* Alternativ 2 – använda Python för att inaktivera datainsamling:

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="example-notebook"></a>Exempel-anteckningsbok

Den `00.Getting Started/12.enable-data-collection-for-models-in-aks.ipynb` notebook demonstrerar begreppen i den här artikeln.  

Hämta den här anteckningsboken:
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]