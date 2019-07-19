---
title: Authentication
titleSuffix: Azure Cognitive Services
description: 'Det finns tre sätt att autentisera en begäran till en Azure Cognitive Services-resurs: en prenumerations nyckel, en Bearer-token eller en prenumeration på flera tjänster. I den här artikeln får du lära dig om varje metod och hur du skapar en begäran.'
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: erhopf
ms.openlocfilehash: 0499b2ef25cc93615a72269bd64af689ebced01d
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68333595"
---
# <a name="authenticate-requests-to-azure-cognitive-services"></a>Autentisera begär anden till Azure Cognitive Services

Varje begäran till en Azure-kognitiv tjänst måste innehålla ett Authentication-huvud. Den här rubriken skickas längs en prenumerations nyckel eller åtkomsttoken som används för att verifiera prenumerationen för en tjänst eller grupp av tjänster. I den här artikeln får du lära dig mer om tre sätt att autentisera en begäran och kraven för var och en.

* [Autentisera med en prenumerations nyckel för en enskild tjänst](#authenticate-with-a-single-service-subscription-key)
* [Autentisera med en prenumerations nyckel för flera tjänster](#authenticate-with-a-multi-service-subscription-key)
* [Autentisera med en token](#authenticate-with-an-authentication-token)

## <a name="prerequisites"></a>Förutsättningar

Innan du gör en förfrågan behöver du ett Azure-konto och en Azure Cognitive Services-prenumeration. Om du redan har ett konto kan du gå vidare och gå vidare till nästa avsnitt. Om du inte har ett konto har vi en guide som hjälper dig att konfigurera på några minuter: [Skapa ett Cognitive Services-konto för Azure](cognitive-services-apis-create-account.md).

Du kan hämta prenumerations nyckeln från [Azure Portal](cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) när du har skapat ditt konto eller aktivera en [kostnads fri utvärderings version](https://azure.microsoft.com/try/cognitive-services/my-apis).

## <a name="authentication-headers"></a>Autentiseringsscheman

Vi går snabbt igenom de autentiseringsscheman som är tillgängliga för användning med Azure Cognitive Services.

| Huvud | Beskrivning |
|--------|-------------|
| OCP-Apim-Subscription-Key | Använd den här rubriken för att autentisera med en prenumerations nyckel för en enskild tjänst eller en prenumerations nyckel för flera tjänster. |
| Ocp-Apim-Subscription-Region | Den här rubriken krävs bara när du använder en prenumerations nyckel för flera tjänster med [Translator text API](./Translator/reference/v3-0-reference.md). Använd den här rubriken för att ange prenumerations region. |
| Authorization | Använd det här huvudet om du använder en autentiseringstoken. Stegen för att utföra ett token-utbyte beskrivs i följande avsnitt. Det tillhandahållna värdet följer det här `Bearer <TOKEN>`formatet:. |

## <a name="authenticate-with-a-single-service-subscription-key"></a>Autentisera med en prenumerations nyckel för en enskild tjänst

Det första alternativet är att autentisera en begäran med en prenumerations nyckel för en speciell tjänst, t. ex. Translator Text. Nycklarna är tillgängliga i Azure Portal för varje resurs som du har skapat. Om du vill använda en prenumerations nyckel för att autentisera en begäran måste den skickas tillsammans `Ocp-Apim-Subscription-Key` med rubriken.

Dessa exempel förfrågningar visar hur du använder `Ocp-Apim-Subscription-Key` sidhuvudet. Tänk på att när du använder det här exemplet måste du inkludera en giltig prenumerations nyckel.

Detta är ett exempel anrop till API för webbsökning i Bing:
```cURL
curl -X GET 'https://api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

Detta är ett exempel anrop till Translator Text API:
```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

Följande videoklipp visar hur du använder en Cognitive Services nyckel.

## <a name="authenticate-with-a-multi-service-subscription-key"></a>Autentisera med en prenumerations nyckel för flera tjänster

>[!WARNING]
> För tillfället stöder dessa tjänster **inte** nycklar med flera tjänster: QnA Maker, tal tjänster och Custom Vision.

Det här alternativet använder också en prenumerations nyckel för att autentisera begär Anden. Den största skillnaden är att en prenumerations nyckel inte är kopplad till en viss tjänst, i stället kan du använda en enskild nyckel för att autentisera begär Anden för flera Cognitive Services. Se [Cognitive Services priser](https://azure.microsoft.com/pricing/details/cognitive-services/) för information om regional tillgänglighet, funktioner som stöds och priser.

Prenumerations nyckeln anges i varje begäran som `Ocp-Apim-Subscription-Key` rubrik.

[![Demonstrations nyckel för flera tjänste prenumerationer för Cognitive Services](./media/index/single-key-demonstration-video.png)](https://www.youtube.com/watch?v=psHtA1p7Cas&feature=youtu.be)

### <a name="supported-regions"></a>Regioner som stöds

När du använder prenumerations nyckeln för flera tjänster för att göra en `api.cognitive.microsoft.com`begäran till måste du inkludera regionen i URL: en. Till exempel: `westus.api.cognitive.microsoft.com`.

När du använder en prenumerations nyckel för flera tjänster med Translator text API måste du ange prenumerations region med `Ocp-Apim-Subscription-Region` rubriken.

Autentisering med flera tjänster stöds i följande regioner:

| | | |
|-|-|-|
| `australiaeast` | `brazilsouth` | `canadacentral` |
| `centralindia` | `eastasia` | `eastus` |
| `japaneast` | `northeurope` | `southcentralus` |
| `southeastasia` | `uksouth` | `westcentralus` |
| `westeurope` | `westus` | `westus2` |


### <a name="sample-requests"></a>Exempel förfrågningar

Detta är ett exempel anrop till API för webbsökning i Bing:

```cURL
curl -X GET 'https://YOUR-REGION.api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

Detta är ett exempel anrop till Translator Text API:

```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Ocp-Apim-Subscription-Region: YOUR_SUBSCRIPTION_REGION' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

## <a name="authenticate-with-an-authentication-token"></a>Autentisera med en autentiseringstoken

Vissa Azure-Cognitive Services accepterar, och i vissa fall kräver detta en autentiseringstoken. Dessa tjänster har för närvarande stöd för autentiseringstoken:

* API för text översättning
* Tal tjänster: Tal till text-REST API
* Tal tjänster: Text till tal-REST API

>[!NOTE]
> QnA Maker använder också Authorization-huvudet, men kräver en slut punkts nyckel. Mer information finns i [QNA Maker: Få svar från kunskaps](./qnamaker/quickstarts/get-answer-from-kb-using-curl.md)basen.

>[!WARNING]
> De tjänster som stöder autentiseringstoken kan ändras med tiden, kontrol lera API-referensen för en tjänst innan du använder den här autentiseringsmetoden.

Både prenumerations nycklar för enskilda tjänster och flera tjänster kan bytas ut mot autentiseringstoken. Autentiseringstoken är giltiga i 10 minuter.

Autentiseringstoken ingår i en begäran som `Authorization` rubrik. Det angivna värdet för token måste föregås `Bearer`av, till exempel `Bearer YOUR_AUTH_TOKEN`:.

### <a name="sample-requests"></a>Exempel förfrågningar

Använd den här URL: en för att byta ut en prenumerations `https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken`nyckel för en autentiseringstoken:.

```cURL
curl -v -X POST \
"https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
-H "Content-type: application/x-www-form-urlencoded" \
-H "Content-length: 0" \
-H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

Dessa multi-service-regioner stöder token Exchange:

| | | |
|-|-|-|
| `australiaeast` | `brazilsouth` | `canadacentral` |
| `centralindia` | `eastasia` | `eastus` |
| `japaneast` | `northeurope` | `southcentralus` |
| `southeastasia` | `uksouth` | `westcentralus` |
| `westeurope` | `westus` | `westus2` |

När du har skaffat en autentiseringstoken måste du skicka den i varje begäran som `Authorization` rubrik. Detta är ett exempel anrop till Translator Text API:

```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Authorization: Bearer YOUR_AUTH_TOKEN' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

## <a name="see-also"></a>Se också

* [Vad är Cognitive Services?](welcome.md)
* [Prissättning för Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/)
* [Skapa ett konto](cognitive-services-apis-create-account.md)
