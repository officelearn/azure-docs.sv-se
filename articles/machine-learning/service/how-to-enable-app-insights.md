---
title: Konfigurera Azure Application Insights för att övervaka ML-modeller
titleSuffix: Azure Machine Learning service
description: Övervaka webbtjänster som distribueras med Azure Machine Learning-tjänsten med Azure Application Insights
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: marthalc
author: marthalc
ms.date: 04/02/2019
ms.custom: seoapril2019
ms.openlocfilehash: 2e481a388d8cbd6baf66b95c74449396b2e70f7d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60820104"
---
# <a name="monitor-your-azure-machine-learning-models-with-application-insights"></a>Övervaka dina Azure Machine Learning-modeller med Application Insights

I den här artikeln får du lära dig hur du ställer in Azure Application Insights för Azure Machine Learning-tjänsten. Application Insights ger dig möjlighet att övervaka:
* Begära frekvens, svarstider och Felfrekvens.
* Beroendefrekvens, svarstider och Felfrekvens.
* Undantag.

[Läs mer om Application Insights](../../azure-monitor/app/app-insights-overview.md). 


## <a name="prerequisites"></a>Nödvändiga komponenter

* Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria versionen eller betalversionen av Azure Machine Learning-tjänsten](https://aka.ms/AMLFree) i dag.

* En lokal katalog som innehåller dina skript och Azure Machine Learning-SDK för Python installerat en Azure Machine Learning-arbetsyta. Information om hur du hämtar dessa krav finns i [så här konfigurerar du en utvecklingsmiljö](how-to-configure-environment.md).
* En tränad modell för maskininlärning för distribution till Azure Kubernetes Service (AKS) eller Azure Container-instans (ACI). Om du inte har någon kan se den [träningsmodell bild klassificering](tutorial-train-models-with-aml.md) självstudien.


## <a name="use-sdk-to-configure"></a>Använd SDK för att konfigurera 

### <a name="update-a-deployed-service"></a>Uppdatera en distribuerad tjänst
1. Identifiera tjänsten i din arbetsyta. Värdet för `ws` är namnet på din arbetsyta.

    ```python
    from azureml.core.webservice import Webservice
    aks_service= Webservice(ws, "my-service-name")
    ```
2. Uppdatera din tjänst och aktivera Application Insights. 

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>Anpassade loggspårningar i din tjänst
Om du vill logga anpassade spårningar följer standard distributionsprocessen för AKS eller ACI i den [hur du distribuerar och var](how-to-deploy-and-where.md) dokumentet. Använd sedan följande steg:

1. Uppdatera bedömningsfil genom att lägga till Skriv ut utdrag.
    
    ```python
    print ("model initialized" + time.strftime("%H:%M:%S"))
    ```

2. Uppdatera tjänstekonfigurationen.
    
    ```python
    config = Webservice.deploy_configuration(enable_app_insights=True)
    ```

3. Skapa en avbildning och distribuera den på [AKS](how-to-deploy-to-aks.md) eller [ACI](how-to-deploy-to-aci.md).  

### <a name="disable-tracking-in-python"></a>Inaktivera spårning i Python

Om du vill inaktivera Application Insights, Använd följande kod:

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```
    
## <a name="use-portal-to-configure"></a>Använd portalen för att konfigurera

Du kan aktivera och inaktivera Application Insights i Azure-portalen.

1. I den [Azure-portalen](https://portal.azure.com), öppna din arbetsyta.

1. På den **distributioner** väljer du en tjänst där du vill aktivera Application Insights.

   [![Lista över tjänster på fliken distributioner](media/how-to-enable-app-insights/Deployments.PNG)](./media/how-to-enable-app-insights/Deployments.PNG#lightbox)

3. Välj **Redigera**.

   [![Knappen Redigera](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

4. I **avancerade inställningar**väljer den **aktivera AppInsights-diagnostik** markerar du kryssrutan.

   [![Markerad kryssruta för att aktivera diagnostik](media/how-to-enable-app-insights/AdvancedSettings.png)](./media/how-to-enable-app-insights/AdvancedSettings.png#lightbox)

1. Välj **uppdatering** längst ned på skärmen för att tillämpa ändringarna. 

### <a name="disable"></a>Inaktivera
1. I den [Azure-portalen](https://portal.azure.com), öppna din arbetsyta.
1. Välj **distributioner**, Välj tjänsten och välj **redigera**.

   [![Använd redigeringsknappen](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

1. I **avancerade inställningar**, avmarkera de **aktivera AppInsights-diagnostik** markerar du kryssrutan. 

   [![Avmarkerad kryssruta för att aktivera diagnostik](media/how-to-enable-app-insights/uncheck.png)](./media/how-to-enable-app-insights/uncheck.png#lightbox)

1. Välj **uppdatering** längst ned på skärmen för att tillämpa ändringarna. 
 

## <a name="evaluate-data"></a>Utvärdera data
Din tjänsts data lagras i ditt Application Insights-konto, inom samma resursgrupp som din Azure Machine Learning-tjänsten.
Visa den:
1. Gå till din Machine Learning-tjänsten arbetsyta i den [Azure-portalen](https://portal.azure.com) och klicka på länken för Application Insights.

    [![AppInsightsLoc](media/how-to-enable-app-insights/AppInsightsLoc.png)](./media/how-to-enable-app-insights/AppInsightsLoc.png#lightbox)

1. Välj den **översikt** fliken för att se en grundläggande uppsättning mått för din tjänst.

   [![Översikt över](media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

3. Om du vill söka i dina anpassade spårningar, Välj **Analytics**.
4. Markera under schemat **spårningar**. Välj sedan **kör** att köra frågan. Data ska visas i tabellformat och ska mappa till dina anpassade anrop i din bedömningsfilen. 

   [![Anpassade spårningar](media/how-to-enable-app-insights/logs.png)](./media/how-to-enable-app-insights/logs.png#lightbox)

Mer information om hur du använder Application Insights finns [vad är Application Insights?](../../azure-monitor/app/app-insights-overview.md).
    

## <a name="example-notebook"></a>Exempel-anteckningsbok

Den [how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) notebook demonstrerar begreppen i den här artikeln. 
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nästa steg
Du kan också samla in data på dina modeller i produktion. Läs artikeln [samla in data för modeller i produktion](how-to-enable-data-collection.md). 

Läs även [Azure Monitor för behållare](https://docs.microsoft.com/azure/monitoring/monitoring-container-insights-overview?toc=%2fazure%2fmonitoring%2ftoc.json).
