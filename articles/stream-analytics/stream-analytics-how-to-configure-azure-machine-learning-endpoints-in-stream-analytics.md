---
title: Använda Machine Learning-slutpunkter i Azure Stream Analytics
description: Den här artikeln beskriver hur du använder maskinkod användardefinierade funktioner i Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.openlocfilehash: cea810a5e57f4b10c170038108226c4e0f1320bc
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53104952"
---
# <a name="machine-learning-integration-in-stream-analytics"></a>Machine Learning-integrering i Stream Analytics
Stream Analytics har stöd för användardefinierade funktioner som Azure Machine Learning-slutpunkter. REST API-stöd för den här funktionen beskrivs i den [Stream Analytics REST API-bibliotek](https://msdn.microsoft.com/library/azure/dn835031.aspx). Den här artikeln ger ytterligare information som behövs för lyckade implementeringen av den här funktionen i Stream Analytics. En självstudiekurs som också har publicerats och är tillgänglig [här](stream-analytics-machine-learning-integration-tutorial.md).

## <a name="overview-azure-machine-learning-terminology"></a>Översikt: Azure Machine Learning-terminologi
Microsoft Azure Machine Learning ger ett där flera användare kan dra och släpp verktyg som du kan använda för att skapa, testa och distribuera prediktiva Analyslösningar utifrån dina data. Det här verktyget kallas den *Azure Machine Learning Studio*. Studio används för att interagera med Machine Learning-resurser och enkelt bygga, testa och iterera på din design. Dessa resurser och deras definitioner finns nedan.

* **Arbetsytan**: den *arbetsytan* är en behållare som innehåller alla andra Machine Learning-resurser tillsammans i en behållare för hantering och kontroll.
* **Experiment**: *experiment* skapas av datavetare och utnyttja datamängder och träna en maskininlärningsmodell.
* **Slutpunkten**: *slutpunkter* är Azure Machine Learning-objektet som används för att ta funktioner som indata, tillämpa en angiven modell för maskininlärning och returnerar poängsatta utdata.
* **Bedömning av webbtjänsten**: A *bedömning av webbtjänsten* är en uppsättning slutpunkter som nämns ovan.

Varje slutpunkt har API: er för batchkörning och synkron körning. Stream Analytics använder synkron körning. Den specifika tjänsten kallas en [begäran/svar-tjänsten](../machine-learning/studio/consume-web-services.md) i AzureML studio.

## <a name="machine-learning-resources-needed-for-stream-analytics-jobs"></a>Machine Learning-resurser som krävs för Stream Analytics-jobb
Jobb för Stream Analytics bearbetar en begäran/svar-slutpunkt, en [apikey](../machine-learning/machine-learning-connect-to-azure-machine-learning-web-service.md), och alla behövs för att utföra en swagger-definition. Stream Analytics har en ytterligare slutpunkt som skapar URL: en för swagger-slutpunkten, letar upp gränssnittet och returnerar en standard UDF-definitionen för användaren.

## <a name="configure-a-stream-analytics-and-machine-learning-udf-via-rest-api"></a>Konfigurera ett Stream Analytics och Machine Learning-UDF via REST-API
Med hjälp av REST API: er kan du konfigurera ditt jobb för att anropa maskinkod för Azure functions. Stegen är följande:

1. Skapa ett Stream Analytics-jobb
2. Definiera indata
3. Definiera utdata
4. Skapa en användardefinierad funktion (UDF)
5. Skriv en Stream Analytics-omvandling som anropar en användardefinierad funktion
6. Starta jobbet

## <a name="creating-a-udf-with-basic-properties"></a>Skapa en UDF med grundläggande egenskaper
Till exempel följande exempelkoden skapar en skalär UDF med namnet *newudf* som binder till en Azure Machine Learning-slutpunkt. Observera att den *endpoint* (tjänstens URI) finns på hjälpsidan för API för den valda tjänsten och *apiKey* kan hittas på huvudsidan för tjänster.

````
    PUT : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>  
````

Exempel-begärandetexten:  

```json
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77fb4b46bf2a30c63c078dca/services/b7be5e40fd194258796fb402c1958eaf/execute ",
                        "apiKey": "replacekeyhere"
                    }
                }
            }
        }
    }
```

## <a name="call-retrievedefaultdefinition-endpoint-for-default-udf"></a>Anropa RetrieveDefaultDefinition slutpunkt för standard UDF
När stommen UDF har skapats krävs fullständiga definitionen av en användardefinierad funktion. RetreiveDefaultDefinition slutpunkten kan du hämta standarddefinitionen för en skalärfunktion som är bunden till en Azure Machine Learning-slutpunkt. Nyttolasten nedan måste du hämta standard UDF-definitionen för en skalärfunktion som är bunden till en Azure Machine Learning-slutpunkt. Det Ange inte den faktiska slutpunkten som har redan angetts under PUT-begäran. Stream Analytics anropar slutpunkten som tillhandahölls i begäran om det anges uttryckligen. Annars används den som ursprungligen refereras. Här sträng UDF-tar en enda sträng parametern (en mening) och returnerar en enda utdata av typen som anger etiketten ”attitydanalys” för den meningen.

````
POST : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>/RetrieveDefaultDefinition?api-version=<apiVersion>
````

Exempel-begärandetexten:  

```json
    {
        "bindingType": "Microsoft.MachineLearning/WebService",
        "bindingRetrievalProperties": {
            "executeEndpoint": null,
            "udfType": "Scalar"
        }
    }
```

Ett exempel på utdata detta skulle titta något i stil nedan.  

```json
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
```

## <a name="patch-udf-with-the-response"></a>Patch-UDF med svaret
Nu en användardefinierad funktion måste korrigeras med föregående svar, enligt nedan.

````
PATCH : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
````

Begärandetexten (utdata från RetrieveDefaultDefinition):

```json
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
```

## <a name="implement-stream-analytics-transformation-to-call-the-udf"></a>Implementera Stream Analytics-transformering för att anropa en användardefinierad funktion
Nu frågar en användardefinierad funktion (här med namnet scoreTweet) för varje händelse och skriva ett svar för händelsen till utdata.  

```json
    {
        "name": "transformation",
        "properties": {
            "streamingUnits": null,
            "query": "select *,scoreTweet(Tweet) TweetSentiment into blobOutput from blobInput"
        }
    }
```


## <a name="get-help"></a>Få hjälp
Om du behöver mer hjälp kan du besöka vårt [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
