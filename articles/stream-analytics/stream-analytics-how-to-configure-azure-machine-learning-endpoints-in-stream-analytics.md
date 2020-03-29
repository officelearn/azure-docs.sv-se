---
title: Använda Machine Learning-slutpunkter i Azure Stream Analytics
description: I den här artikeln beskrivs hur du använder användardefinierade funktioner för maskinspråk i Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 239955025f21d8679cbcf0bbfe68f9070f0217c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426188"
---
# <a name="azure-machine-learning-studio-classic-integration-in-stream-analytics-preview"></a>Azure Machine Learning Studio (klassisk) integration i Stream Analytics (Preview)
Stream Analytics stöder användardefinierade funktioner som ropar till Azure Machine Learning Studio (klassiska) slutpunkter. REST API-stöd för den här funktionen beskrivs i [Stream Analytics REST API-bibliotek](https://msdn.microsoft.com/library/azure/dn835031.aspx). Den här artikeln innehåller ytterligare information som behövs för att den här funktionen ska kunna implementeras i Stream Analytics. En handledning har också lagts upp och finns [här](stream-analytics-machine-learning-integration-tutorial.md).

## <a name="overview-azure-machine-learning-studio-classic-terminology"></a>Översikt: Azure Machine Learning Studio (klassisk) terminologi
Microsoft Azure Machine Learning Studio (klassisk) tillhandahåller ett samarbetsverktyg för att dra och släppa som du kan använda för att skapa, testa och distribuera lösningar för prediktiva analyser på dina data. Det här verktyget kallas *Azure Machine Learning Studio (klassisk)*. Studion används för att interagera med Machine Learning-resurserna och enkelt bygga, testa och iterera på din design. Dessa resurser och deras definitioner finns nedan.

* **Arbetsyta:** *Arbetsytan* är en behållare som innehåller alla andra Machine Learning-resurser tillsammans i en behållare för hantering och kontroll.
* **Experiment:** *Experiment skapas* av datavetare för att använda datauppsättningar och träna en maskininlärningsmodell.
* **Slutpunkt:** *Slutpunkter* är Azure Machine Learning Studio (klassiskt) objekt som används för att ta funktioner som indata, tillämpa en angiven maskininlärningsmodell och returnera poängsatt utdata.
* **Bedömning Webservice**: En *bedömning webtjänst* är en samling slutpunkter som nämnts ovan.

Varje slutpunkt har api:er för körning av batch och synkron körning. Stream Analytics använder synkron körning. Den specifika tjänsten heter en [begäran/svarstjänst](../machine-learning/studio/consume-web-services.md) i Azure Machine Learning Studio (klassisk).

## <a name="machine-learning-resources-needed-for-stream-analytics-jobs"></a>Machine Learning-resurser som behövs för Stream Analytics-jobb
För Dataanalys jobbbearbetning är en slutpunkt för begäran/svar, en [apikey](../machine-learning/machine-learning-connect-to-azure-machine-learning-web-service.md)och en swagger-definition alla nödvändiga för att körningen ska kunna köras. Stream Analytics har ytterligare en slutpunkt som konstruerar url:en för swagger-slutpunkten, slår upp gränssnittet och returnerar en standard-UDF-definition till användaren.

## <a name="configure-a-stream-analytics-and-machine-learning-udf-via-rest-api"></a>Konfigurera en Stream Analytics och Machine Learning UDF via REST API
Genom att använda REST API:er kan du konfigurera jobbet så att det anropar Azure Machine Language-funktioner. Stegen är följande:

1. Skapa ett Stream Analytics-jobb
2. Definiera en indata
3. Definiera en utdata
4. Skapa en användardefinierad funktion (UDF)
5. Skriv en Stream Analytics-omvandling som anropar UDF
6. Starta jobbet

## <a name="creating-a-udf-with-basic-properties"></a>Skapa en UDF med grundläggande egenskaper
Som ett exempel skapar följande exempelkod en scalar UDF med namnet *newudf* som binder till en Azure Machine Learning Studio (klassisk) slutpunkt. Observera att *slutpunkten* (tjänst URI) finns på API-hjälpsidan för den valda tjänsten och *apiKey* finns på huvudsidan för Tjänster.

```
    PUT : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
```

Exempel begäran organ:

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

## <a name="call-retrievedefaultdefinition-endpoint-for-default-udf"></a>Anropa Endpoint för Call RetrieveDefaultDefinition för uDF som standard
När skelettet UDF skapas behövs den fullständiga definitionen av UDF. Endpointen RetrieveDefaultDefinition hjälper dig att få standarddefinitionen för en skalfunktion som är bunden till en Azure Machine Learning Studio -slutpunkt (klassisk). Nyttolasten nedan kräver att du får standard UDF-definitionen för en skalärfunktion som är bunden till en Azure Machine Learning-slutpunkt. Den anger inte den faktiska slutpunkten eftersom den redan har angetts under PUT-begäran. Stream Analytics anropar slutpunkten som anges i begäran om den uttryckligen anges. Annars använder den den som ursprungligen refererades. Här tar UDF en enda strängparameter (en mening) och returnerar en enda utdata av typen sträng som anger etiketten "sentiment" för den meningen.

```
POST : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>/RetrieveDefaultDefinition?api-version=<apiVersion>
```

Exempel begäran organ:

```json
    {
        "bindingType": "Microsoft.MachineLearning/WebService",
        "bindingRetrievalProperties": {
            "executeEndpoint": null,
            "udfType": "Scalar"
        }
    }
```

Ett exempel på detta skulle se ut ungefär som nedan.

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

## <a name="patch-udf-with-the-response"></a>Patch UDF med svaret
Nu UDF måste lappas med föregående svar, som visas nedan.

```
PATCH : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
```

Begärandetext (Utdata från RetrieveDefaultDefinition):

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

## <a name="implement-stream-analytics-transformation-to-call-the-udf"></a>Implementera Stream Analytics-omvandling för att anropa UDF
Fråga nu UDF (här heter scoreTweet) för varje indatahändelse och skriv ett svar för den händelsen till en utdata.

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
* [Referens för Azure Stream Analytics-frågespråket](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referens för Azure Stream Analytics Management REST-API:et](https://msdn.microsoft.com/library/azure/dn835031.aspx)
