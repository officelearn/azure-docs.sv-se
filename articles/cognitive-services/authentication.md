---
title: Autentisering
titleSuffix: Azure Cognitive Services
description: 'Det finns tre sätt att autentisera en begäran till en Azure Cognitive Services-resurs: en prenumerationsnyckel, en bäraretoken eller en prenumeration med flera tjänster. I den här artikeln får du lära dig mer om varje metod och hur du gör en begäran.'
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 11/22/2019
ms.author: erhopf
ms.openlocfilehash: 1c13c2cc4d4e562d3512de90338d874091dfeef6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74423938"
---
# <a name="authenticate-requests-to-azure-cognitive-services"></a>Autentisera begäranden till Azure Cognitive Services

Varje begäran till en Azure Cognitive Service måste innehålla ett autentiseringshuvud. Det här huvudet skickas längs en prenumerationsnyckel eller åtkomsttoken, som används för att verifiera din prenumeration för en tjänst eller grupp av tjänster. I den här artikeln får du lära dig mer om tre sätt att autentisera en begäran och kraven för varje.

* [Autentisera med en prenumerationsnyckel för en tjänst](#authenticate-with-a-single-service-subscription-key)
* [Autentisera med en prenumerationsnyckel för flera tjänster](#authenticate-with-a-multi-service-subscription-key)
* [Autentisera med en token](#authenticate-with-an-authentication-token)
* [Autentisera med Azure Active Directory (AAD)](#authenticate-with-azure-active-directory)

## <a name="prerequisites"></a>Krav

Innan du gör en begäran behöver du ett Azure-konto och en Azure Cognitive Services-prenumeration. Om du redan har ett konto går du vidare och går vidare till nästa avsnitt. Om du inte har ett konto har vi en guide som hjälper dig att konfigurera på några minuter: [Skapa ett Cognitive Services-konto för Azure](cognitive-services-apis-create-account.md).

Du kan hämta din prenumerationsnyckel från [Azure-portalen](cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) när du har skapat ditt konto eller aktiverat en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/try/cognitive-services/my-apis).

## <a name="authentication-headers"></a>Autentiseringshuvuden

Nu ska vi snabbt granska de autentiseringshuvuden som är tillgängliga för användning med Azure Cognitive Services.

| Huvud | Beskrivning |
|--------|-------------|
| Ocp-Apim-Subscription-Key | Använd det här huvudet för att autentisera med en prenumerationsnyckel för en viss tjänst eller en prenumerationsnyckel för flera tjänster. |
| Ocp-Apim-Prenumeration-region | Det här huvudet krävs bara när du använder en prenumerationsnyckel för flera tjänster med [Translator Text API](./Translator/reference/v3-0-reference.md). Använd det här huvudet för att ange prenumerationsregionen. |
| Auktorisering | Använd det här huvudet om du använder en autentiseringstoken. Stegen för att utföra ett tokenutbyte beskrivs i följande avsnitt. Värdet som anges följer `Bearer <TOKEN>`det här formatet: . |

## <a name="authenticate-with-a-single-service-subscription-key"></a>Autentisera med en prenumerationsnyckel för en tjänst

Det första alternativet är att autentisera en begäran med en prenumerationsnyckel för en viss tjänst, till exempel Översättare text. Nycklarna är tillgängliga i Azure-portalen för varje resurs som du har skapat. Om du vill använda en prenumerationsnyckel för att `Ocp-Apim-Subscription-Key` autentisera en begäran måste den skickas vidare som huvud.

Dessa exempelbegäranden visar hur `Ocp-Apim-Subscription-Key` du använder huvudet. Tänk på att när du använder det här exemplet måste du inkludera en giltig prenumerationsnyckel.

Det här är ett exempelrop till API:et för webbsökning på Bing:
```cURL
curl -X GET 'https://api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

Det här är ett exempelrop till Translator Text API:
```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

Följande video visas med hjälp av en Cognitive Services-nyckel.

## <a name="authenticate-with-a-multi-service-subscription-key"></a>Autentisera med en prenumerationsnyckel för flera tjänster

>[!WARNING]
> För närvarande stöder dessa tjänster **inte** flertjänstnycklar: QnA Maker, Taltjänster, Anpassad syn och avvikelsedetektor.

Det här alternativet använder också en prenumerationsnyckel för att autentisera begäranden. Den största skillnaden är att en prenumerationsnyckel inte är knuten till en viss tjänst, utan att en enda nyckel kan användas för att autentisera begäranden för flera Cognitive Services. Se [Priser för Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/) för information om regional tillgänglighet, funktioner som stöds och priser.

Prenumerationsnyckeln anges i varje `Ocp-Apim-Subscription-Key` begäran som huvud.

[![Demonstration av prenumerationsnyckel för flera tjänster för Cognitive Services](./media/index/single-key-demonstration-video.png)](https://www.youtube.com/watch?v=psHtA1p7Cas&feature=youtu.be)

### <a name="supported-regions"></a>Regioner som stöds

När du använder prenumerationsnyckeln för `api.cognitive.microsoft.com`flera tjänster för att göra en begäran till måste du inkludera regionen i URL:en. Till exempel: `westus.api.cognitive.microsoft.com`.

När du använder prenumerationsnyckel för flera tjänster med Translator Text `Ocp-Apim-Subscription-Region` API måste du ange prenumerationsregionen med huvudet.

Multi-service autentisering stöds i dessa regioner:

| | | |
|-|-|-|
| `australiaeast` | `brazilsouth` | `canadacentral` |
| `centralindia` | `eastasia` | `eastus` |
| `japaneast` | `northeurope` | `southcentralus` |
| `southeastasia` | `uksouth` | `westcentralus` |
| `westeurope` | `westus` | `westus2` |


### <a name="sample-requests"></a>Exempel på begäranden

Det här är ett exempelrop till API:et för webbsökning på Bing:

```cURL
curl -X GET 'https://YOUR-REGION.api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

Det här är ett exempelrop till Translator Text API:

```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Ocp-Apim-Subscription-Region: YOUR_SUBSCRIPTION_REGION' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

## <a name="authenticate-with-an-authentication-token"></a>Autentisera med en autentiseringstoken

Vissa Azure Cognitive Services accepterar, och kräver i vissa fall, en autentiseringstoken. För närvarande stöder dessa tjänster autentiseringstoken:

* API för textöversättning
* Taltjänster: REST-API för tal till text
* Taltjänster: REST-API för text till tal

>[!NOTE]
> QnA Maker använder också auktoriseringshuvudet, men kräver en slutpunktsnyckel. Mer information finns i [QnA Maker: Få svar från kunskapsbasen](./qnamaker/quickstarts/get-answer-from-knowledge-base-using-url-tool.md).

>[!WARNING]
> Tjänsterna som stöder autentiseringstoken kan ändras med tiden, kontrollera API-referensen för en tjänst innan du använder den här autentiseringsmetoden.

Både entjänst- och prenumerationsnycklar med flera tjänster kan bytas ut mot autentiseringstoken. Autentiseringstoken är giltiga i 10 minuter.

Autentiseringstoken ingår i `Authorization` en begäran som huvud. Tokenvärdet måste föregås av `Bearer`till exempel: `Bearer YOUR_AUTH_TOKEN`.

### <a name="sample-requests"></a>Exempel på begäranden

Använd den här URL:en för att `https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken`byta ut en prenumerationsnyckel mot en autentiseringstoken: .

```cURL
curl -v -X POST \
"https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
-H "Content-type: application/x-www-form-urlencoded" \
-H "Content-length: 0" \
-H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

Dessa regioner med flera tjänster stöder tokenutbyte:

| | | |
|-|-|-|
| `australiaeast` | `brazilsouth` | `canadacentral` |
| `centralindia` | `eastasia` | `eastus` |
| `japaneast` | `northeurope` | `southcentralus` |
| `southeastasia` | `uksouth` | `westcentralus` |
| `westeurope` | `westus` | `westus2` |

När du har fått en autentiseringstoken måste du `Authorization` skicka den i varje begäran som sidhuvud. Det här är ett exempelrop till Translator Text API:

```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Authorization: Bearer YOUR_AUTH_TOKEN' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

[!INCLUDE [](../../includes/cognitive-services-azure-active-directory-authentication.md)]

## <a name="see-also"></a>Se även

* [Vad är Cognitive Services?](welcome.md)
* [Prissättning för Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/)
* [Anpassade underdomäner](cognitive-services-custom-subdomains.md)
