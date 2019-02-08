---
title: Authentication
titleSuffix: Cognitive Services - Azure
description: 'Det finns tre sätt att autentisera en begäran till en Azure Cognitive Services-resurs: en prenumerationsnyckel, en ägartoken eller en prenumeration för flera tjänster. I den här artikeln får du lära dig om varje metod och hur du gör en begäran.'
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 01/14/2019
ms.author: erhopf
ms.openlocfilehash: f724bba5acdda20d31d067b850634178a0650cf7
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55859754"
---
# <a name="authenticate-requests-to-azure-cognitive-services"></a>Autentisera begäranden till Azure Cognitive Services

Varje begäran till en Azure Cognitive Service måste innehålla en rubrik för autentisering. Den här rubriken skickar med en prenumerationsnyckel eller åtkomst-token som används för att verifiera din prenumeration för en tjänst eller en grupp med tjänster. I den här artikeln lär du om tre sätt att autentisera en begäran och kraven för var och en.

* [Autentisera med en prenumerationsnyckel som en tjänst](#authenticate-with-a-single-service-subscription-key)
* [Autentisera med en flera tjänster prenumerationsnyckel](#authenticate-with-a-multi-service-subscription-key)
* [Autentisera med en token](#authenticate-with-an-authentication-token)

## <a name="prerequisites"></a>Förutsättningar

Innan du gör en begäran om behöver du ett Azure-konto och en Azure Cognitive Services-prenumeration. Om du redan har ett konto kan du gå vidare och gå vidare till nästa avsnitt. Om du inte har ett konto, har vi en guide som hjälper dig att konfigurera på några minuter: [Skapa ett Cognitive Services-konto för Azure](cognitive-services-apis-create-account.md).

## <a name="authentication-headers"></a>Autentiseringshuvuden

Nu ska vi se snabbt autentiseringshuvuden som är tillgängliga för användning med Azure Cognitive Services.

| Huvud | Beskrivning |
|--------|-------------|
| OCP-Apim-Subscription-Key | Använd den här rubriken om du vill autentisera med en prenumerationsnyckel för en specifik tjänst eller en prenumerationsnyckel som flera tjänster. |
| Ocp-Apim-Subscription-Region | Den här rubriken är endast krävs när du använder en prenumerationsnyckel som flera tjänster med den [Translator Text API](./Translator/reference/v3-0-reference.md). Använd den här rubriken anger prenumeration region. |
| Auktorisering | Använd den här rubriken om du använder en autentiseringstoken. I följande avsnitt beskrivs stegen för att utföra en tokenutbytet. Det angivna värdet följer det här formatet: `Bearer <TOKEN>`. |

## <a name="authenticate-with-a-single-service-subscription-key"></a>Autentisera med en prenumerationsnyckel som en tjänst

Det första alternativet är att autentisera en begäran med en prenumerationsnyckel för en viss tjänst, till exempel textöversättning. Nycklarna är tillgängliga i Azure-portalen för varje resurs som du har skapat. För att använda en prenumeration för att autentisera en begäran, den måste skickas vidare som de `Ocp-Apim-Subscription-Key` rubrik.

Begärandena exemplet visar hur du använder den `Ocp-Apim-Subscription-Key` rubrik. Kom ihåg när du använder det här exemplet måste du innehåller en giltig prenumeration-nyckel.

Det här är ett exempel anrop till Bing Web Search API:
```cURL
curl -X GET 'https://api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

Det här är ett exempel anrop till Translator Text API:
```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

Följande videoklipp visar hur du använder en Cognitive Services-nyckel. 

## <a name="authenticate-with-a-multi-service-subscription-key"></a>Autentisera med en flera tjänster prenumerationsnyckel

>[!WARNING]
> De här tjänsterna för närvarande **inte** stöd för flera tjänster nycklar: QnA Maker Taltjänster och Custom Vision.

Det här alternativet använder också en prenumeration för att autentisera begäranden. Den största skillnaden är att en prenumerationsnyckel inte är kopplat till en specifik tjänst, i stället en enda nyckel kan användas för att autentisera begäranden för flera Cognitive Services. Se [priser för Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/) för information om regional tillgänglighet, funktioner som stöds och priser.

Prenumerationsnyckeln har angetts i varje begäran som den `Ocp-Apim-Subscription-Key` rubrik.

[![Flera tjänster prenumeration viktiga demonstration för Cognitive Services](./media/index/single-key-demonstration-video.png)](https://www.youtube.com/watch?v=psHtA1p7Cas&feature=youtu.be)

### <a name="supported-regions"></a>Regioner som stöds

När du använder flera tjänster prenumerationsnyckeln för att begära att `api.cognitive.microsoft.com`, måste du inkludera regionen i URL: en. Till exempel: `westus.api.cognitive.microsoft.com`.

När du använder flera tjänster prenumerationsnyckel med Translator Text API kan du ange prenumeration region med de `Ocp-Apim-Subscription-Region` rubrik.

Flera tjänster autentisering stöds i dessa regioner:

| | | |
|-|-|-|
| `australiaeast` | `brazilsouth` | `canadacentral` |
| `centralindia` | `eastasia` | `eastus` |
| `japaneast` | `northeurope` | `southcentralus` |
| `southeastasia` | `uksouth` | `westcentralus` |
| `westeurope` | `westus` | `westus2` |


### <a name="sample-requests"></a>Exempelförfrågan

Det här är ett exempel anrop till Bing Web Search API:

```cURL
curl -X GET 'https://YOUR-REGION.api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

Det här är ett exempel anrop till Translator Text API:

```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Ocp-Apim-Subscription-Region: YOUR_SUBSCRIPTION_REGION' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

## <a name="authenticate-with-an-authentication-token"></a>Autentisera med en autentiseringstoken

Vissa Azure Cognitive Services Godkänn och i vissa fall kräver, en autentiseringstoken. För närvarande stöder de här tjänsterna autentiseringstoken:

* API för textöversättning
* Taltjänster: Tal till text REST-API
* Taltjänster: Text till tal REST-API

>[!NOTE]
> QnA Maker kan du även använder auktoriseringsrubriken, men kräver en slutpunktsnyckeln. Mer information finns i [QnA Maker: Få svar från knowledge base](./qnamaker/quickstarts/get-answer-from-kb-using-curl.md).

>[!WARNING]
> Tjänster som stöder autentiseringstoken kan ändras över tid,. Kontrollera API-referens för en tjänst innan du använder den här autentiseringsmetoden.

Både enkel service och flera tjänster prenumerationsnycklar kan utbyta för autentiseringstoken. Autentiseringstoken är giltiga i 10 minuter.

Autentiseringstoken som ingår i en begäran som den `Authorization` rubrik. Token värdet som angetts måste föregås av `Bearer`, till exempel: `Bearer YOUR_AUTH_TOKEN`.

### <a name="sample-requests"></a>Exempelförfrågan

Använda den här URL: en för att skicka en prenumerationsnyckel för en tjänst för en autentiseringstoken: `https://api.cognitive.microsoft.com/sts/v1.0/issueToken`.

```cURL
curl -v -X POST \
"https://api.cognitive.microsoft.com/sts/v1.0/issueToken" \
-H "Content-type: application/x-www-form-urlencoded" \
-H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

När du använder flera tjänster prenumerationsnyckel, måste du använda en viss region-slutpunkt för token exchange. Använda den här URL: en för att skicka en flera tjänster prenumerationsnyckel för en autentiseringstoken: `https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken`.

Dessa flera tjänster regioner har stöd för token exchange:

| | | |
|-|-|-|
| `australiaeast` | `brazilsouth` | `canadacentral` |
| `centralindia` | `eastasia` | `eastus` |
| `japaneast` | `northeurope` | `southcentralus` |
| `southeastasia` | `uksouth` | `westcentralus` |
| `westeurope` | `westus` | `westus2` |

```cURL
curl -v -X POST \
"https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
-H "Content-type: application/x-www-form-urlencoded" \
-H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

När du får en autentiseringstoken, måste du skicka den i varje begäran som den `Authorization` rubrik. Det här är ett exempel anrop till Translator Text API:

```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Authorization: Bearer YOUR_AUTH_TOKEN' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

## <a name="see-also"></a>Se också

* [Vad är Cognitive Services?](welcome.md)
* [Priser för cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/)
* [Skapa ett konto](cognitive-services-apis-create-account.md)
