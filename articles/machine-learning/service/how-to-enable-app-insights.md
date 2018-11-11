---
title: Aktivera Application Insights för Azure Machine Learning-tjänsten i produktion
description: Lär dig hur du ställer in Application Insights för Azure Machine Learning-tjänsten för distribution till Azure Kubernetes Service
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: marthalc
author: marthalc
ms.date: 10/01/2018
ms.openlocfilehash: 962090340cad6bcd95245cffe16c25f08a98226b
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2018
ms.locfileid: "51300769"
---
# <a name="monitor-your-azure-machine-learning-models-in-production-with-application-insights"></a>Övervaka dina Azure Machine Learning-modeller i produktion med Application Insights

I den här artikeln får du lära dig hur du ställer in Azure Application Insights för Azure Machine Learning-tjänsten. Application Insights ger dig möjlighet att övervaka:
* Begära frekvens, svarstider och Felfrekvens.
* Beroendefrekvens, svarstider och Felfrekvens.
* Undantag.

[Läs mer om Application Insights](../../application-insights/app-insights-overview.md). 

## <a name="prerequisites"></a>Förutsättningar
* En Azure-prenumeration. Om du inte har ett konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
* En lokal katalog som innehåller dina skript och Azure Machine Learning-SDK för Python installerat en Azure Machine Learning-arbetsyta. Information om hur du hämtar dessa krav finns i [så här konfigurerar du en utvecklingsmiljö](how-to-configure-environment.md).
* En tränad modell för maskininlärning som ska distribueras till Azure Kubernetes Service (AKS). Om du inte har någon kan se den [träningsmodell bild klassificering](tutorial-train-models-with-aml.md) självstudien.
* En [AKS-kluster](how-to-deploy-to-aks.md).

## <a name="enable-and-disable-in-the-portal"></a>Aktivera och inaktivera i portalen

Du kan aktivera och inaktivera Application Insights i Azure-portalen.

### <a name="enable"></a>Aktivera

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

   [![Knappen Redigera](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

1. I **avancerade inställningar**, avmarkera de **aktivera AppInsights-diagnostik** markerar du kryssrutan. 

   [![Avmarkerad kryssruta för att aktivera diagnostik](media/how-to-enable-app-insights/uncheck.png)](./media/how-to-enable-app-insights/uncheck.png#lightbox)

1. Välj **uppdatering** längst ned på skärmen för att tillämpa ändringarna. 

## <a name="enable-and-disable-from-the-sdk"></a>Aktivera och inaktivera från SDK

### <a name="update-a-deployed-service"></a>Uppdatera en distribuerad tjänst
1. Identifiera tjänsten i din arbetsyta. Värdet för `ws` är namnet på din arbetsyta.

    ```python
    aks_service= Webservice(ws, "my-service-name")
    ```
2. Uppdatera din tjänst och aktivera Application Insights. 

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>Anpassade loggspårningar i din tjänst
Om du vill anpassade loggspårningar, följer du de [standard distributionsprocessen för AKS](how-to-deploy-to-aks.md). Sedan:

1. Uppdatera bedömningsfil genom att lägga till Skriv ut utdrag.
    
    ```python
    print ("model initialized" + time.strftime("%H:%M:%S"))
    ```

2. Uppdatera AKS-konfigurationen.
    
    ```python
    aks_config = AksWebservice.deploy_configuration(enable_app_insights=True)
    ```

3. [Skapa avbildningen och distribuera den](how-to-deploy-to-aks.md).  

### <a name="disable-tracking-in-python"></a>Inaktivera spårning i Python

Om du vill inaktivera Application Insights, Använd följande kod:

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```
    

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

Mer information om hur du använder Application Insights finns [vad är Application Insights?](../../application-insights/app-insights-overview.md).
    

## <a name="example-notebook"></a>Exempel-anteckningsbok

Den [00. komma Started/13.enable-app-insights-in-production-service.ipynb](https://github.com/Azure/MachineLearningNotebooks/tree/master/01.getting-started/13.enable-app-insights) notebook demonstrerar begreppen i den här artikeln.  Hämta den här anteckningsboken:
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nästa steg
Du kan också samla in data på dina modeller i produktion. Läs artikeln [samla in data för modeller i produktion](how-to-enable-data-collection.md). 


## <a name="other-references"></a>Andra referenser
* [Azure Monitor för behållare](https://docs.microsoft.com/azure/monitoring/monitoring-container-insights-overview?toc=%2fazure%2fmonitoring%2ftoc.json)
