---
title: Använda Machine Learning-slutpunkter i Azure Stream Analytics
description: Den här artikeln beskriver hur du använder datorn språk användardefinierade funktioner i Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: bdc6041258e4a5ecf602d19c0d912918f86af313
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
ms.locfileid: "30911213"
---
# <a name="machine-learning-integration-in-stream-analytics"></a>Datorn Learning integrering i Stream Analytics
Stream Analytics stöder användardefinierade funktioner som anropar till Azure Machine Learning-slutpunkter. REST API-stöd för den här funktionen beskrivs i den [Stream Analytics REST API-bibliotek](https://msdn.microsoft.com/library/azure/dn835031.aspx). Den här artikeln innehåller ytterligare information som behövs för lyckad implementering av den här funktionen i Stream Analytics. En självstudiekurs har även publicerats och är tillgänglig [här](stream-analytics-machine-learning-integration-tutorial.md).

## <a name="overview-azure-machine-learning-terminology"></a>Översikt: Terminologi för Azure Machine Learning
Microsoft Azure Machine Learning ger ett samarbete, dra och släpp verktyg som du kan använda för att bygga, testa och distribuera prediktiva Analyslösningar utifrån dina data. Det här verktyget kallas den *Azure Machine Learning Studio*. Studio används för att interagera med Machine Learning-resurser och enkelt skapa, testa och iterera på din design. Dessa resurser och deras definitioner som är lägre än.

* **Arbetsytan**: den *arbetsytan* är en behållare som innehåller alla andra Machine Learning resurser tillsammans i en behållare för hantering och kontroll.
* **Experiment**: *experiment* skapas av data-forskare att använda datamängder och träna en maskininlärningsmodell.
* **Slutpunkten**: *slutpunkter* är Azure Machine Learning-objektet som används för att ta funktioner som indata, tillämpa en angivna maskininlärningsmodell och returnera poängsatta utdata.
* **Bedömningen Webservice**: A *bedömningen webservice* är en uppsättning slutpunkter som nämns ovan.

Varje slutpunkt har API: er för batchkörning och synkron körning. Stream Analytics använder synkron körning. Specifika är tjänsten en [frågor och svar Service](../machine-learning/studio/consume-web-services.md) i AzureML studio.

## <a name="machine-learning-resources-needed-for-stream-analytics-jobs"></a>Machine Learning resurser som krävs för Stream Analytics-jobb
Jobbet bearbeta en begäran och svar-slutpunkt för Stream Analytics en [apikey](../machine-learning/machine-learning-connect-to-azure-machine-learning-web-service.md), och en swagger-definition är alla nödvändiga för att kunna köras. Stream Analytics har en ytterligare slutpunkt som konstruktioner URL: en för swagger-slutpunkt, letar upp gränssnittet och returnerar en UDF standarddefinition för användaren.

## <a name="configure-a-stream-analytics-and-machine-learning-udf-via-rest-api"></a>Konfigurera ett Stream Analytics och Maskininlärning UDF via REST-API
Du kan konfigurera ditt jobb för att anropa Azure Machine Language funktioner med hjälp av REST API: er. Stegen är följande:

1. Skapa ett Stream Analytics-jobb
2. Definiera indata
3. Definiera ett utgående
4. Skapa en användardefinierad funktion (UDF)
5. Skriva ett Stream Analytics-transformation som anropar en användardefinierad funktion
6. Starta jobbet

## <a name="creating-a-udf-with-basic-properties"></a>Skapa en UDF med grundläggande egenskaper
Exempelvis följande exempelkod skapar en skalär användardefinierad funktion med namnet *newudf* som binder till en Azure Machine Learning-slutpunkt. Observera att den *endpoint* (service URI) finns på sidan API hjälp för den valda tjänsten och *apiKey* kan hittas på huvudsidan för tjänster.

````
    PUT : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>  
````

Exempel frågans brödtext:  

````
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
````

## <a name="call-retrievedefaultdefinition-endpoint-for-default-udf"></a>Anropet RetrieveDefaultDefinition slutpunkt för standard UDF
När stommen UDF skapas krävs fullständig definitionen för en användardefinierad funktion. Slutpunkten RetreiveDefaultDefinition hjälper dig att hämta standarddefinitionen för en skalär funktion som är bunden till en Azure Machine Learning-slutpunkt. Nyttolasten nedan måste du hämta standarddefinitionen för UDF för en skalär funktion som är bunden till en Azure Machine Learning-slutpunkt. Faktisk slutpunkt anges inte som redan har angetts under PUT-förfrågan. Stream Analytics anropar den slutpunkt som anges i begäran om anges explicit. Annars används den som ursprungligen refererar till. Här sträng UDF-tar en enda string parametern (en mening) och returnerar en enda utdata av typen som anger att meningen ”sentiment” etikett.

````
POST : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>/RetrieveDefaultDefinition?api-version=<apiVersion>
````

Exempel frågans brödtext:  

````
    {
        "bindingType": "Microsoft.MachineLearning/WebService",
        "bindingRetrievalProperties": {
            "executeEndpoint": null,
            "udfType": "Scalar"
        }
    }
````

Ett exempel på utdata för detta skulle titta liknande nedan.  

````
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
````

## <a name="patch-udf-with-the-response"></a>Korrigering UDF med svaret
Nu måste korrigeras UDF-filen med föregående svar, enligt nedan.

````
PATCH : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
````

Begärandetexten (utdata från RetrieveDefaultDefinition):

````
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
````

## <a name="implement-stream-analytics-transformation-to-call-the-udf"></a>Implementera Stream Analytics-transformering för att anropa en användardefinierad funktion
Nu frågar UDF (kallas här scoreTweet) för varje händelse och skriva ett svar för händelsen till utdata.  

````
    {
        "name": "transformation",
        "properties": {
            "streamingUnits": null,
            "query": "select *,scoreTweet(Tweet) TweetSentiment into blobOutput from blobInput"
        }
    }
````


## <a name="get-help"></a>Få hjälp
Om du behöver mer hjälp kan du besöka vårt [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
