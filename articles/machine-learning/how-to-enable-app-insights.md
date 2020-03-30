---
title: Övervaka och samla in data från slutpunkter för Machine Learning-webbtjänsten
titleSuffix: Azure Machine Learning
description: Övervaka webbtjänster som distribueras med Azure Machine Learning med hjälp av Azure Application Insights
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: larryfr
author: blackmist
ms.date: 03/12/2020
ms.openlocfilehash: 464ec1fcf0986dc04bd92bbe9e31b5675e5822d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136201"
---
# <a name="monitor-and-collect-data-from-ml-web-service-endpoints"></a>Övervaka och samla in data från ML-webbtjänstslutpunkter
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig hur du samlar in data från och övervakar modeller som distribueras till slutpunkter för webbtjänster i Azure Kubernetes Service (AKS) eller Azure Container Instances (ACI) genom att aktivera Azure Application Insights via 
* [Azure Machine Learning Python SDK](#python)
* [Azure Machine Learning studio](#studio) påhttps://ml.azure.com

Förutom att samla in utdata och svar på en slutpunkt kan du övervaka:

* Begär frekvenser, svarstider och felfrekvens
* Beroendefrekvens, svarstider och felfrekvens
* Undantag

[Läs mer om Azure Application Insights](../azure-monitor/app/app-insights-overview.md). 


## <a name="prerequisites"></a>Krav

* Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag

* En Azure Machine Learning-arbetsyta, en lokal katalog som innehåller dina skript och Azure Machine Learning SDK för Python installerat. Mer information om hur du får dessa förutsättningar finns i [Så här konfigurerar du en utvecklingsmiljö](how-to-configure-environment.md)

* En tränad maskininlärningsmodell som ska distribueras till Azure Kubernetes Service (AKS) eller Azure Container Instance (ACI). Om du inte har någon, se [självstudiekursen för klassen Tågbildklassificeringsmodell](tutorial-train-models-with-aml.md)

## <a name="web-service-metadata-and-response-data"></a>Metadata och svarsdata för webbtjänsten

>[!Important]
> Azure Application Insights loggar bara nyttolaster på upp till 64 kb. Om den här gränsen nås loggas endast de senaste utgångarna av modellen. 

Metadata och svar på tjänsten - som motsvarar webbtjänstens metadata och modellens förutsägelser - loggas till `"model_data_collection"`Azure Application Insights-spårningarna under meddelandet . Du kan fråga Azure Application Insights direkt för att komma åt dessa data, eller ställa in en [kontinuerlig export](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) till ett lagringskonto för längre kvarhållning eller vidare bearbetning. Modelldata kan sedan användas i Azure Machine Learning för att ställa in märkning, omskolning, explainability, dataanalys eller annan användning. 

<a name="python"></a>

## <a name="use-python-sdk-to-configure"></a>Använda Python SDK för att konfigurera 

### <a name="update-a-deployed-service"></a>Uppdatera en distribuerad tjänst

1. Identifiera tjänsten på arbetsytan. Värdet för `ws` är namnet på arbetsytan

    ```python
    from azureml.core.webservice import Webservice
    aks_service= Webservice(ws, "my-service-name")
    ```
2. Uppdatera din tjänst och aktivera Azure Application Insights

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>Logga anpassade spårningar i din tjänst

Om du vill logga anpassade spårningar följer du standarddistributionsprocessen för AKS eller ACI i hur du distribuerar och var dokumentet ska [distribueras.](how-to-deploy-and-where.md) Använd sedan följande steg:

1. Uppdatera bedömningsfilen genom att lägga till utskriftssatser
    
    ```python
    print ("model initialized" + time.strftime("%H:%M:%S"))
    ```

2. Uppdatera tjänstkonfigurationen
    
    ```python
    config = Webservice.deploy_configuration(enable_app_insights=True)
    ```

3. Skapa en avbildning och distribuera den på [AKS eller ACI](how-to-deploy-and-where.md).

### <a name="disable-tracking-in-python"></a>Inaktivera spårning i Python

Om du vill inaktivera Azure Application Insights använder du följande kod:

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```

<a name="studio"></a>

## <a name="use-azure-machine-learning-studio-to-configure"></a>Använda Azure Machine Learning studio för att konfigurera

Du kan också aktivera Azure Application Insights från Azure Machine Learning studio när du är redo att distribuera din modell med dessa steg.

1. Logga in på din arbetsyta påhttps://ml.azure.com/
1. Gå till **Modeller** och välj vilken modell du vill distribuera
1. Välj **+Deploy**
1. Fylla i formuläret **Distribuera modell**
1. Expandera menyn **Avancerat**

    ![Distribuera formulär](./media/how-to-enable-app-insights/deploy-form.png)
1. Välj **Aktivera diagnostik och datainsamling av programinsikter**

    ![Aktivera appstatistik](./media/how-to-enable-app-insights/enable-app-insights.png)
## <a name="evaluate-data"></a>Utvärdera data
Tjänstens data lagras i ditt Azure Application Insights-konto, inom samma resursgrupp som Azure Machine Learning.
Så här visar du den:

1. Gå till arbetsytan Azure Machine Learning i [Azure-portalen](https://ms.portal.azure.com/) och klicka på länken Application Insights

    [![AppInsightsLoc](./media/how-to-enable-app-insights/AppInsightsLoc.png)](././media/how-to-enable-app-insights/AppInsightsLoc.png#lightbox)

1. Välj fliken **Översikt** om du vill visa en grundläggande uppsättning mått för tjänsten

   [![Översikt](./media/how-to-enable-app-insights/overview.png)](././media/how-to-enable-app-insights/overview.png#lightbox)

1. Om du vill titta på metadata och svar på webbtjänstens begäran väljer du **tabellen begäranden** i avsnittet **Loggar (Analytics)** och väljer **Kör** för att visa begäranden

   [![Modelldata](./media/how-to-enable-app-insights/model-data-trace.png)](././media/how-to-enable-app-insights/model-data-trace.png#lightbox)


3. Om du vill titta på dina anpassade spårningar väljer du **Analytics**
4. Välj **Spårningar**i schemaavsnittet . Välj sedan **Kör** för att köra frågan. Data ska visas i ett tabellformat och mappas till dina anpassade samtal i bedömningsfilen

   [![Anpassade spårningar](./media/how-to-enable-app-insights/logs.png)](././media/how-to-enable-app-insights/logs.png#lightbox)

Mer information om hur du använder Azure Application Insights finns i [Vad är Programinsikter?](../azure-monitor/app/app-insights-overview.md).

## <a name="export-data-for-further-processing-and-longer-retention"></a>Exportera data för vidare bearbetning och längre lagring

>[!Important]
> Azure Application Insights stöder endast export till blob-lagring. Ytterligare begränsningar för den här exportfunktionen visas i [Exportera telemetri från App Insights](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry#continuous-export-advanced-storage-configuration).

Du kan använda Azure Application Insights [kontinuerliga export](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) för att skicka meddelanden till ett lagringskonto som stöds, där en längre kvarhållning kan anges. Meddelandena `"model_data_collection"` lagras i JSON-format och kan enkelt tolkas för att extrahera modelldata. 

Azure Data Factory, Azure ML Pipelines eller andra databehandlingsverktyg kan användas för att omvandla data efter behov. När du har omvandlat data kan du sedan registrera dem med arbetsytan Azure Machine Learning som en datauppsättning. Det gör du genom att se [Så här skapar och registrerar du datauppsättningar](how-to-create-register-datasets.md).

   [![Kontinuerlig export](./media/how-to-enable-app-insights/continuous-export-setup.png)](././media/how-to-enable-app-insights/continuous-export-setup.png)


## <a name="example-notebook"></a>Exempel på anteckningsbok

Den [aktivera-app-insights-in-production-service.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) anteckningsboken visar begrepp i den här artikeln. 
 
[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nästa steg

* Se [hur du distribuerar en modell till ett Azure Kubernetes-tjänstkluster](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-kubernetes-service) eller [hur du distribuerar en modell till Azure Container Instances](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-container-instance) för att distribuera dina modeller till slutpunkter för webbtjänster och aktivera Azure Application Insights för att utnyttja datainsamling och slutpunktsövervakning
* Se [MLOps: Hantera, distribuera och övervaka modeller med Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-model-management-and-deployment) om du vill veta mer om hur du använder data som samlats in från modeller i produktion. Sådana data kan bidra till att kontinuerligt förbättra din maskininlärningsprocess
