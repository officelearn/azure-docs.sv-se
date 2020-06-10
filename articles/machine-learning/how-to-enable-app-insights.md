---
title: Övervaka och samla in data från Machine Learning webb tjänst slut punkter
titleSuffix: Azure Machine Learning
description: Övervaka webb tjänster som distribueras med Azure Machine Learning med hjälp av Azure Application Insights
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: jmartens
ms.author: larryfr
author: blackmist
ms.date: 06/09/2020
ms.custom: tracking-python
ms.openlocfilehash: 021d548c56810021af7257b25c40d7d4cc68ec12
ms.sourcegitcommit: d7fba095266e2fb5ad8776bffe97921a57832e23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/09/2020
ms.locfileid: "84629448"
---
# <a name="monitor-and-collect-data-from-ml-web-service-endpoints"></a>Övervaka och samla in data från ML webb tjänst slut punkter
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig hur du samlar in data från och övervakar modeller som har distribuerats till webb tjänst slut punkter i Azure Kubernetes service (AKS) eller Azure Container Instances (ACI) genom att aktivera Azure Application insikter via 
* [Azure Machine Learning python SDK](#python)
* [Azure Machine Learning Studio](#studio) påhttps://ml.azure.com

Förutom att samla in en slut punkts utdata och svar kan du övervaka:

* Begär ande frekvens, svars tider och felaktiga frekvenser
* Beroende frekvens, svars tider och felaktiga frekvenser
* Undantag

[Läs mer om Azure Application insikter](../azure-monitor/app/app-insights-overview.md). 


## <a name="prerequisites"></a>Krav

* Om du inte har någon Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag

* En Azure Machine Learning arbets yta, en lokal katalog som innehåller dina skript och Azure Machine Learning SDK för python installerat. Information om hur du får dessa krav finns i så här [konfigurerar du en utvecklings miljö](how-to-configure-environment.md)

* En utbildad maskin inlärnings modell som ska distribueras till Azure Kubernetes service (AKS) eller Azure Container Instance (ACI). Om du inte har något kan du läsa själv studie kursen [träna bild klassificerings modell](tutorial-train-models-with-aml.md)

## <a name="web-service-metadata-and-response-data"></a>Metadata och svars data för webb tjänst

>[!Important]
> Azure Application Insights loggar bara nytto laster på upp till 64 kB. Om den här gränsen nås loggas bara de senaste utflödena av modellen. 

Om du vill logga information om en begäran till webb tjänsten lägger du till `print` instruktioner till din score.py-fil. Varje `print` instruktion resulterar i en post i spårnings tabellen i Application Insights under meddelandet `STDOUT` . Innehållet i `print` instruktionen kommer att finnas under `customDimensions` och sedan `Contents` i spårnings tabellen. Om du skriver ut en JSON-sträng skapar den en hierarkisk data struktur i spårningens utdata under `Contents` .

Du kan fråga Azure Application insikter direkt för att komma åt dessa data eller konfigurera en [kontinuerlig export](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) till ett lagrings konto för längre kvarhållning eller ytterligare bearbetning. Modell data kan sedan användas i Azure Machine Learning för att konfigurera etiketter, omskolning, bedömning, data analys eller annan användning. 

<a name="python"></a>

## <a name="use-python-sdk-to-configure"></a>Använd python SDK för att konfigurera 

### <a name="update-a-deployed-service"></a>Uppdatera en distribuerad tjänst

1. Identifiera tjänsten i din arbets yta. Värdet för `ws` är namnet på din arbets yta

    ```python
    from azureml.core.webservice import Webservice
    aks_service= Webservice(ws, "my-service-name")
    ```
2. Uppdatera tjänsten och aktivera Azure Application insikter

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>Logga anpassade spårningar i din tjänst

Om du vill logga anpassade spår följer du standard distributions processen för AKS eller ACI i avsnittet [så här distribuerar och var](how-to-deploy-and-where.md) dokumentet. Använd sedan följande steg:

1. Om du vill skicka data till Application Insights under härledningen uppdaterar du bedömnings filen genom att lägga till utskrifts instruktioner. Om du vill logga mer komplex information, till exempel begär ande data och svar, kan du se en JSON-struktur. I följande exempel score.py-fil loggar tiden som modellen initieras, indata och utdata under härledningen och hur lång tid det tar att utföra fel:
    
    ```python
    import pickle
    import json
    import numpy 
    from sklearn.externals import joblib
    from sklearn.linear_model import Ridge
    from azureml.core.model import Model
    import time

    def init():
        global model
        #Print statement for appinsights custom traces:
        print ("model initialized" + time.strftime("%H:%M:%S"))
        
        # note here "sklearn_regression_model.pkl" is the name of the model registered under the workspace
        # this call should return the path to the model.pkl file on the local disk.
        model_path = Model.get_model_path(model_name = 'sklearn_regression_model.pkl')
        
        # deserialize the model file back into a sklearn model
        model = joblib.load(model_path)
    

    # note you can pass in multiple rows for scoring
    def run(raw_data):
        try:
            data = json.loads(raw_data)['data']
            data = numpy.array(data)
            result = model.predict(data)
            # Log the input and output data to appinsights:
            info = {
                "input": raw_data,
                "output": result.tolist()
                }
            print(json.dumps(info))
            # you can return any datatype as long as it is JSON-serializable
            return result.tolist()
        except Exception as e:
            error = str(e)
            print (error + time.strftime("%H:%M:%S"))
            return error
    ```

2. Uppdatera tjänst konfigurationen
    
    ```python
    config = Webservice.deploy_configuration(enable_app_insights=True)
    ```

3. Bygg en avbildning och distribuera den på [AKS eller ACI](how-to-deploy-and-where.md).

### <a name="disable-tracking-in-python"></a>Inaktivera spårning i python

Om du vill inaktivera Azure Application insikter använder du följande kod:

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```

<a name="studio"></a>

## <a name="use-azure-machine-learning-studio-to-configure"></a>Använd Azure Machine Learning Studio för att konfigurera

Du kan också aktivera Azure Application insikter från Azure Machine Learning Studio när du är redo att distribuera din modell med de här stegen.

1. Logga in på din arbets yta påhttps://ml.azure.com/
1. Gå till **modeller** och välj vilken modell du vill distribuera
1. Välj **+ distribuera**
1. Fyll i formuläret **distribuera modell**
1. Expandera menyn **Avancerat**

    ![Distribuera formulär](./media/how-to-enable-app-insights/deploy-form.png)
1. Välj **aktivera Application Insights diagnostik och data insamling**

    ![Aktivera App Insights](./media/how-to-enable-app-insights/enable-app-insights.png)
## <a name="evaluate-data"></a>Utvärdera data
Din tjänsts data lagras i ditt Azure Application Insights-konto inom samma resurs grupp som Azure Machine Learning.
Så här visar du det:

1. Gå till arbets ytan Azure Machine Learning i [Azure Portal](https://ms.portal.azure.com/) och klicka på Application Insights länken

    [![AppInsightsLoc](./media/how-to-enable-app-insights/AppInsightsLoc.png)](././media/how-to-enable-app-insights/AppInsightsLoc.png#lightbox)

1. Välj __loggar__på fliken **Översikt** eller i avsnittet __övervakning__ i listan till vänster.

    [![Fliken Översikt för övervakning](./media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

1. Om du vill visa information som loggats från score.py-filen tittar du på tabellen __spår__ . Följande fråga söker efter loggar där __indatavärdet__ loggades:

    ```kusto
    traces
    | where customDimensions contains "input"
    | limit 10
    ```

   [![spårnings data](./media/how-to-enable-app-insights/model-data-trace.png)](././media/how-to-enable-app-insights/model-data-trace.png#lightbox)

Mer information om hur du använder Azure Application Insights finns i [Application Insights?](../azure-monitor/app/app-insights-overview.md).

## <a name="export-data-for-further-processing-and-longer-retention"></a>Exportera data för vidare bearbetning och längre kvarhållning

>[!Important]
> Azure Application Insights stöder endast export till Blob Storage. Ytterligare begränsningar för den här export funktionen visas i [Exportera telemetri från App Insights](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry#continuous-export-advanced-storage-configuration).

Du kan använda Azure Application insightss [löpande export](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) för att skicka meddelanden till ett lagrings konto som stöds, där en längre kvarhållning kan ställas in. Data lagras i JSON-format och kan enkelt analyseras för att extrahera modell data. 

Azure Data Factory, Azure ML-pipeliner eller andra data bearbetnings verktyg kan användas för att transformera data vid behov. När du har transformerat data kan du registrera den med Azure Machine Learning arbets ytan som en data uppsättning. Det gör du i så [här skapar och registrerar du data uppsättningar](how-to-create-register-datasets.md).

   [![Löpande export](./media/how-to-enable-app-insights/continuous-export-setup.png)](././media/how-to-enable-app-insights/continuous-export-setup.png)


## <a name="example-notebook"></a>Exempel antecknings bok

Den [Enable-App-Insights-in-producting-service. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) Notebook visar begrepp i den här artikeln. 
 
[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nästa steg

* Se [distribuera en modell till ett Azure Kubernetes service-kluster](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-kubernetes-service) eller [distribuera en modell till Azure Container instances](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-container-instance) för att distribuera dina modeller till webb tjänst slut punkter, och aktivera Azure Application insikter för att utnyttja data insamling och slut punkts övervakning
* Se [MLOps: hantera, distribuera och övervaka modeller med Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-model-management-and-deployment) för att lära dig mer om att använda data som samlas in från modeller i produktion. Sådana data kan hjälpa till att kontinuerligt förbättra din Machine Learning-process
