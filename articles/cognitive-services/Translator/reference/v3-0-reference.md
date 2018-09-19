---
title: Referens för Translator Text API-version 3.0
titlesuffix: Azure Cognitive Services
description: Referensdokumentation för Translator Text API V3.0.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: 9282d8af30cbfb3346394bcd71510faf8d8c8a21
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "46129394"
---
# <a name="translator-text-api-v30"></a>Translator Text API v3.0

## <a name="whats-new"></a>Nyheter

Version 3 av Translator Text API innehåller ett moderna JSON-baserade webb-API. Det förbättrar användbarhet och prestanda genom att konsolidera befintliga funktioner i färre åtgärder och innehåller nya funktioner.

 * Transkriberingsspråk att konvertera text på ett språk från ett skript till ett annat skript.
 * Översättning för flera språk i en begäran.
 * Språkidentifiering, översättning och transkriberingsspråk i en begäran.
 * Ordlista till alternativa översättningar för sökning av en period, att hitta tillbaka översättningar och exempel som visar termer som används i kontexten.
 * Mer informativt resultat för språk-identifiering.

## <a name="base-urls"></a>Grundläggande URL: er

Text-API version 3.0 finns i följande molnet:

| Beskrivning | Region | Grundläggande URL                                        |
|-------------|--------|-------------------------------------------------|
| Azure       | Global | API.cognitive.microsofttranslator.com           |


## <a name="authentication"></a>Autentisering

Prenumererar på Translator Text API i Microsoft Cognitive Services och använder din prenumerationsnyckel (tillgängligt i Azure-portalen) för att autentisera. 

Det enklaste sättet är att skicka din Azure hemlig nyckel till Translator-tjänsten med begärandehuvudet `Ocp-Apim-Subscription-Key`.

Ett alternativ är att använda din hemliga nyckeln för att hämta en autentiseringstoken från token-tjänsten. Sedan kan du skicka autentiseringstoken till Translator-tjänsten med den `Authorization` huvudet i begäran. Om du vill ha en autentiseringstoken, göra en `POST` begäran till följande URL:

| Miljö     | Tjänst-URL för autentisering                                |
|-----------------|-----------------------------------------------------------|
| Azure           | `https://api.cognitive.microsoft.com/sts/v1.0/issueToken` |

Här följer exempel begäranden att hämta en token som en hemlig nyckel:

```
// Pass secret key using header
curl --header 'Ocp-Apim-Subscription-Key: <your-key>' --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken'
// Pass secret key using query string parameter
curl --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken?Subscription-Key=<your-key>'
```

En lyckad begäran returnerar den kodade åtkomst-token som oformaterad text i svarstexten. Som en ägartoken i tillståndet skickades till tjänsten Translator är en giltig token.

```
Authorization: Bearer <Base64-access_token>
```

En autentiseringstoken är giltig i 10 minuter. Token ska återanvändas när flera anrop till Translator-API: er. Men om ditt program gör begäranden till Translator API under en längre tid, måste sedan programmet begära en ny åtkomsttoken med jämna mellanrum (t.ex. var 8: e minut).

Sammanfattningsvis innehåller en klientbegäran Translator-API: et en auktoriseringsrubrik som kommer från följande tabell:

<table width="100%">
  <th width="30%">Rubriker</th>
  <th>Beskrivning</th>
  <tr>
    <td>OCP-Apim-Subscription-Key</td>
    <td>*Använd med Cognitive Services-prenumeration om du skickar din hemliga nyckel*.<br/>Värdet är Azure hemlig nyckel för din prenumeration till Translator Text API.</td>
  </tr>
  <tr>
    <td>Auktorisering</td>
    <td>*Använd med Cognitive Services-prenumeration om du skickar en autentiseringstoken.*<br/>Värdet är ägartoken: ' ägar <token>'.</td>
  </tr>
</table> 

## <a name="errors"></a>Fel

Ett felsvar som standard är ett JSON-objekt med namn/värde-par med namnet `error`. Värdet är också ett JSON-objekt med egenskaper:

  * `code`: En server-definierade felkod.

  * `message`: En sträng som ger en läsbara representation av felet.

Exempelvis skulle en kund med en kostnadsfri utvärderingsprenumeration får följande fel när den lediga kvoten är slut:

```
{
  "error": {
    "code":403000,
    "message":"The subscription has exceeded its free quota."
    }
}
```
