---
title: Microsoft Translator Text API V3.0 referens | Microsoft Docs
description: I referensdokumentationen för V3.0 Microsoft översättare Text-API.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: cfaa9584e833b137b417d9074fbfcf606eb21388
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352701"
---
#<a name="translator-text-api-v30"></a>Översättare Text API v3.0

## <a name="whats-new"></a>Nyheter

Version 3 av Microsoft översättare Text API ger en modern JSON-baserade webb-API. Det förbättrar användbarhet och prestanda genom att konsolidera befintliga funktioner i färre åtgärder och innehåller nya funktioner.

 * Transliteration att konvertera text på ett språk från ett skript till ett annat skript.
 * Översättning till flera språk i en begäran.
 * Identifiera språk, översättning och transliteration i en begäran.
 * Ordlista till sökning alternativa översättningar löper, att hitta tillbaka översättningar och exempel som visar termer som används i kontexten.
 * Mer informativa Identifieringsresultat för språk.

## <a name="base-urls"></a>Bas-URL: er

Text API v3.0 finns i följande molnet:

| Beskrivning | Region | Grundläggande URL                                        |
|-------------|--------|-------------------------------------------------|
| Azure       | Global | API.cognitive.microsofttranslator.com           |


## <a name="authentication"></a>Autentisering

Prenumerera på översättare Text API i kognitiva Microsoft-tjänster och använda din prenumeration nyckel (tillgängligt i Azure-portalen) för att autentisera. 

Det enklaste sättet är att skicka din Azure hemlig nyckel till tjänsten översättare med hjälp av begärandehuvudet `Ocp-Apim-Subscription-Key`.

Ett alternativ är att använda din hemliga nyckeln för att hämta en autentiseringstoken från token-tjänsten. Sedan kan du skickar autentiseringstoken till översättare tjänsten använder den `Authorization` huvudet i begäran. För att få en autentiseringstoken kan göra en `POST` begäran till följande URL:

| Miljö     | URL för autentisering                                |
|-----------------|-----------------------------------------------------------|
| Azure           | `https://api.cognitive.microsoft.com/sts/v1.0/issueToken` |

Här följer exempel begäranden att hämta en token som ges en hemlig nyckel:

```
// Pass secret key using header
curl --header 'Ocp-Apim-Subscription-Key: <your-key>' --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken'
// Pass secret key using query string parameter
curl --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken?Subscription-Key=<your-key>'
```

En lyckad begäran returnerar den kodade åtkomst-token som oformaterad text i brödtext för svar. Ogiltig token har överförts till tjänsten översättare som en ägartoken i tillståndet.

```
Authorization: Bearer <Base64-access_token>
```

En token för autentisering är giltig i 10 minuter. Token som ska återanvändas när du anropar API: er för översättare flera. Men om ditt program gör begäranden till översättare API under en längre tidsperiod, måste sedan programmet begära en ny åtkomsttoken med regelbundna intervall (t.ex. var 8: e minut).

För att sammanfatta agerar innehåller en klientbegäran till översättare API ett authorization-huvud som vidtagits i följande tabell:

<table width="100%">
  <th width="30%">Sidhuvuden</th>
  <th>Beskrivning</th>
  <tr>
    <td>OCP-Apim-prenumeration-nyckel</td>
    <td>*Om du skickar din hemliga nyckeln använda med kognitiva abonnemang*.<br/>Värdet är den hemliga nyckeln för din prenumeration översättare Text-API: et för Azure.</td>
  </tr>
  <tr>
    <td>Auktorisering</td>
    <td>*Använd med kognitiva Services prenumeration om du skickar en autentiseringstoken.*<br/>Värdet är ägar-token: ' ägar <token>'.</td>
  </tr>
</table> 

## <a name="errors"></a>Fel

Ett felsvar som standard är en JSON-objekt med namn/värde-par med namnet `error`. Värdet är också ett JSON-objekt med egenskaper:

  * `code`: En server-definierade felkod.

  * `message`: En sträng som ger en läsbar representation av felet.

En kund med en kostnadsfri utvärderingsprenumeration skulle visas följande felmeddelande när det lediga är uttömd:

```
{
  "error": {
    "code":403000,
    "message":"The subscription has exceeded its free quota."
    }
}
```
