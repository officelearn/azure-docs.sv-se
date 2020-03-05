---
title: Övervaka och samla in data från Machine Learning webb tjänst slut punkter
titleSuffix: Azure Machine Learning
description: Övervaka webb tjänster som distribueras med Azure Machine Learning med hjälp av Azure Application Insights
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: larryfr
author: blackmist
ms.date: 11/12/2019
ms.openlocfilehash: 34aba3c00ac0026abebbdfc93143aa5e7f788e8b
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2020
ms.locfileid: "78268486"
---
# <a name="monitor-and-collect-data-from-ml-web-service-endpoints"></a>Övervaka och samla in data från ML webb tjänst slut punkter
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig hur du samlar in data från och övervakar modeller som har distribuerats till webb tjänst slut punkter i Azure Kubernetes service (AKS) eller Azure Container Instances (ACI) genom att aktivera Azure Application insikter. Förutom att samla in data och svar för slutpunkten kan du övervaka:

* Begär ande frekvens, svars tider och felaktiga frekvenser
* Beroende frekvens, svars tider och felaktiga frekvenser
* Undantag

[Läs mer om Azure Application insikter](../azure-monitor/app/app-insights-overview.md). 


## <a name="prerequisites"></a>Förutsättningar

* Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag

* En lokal katalog som innehåller dina skript och Azure Machine Learning-SDK för Python installerat en Azure Machine Learning-arbetsyta. Information om hur du får dessa krav finns i så här [konfigurerar du en utvecklings miljö](how-to-configure-environment.md)
* En tränad modell för maskininlärning för distribution till Azure Kubernetes Service (AKS) eller Azure Container-instans (ACI). Om du inte har något kan du läsa själv studie kursen [träna bild klassificerings modell](tutorial-train-models-with-aml.md)

## <a name="web-service-metadata-and-response-data"></a>Metadata och svars data för webb tjänst

>[!Important]
> Azure Application Insights loggar bara nytto laster på upp till 64 kB. Om den här gränsen nås loggas bara de senaste utflödena av modellen. 

Metadata och svar på tjänsten – som motsvarar metadata för webb tjänsten och modellens förutsägelser – loggas i Azure Application Insights-spår under meddelande `"model_data_collection"`. Du kan fråga Azure Application insikter direkt för att komma åt dessa data eller konfigurera en [kontinuerlig export](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) till ett lagrings konto för längre kvarhållning eller ytterligare bearbetning. Modell data kan sedan användas i Azure Machine Learning för att konfigurera etiketter, omskolning, bedömning, data analys eller annan användning. 

## <a name="use-python-sdk-to-configure"></a>Använd python SDK för att konfigurera 

### <a name="update-a-deployed-service"></a>Uppdatera en distribuerad tjänst

1. Identifiera tjänsten i din arbetsyta. Värdet för `ws` är namnet på din arbets yta

    ```python
    from azureml.core.webservice import Webservice
    aks_service= Webservice(ws, "my-service-name")
    ```
2. Uppdatera tjänsten och aktivera Azure Application insikter

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>Anpassade loggspårningar i din tjänst

Om du vill logga anpassade spår följer du standard distributions processen för AKS eller ACI i avsnittet [så här distribuerar och var](how-to-deploy-and-where.md) dokumentet. Använd sedan följande steg:

1. Uppdatera bedömnings filen genom att lägga till utskrifts uttryck
    
    ```python
    print ("model initialized" + time.strftime("%H:%M:%S"))
    ```

2. Uppdatera tjänst konfigurationen
    
    ```python
    config = Webservice.deploy_configuration(enable_app_insights=True)
    ```

3. Bygg en avbildning och distribuera den på [AKS eller ACI](how-to-deploy-and-where.md).

### <a name="disable-tracking-in-python"></a>Inaktivera spårning i Python

Om du vill inaktivera Azure Application insikter använder du följande kod:

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```

## <a name="evaluate-data"></a>Utvärdera data
Din tjänsts data lagras i ditt Azure Application Insights-konto inom samma resurs grupp som Azure Machine Learning.
Visa den:

1. Gå till arbets ytan Azure Machine Learning i [Azure Machine Learning Studio](https://ml.azure.com) och klicka på Application Insights länk

    [![AppInsightsLoc](./media/how-to-enable-app-insights/AppInsightsLoc.png)](././media/how-to-enable-app-insights/AppInsightsLoc.png#lightbox)

1. Välj fliken **Översikt** om du vill se en grundläggande uppsättning mått för din tjänst

   [Översikt över ![](./media/how-to-enable-app-insights/overview.png)](././media/how-to-enable-app-insights/overview.png#lightbox)

1. Om du vill titta på metadata och svar för webb tjänst begär Anden väljer du tabellen **förfrågningar** i avsnittet **loggar (analys)** och väljer **Kör** för att Visa begär Anden

   [![modell data](./media/how-to-enable-app-insights/model-data-trace.png)](././media/how-to-enable-app-insights/model-data-trace.png#lightbox)


3. Om du vill titta på dina anpassade spår väljer du **analys**
4. I avsnittet schema väljer du **spår**. Välj **Kör** för att köra frågan. Data ska visas i tabell format och bör mappas till dina anpassade anrop i din bedömnings fil

   [![anpassade spår](./media/how-to-enable-app-insights/logs.png)](././media/how-to-enable-app-insights/logs.png#lightbox)

Mer information om hur du använder Azure Application Insights finns i [Application Insights?](../azure-monitor/app/app-insights-overview.md).

## <a name="export-data-for-further-processing-and-longer-retention"></a>Exportera data för vidare bearbetning och längre kvarhållning

>[!Important]
> Azure Application Insights stöder endast export till Blob Storage. Ytterligare begränsningar för den här export funktionen visas i [Exportera telemetri från App Insights](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry#continuous-export-advanced-storage-configuration).

Du kan använda Azure Application insightss [löpande export](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) för att skicka meddelanden till ett lagrings konto som stöds, där en längre kvarhållning kan ställas in. `"model_data_collection"`-meddelanden lagras i JSON-format och kan enkelt tolkas för att extrahera modell data. 

Azure Data Factory, Azure ML-pipeliner eller andra data bearbetnings verktyg kan användas för att transformera data vid behov. När du har transformerat data kan du registrera den med Azure Machine Learning arbets ytan som en data uppsättning. Det gör du i så [här skapar och registrerar du data uppsättningar](how-to-create-register-datasets.md).

   [![löpande export](./media/how-to-enable-app-insights/continuous-export-setup.png)](././media/how-to-enable-app-insights/continuous-export-setup.png)


## <a name="example-notebook"></a>Exempel-anteckningsbok

Den [Enable-App-Insights-in-producting-service. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) Notebook visar begrepp i den här artikeln. 
 
[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nästa steg

* Se [distribuera en modell till ett Azure Kubernetes service-kluster](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-kubernetes-service) eller [distribuera en modell till Azure Container instances](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-container-instance) för att distribuera dina modeller till webb tjänst slut punkter, och aktivera Azure Application insikter för att utnyttja data insamling och slut punkts övervakning
* Se [MLOps: hantera, distribuera och övervaka modeller med Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-model-management-and-deployment) för att lära dig mer om att använda data som samlas in från modeller i produktion. Sådana data kan hjälpa till att kontinuerligt förbättra din Machine Learning-process
