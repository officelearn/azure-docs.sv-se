---
title: Aktivera application insights för Azure Machine Learning-tjänsten i produktion
description: Lär dig hur du ställer in Application Insights för Azure Machine Learning-tjänsten som har distribuerats i ett Azure Kubernetes Service
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: marthalc
author: marthalc
ms.date: 10/01/2018
ms.openlocfilehash: 45871ab515c7ffd9520b1d77d3fd1e77abcc29ef
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2018
ms.locfileid: "49114575"
---
# <a name="monitor-your-azure-machine-learning-models-in-production-with-application-insights"></a>Övervaka dina Azure Machine Learning-modeller i produktion med Application Insights

I den här artikeln får du lära dig hur du ställer in **Programinsikter** för din **Azure Machine Learning** service. När du har aktiverat, kan Application Insights du övervaka:
* Begär frekvens, svarstider och Felfrekvens
* Beroendefrekvens, svarstider och Felfrekvens
* Undantag

Läs mer om Application Insights [här](../../application-insights/app-insights-overview.md). 

## <a name="prerequisites"></a>Förutsättningar
* En Azure-prenumeration. Om du inte har ett konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
* En lokal katalog som innehåller dina skript och Azure Machine Learning-SDK för Python installerat en Azure Machine Learning-arbetsyta. Lär dig hur du hämtar dessa krav med hjälp av den [så här konfigurerar du en utvecklingsmiljö](how-to-configure-environment.md) dokumentet.
* En tränad modell för maskininlärning som ska distribueras till Azure Kubernetes Service (AKS). Om du inte har någon kan se den [träna bild klassificeringsmodellen](tutorial-train-models-with-aml.md) självstudien.
* En [AKS-kluster](how-to-deploy-to-aks.md).

## <a name="enable--disable-in-the-portal"></a>Aktivera och inaktivera i portalen

Du kan aktivera och inaktivera Application Insights i Azure-portalen.

### <a name="enable"></a>Aktivera

1. I [Azure-portalen](https://portal.azure.com), öppna din arbetsyta.

1. Gå till dina distributioner och välj tjänsten där du vill aktivera Application Insights.

   [![Distributioner](media/how-to-enable-app-insights/Deployments.PNG)](./media/how-to-enable-app-insights/Deployments.PNG#lightbox)

3. Klicka på **redigera** och gå till **avancerade inställningar**.

   [![Redigera](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

4. I **avancerade inställningar** Välj **aktivera Application Insights-diagnostik**.

   [![Redigera](media/how-to-enable-app-insights/AdvancedSettings.png)](./media/how-to-enable-app-insights/AdvancedSettings.png#lightbox)

1. Välj **uppdatering** längst ned på skärmen för att tillämpa ändringarna. 

### <a name="disable"></a>Inaktivera
Om du vill inaktivera Application Insights i Azure-portalen gör du följande:

1. Logga in på Azure portal på https://portal.azure.com.
1. Gå till din arbetsyta.
1. Välj **distributioner**, sedan **Välj tjänst**, och sedan **redigera**.

   [![Redigera](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

1. I **avancerade inställningar**, avmarkerar du alternativet **aktivera AppInsights-diagnostik**. 

   [![Avmarkera](media/how-to-enable-app-insights/uncheck.png)](./media/how-to-enable-app-insights/uncheck.png#lightbox)

1. Välj **uppdatering** längst ned på skärmen för att tillämpa ändringarna. 

## <a name="enable--disable-from-the-sdk"></a>Aktivera och inaktivera från SDK

### <a name="update-a-deployed-service"></a>Uppdatera en distribuerad tjänst
1. Identifiera tjänsten i din arbetsyta (ws = namnet på din arbetsyta)

    ```python
    aks_service= Webservice(ws, "my-service-name")
    ```
2. Uppdatera din tjänst och aktivera Application Insights. 

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>Anpassade loggspårningar i din tjänst
Om du vill anpassade loggspårningar, följer du de [standard distributionsprocessen för AKS](how-to-deploy-to-aks.md) och kommer du att:

1. Uppdatera bedömningsfilen genom att lägga till Skriv ut utdrag.
    
    ```python
    print ("model initialized" + time.strftime("%H:%M:%S"))
    ```

2. Uppdateringskonfigurationen för aks.
    
    ```python
    aks_config = AksWebservice.deploy_configuration(enable_app_insights=True)
    ```

3. [Skapa avbildningen och distribuera den.](how-to-deploy-to-aks.md)  

### <a name="disable-tracking-in-python"></a>Inaktivera spårning i Python

Om du vill inaktivera Application Insights, Använd följande kod:

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```
    

## <a name="evaluate-data"></a>Utvärdera data
Data för din tjänst lagras i Application Insights-konto i din arbetsyta för Azure Machine Learning-tjänsten är i samma resursgrupp.
Visa den:
1. Gå till din resursgrupp i den [Azure-portalen](https://portal.azure.com) och klickar på Application Insights-resursen. 
2. Den **översikt** fliken visas en uppsättning mått för din tjänst.

   [![Översikt över](media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

3. Titta på din anpassade spårningar genom att klicka på **Analytics**.
4. I schemaavsnittet, klickar du på **spårningar** och sedan **kör** din fråga. Data ska visas i tabellformat ned nedan och ska mappa till dina anpassade anrop i din bedömningsfilen. 

   [![Anpassade spårningar](media/how-to-enable-app-insights/logs.png)](./media/how-to-enable-app-insights/logs.png#lightbox)

Klicka på [här](../../application-insights/app-insights-overview.md) mer information om hur du använder Application Insights.
    

## <a name="example-notebook"></a>Exempel-anteckningsbok

Den [00. komma Started/13.enable-app-insights-in-production-service.ipynb](https://github.com/Azure/MachineLearningNotebooks/tree/master/01.getting-started/13.enable-app-insights) notebook demonstrerar begreppen i den här artikeln.  Hämta den här anteckningsboken:
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nästa steg
Du kan också samla in data på dina modeller i produktion. Läs artikeln [samla in data för modeller i produktion](how-to-enable-data-collection.md) 
