---
title: Referens för Translator Text API-version 3.0
titlesuffix: Azure Cognitive Services
description: Referensdokumentation för Translator Text API V3.0.
services: cognitive-services
author: v-pawal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: 4a5bed67252c3b87233c8d2e677e3c620adb8a17
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/04/2019
ms.locfileid: "58918815"
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

Microsoft Translator hanteras från flera datacenter-platser. För närvarande de befinner sig i 6 [Azure-regioner](https://azure.microsoft.com/global-infrastructure/regions):

* **Americas:** USA, västra 2 och USA, västra centrala 
* **Asien/Stillahavsområdet:** Asien, sydöstra och Korea, södra
* **Europa:** Norra Europa och västra Europa

Förfrågningar till Microsoft Translator Text API är oftast hanteras av datacentret som är närmast som begäran kom från. Om ett fel uppstår i datacenter, kan begäran skickas utanför regionen.

Om du vill tvinga begäran som ska hanteras av en specifik datacenter, ändrar du Global slutpunkt i API-begäran till den önskade regionala slutpunkten:

|Beskrivning|Region|Grundläggande URL|
|:--|:--|:--|
|Azure|Global|  api.cognitive.microsofttranslator.com|
|Azure|Nordamerika|   api-nam.cognitive.microsofttranslator.com|
|Azure|Europa|  api-eur.cognitive.microsofttranslator.com|
|Azure|Asien och stillahavsområdet|    api-apc.cognitive.microsofttranslator.com|


## <a name="authentication"></a>Authentication

Prenumererar på Translator Text API eller [flera-tjänsten för Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/) i Microsoft Cognitive Services och använda din prenumeration tangent (tillgängligt i Azure-portalen) för att autentisera. 

Det finns tre rubriker som du kan använda för att autentisera din prenumeration. Den här tabellen innehåller beskriver hur var och en används:

|Rubriker|Beskrivning|
|:----|:----|
|OCP-Apim-Subscription-Key|*Använd med Cognitive Services-prenumeration om du skickar din hemliga nyckel*.<br/>Värdet är Azure hemlig nyckel för din prenumeration till Translator Text API.|
|Auktorisering|*Använd med Cognitive Services-prenumeration om du skickar en autentiseringstoken.*<br/>Värdet är ägartoken: `Bearer <token>`.|
|Ocp-Apim-Subscription-Region|*Använd med flera tjänster Cognitive Services-prenumeration om du skickar en flera tjänster hemlig nyckel.*<br/>Värdet är regionen för flera tjänster prenumerationen. Det här värdet är valfritt när du inte använder en prenumeration på flera tjänster.|

###  <a name="secret-key"></a>Hemlig nyckel
Det första alternativet är att autentisera med hjälp av den `Ocp-Apim-Subscription-Key` rubrik. Lägg bara till den `Ocp-Apim-Subscription-Key: <YOUR_SECRET_KEY>` rubrik på din begäran.

### <a name="authorization-token"></a>Autentiseringstoken
Du kan också byta en hemlig nyckel för en åtkomsttoken. Denna token som ingår i varje begäran som den `Authorization` rubrik. Om du vill ha en autentiseringstoken, göra en `POST` begäran till följande URL:

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

### <a name="multi-service-subscription"></a>Flera tjänster prenumeration

Alternativet senaste autentisering är att använda en Cognitive Service flera tjänster prenumeration. På så sätt kan du använda en enda hemlig nyckel för att autentisera begäranden för flera tjänster. 

När du använder en flera tjänster hemlig nyckel, måste du inkludera två autentiseringshuvuden din förfrågan. Först skickar den hemliga nyckeln, andra anger regionen som är associerade med prenumerationen. 
* `Ocp-Apim-Subscription-Key`
* `Ocp-Apim-Subscription-Region`

Region måste anges för flera tjänster Text API-prenumeration. Den region som du väljer är den enda regionen som du kan använda för textöversättning när du använder flera tjänster prenumerationsnyckeln och måste vara i samma region som du valde när du registrerat dig för prenumerationen flera tjänster via Azure portal.

Regioner är tillgängliga `australiaeast`, `brazilsouth`, `canadacentral`, `centralindia`, `centraluseuap`, `eastasia`, `eastus`, `eastus2`, `japaneast`, `northeurope`, `southcentralus`, `southeastasia`, `uksouth`, `westcentralus`, `westeurope`, `westus`, och `westus2`.

Om du skickar den hemliga nyckeln i frågesträngen med parametern `Subscription-Key`, måste du ange regionen med Frågeparametern `Subscription-Region`.

Om du använder en ägartoken, måste du skaffa en token från den region-slutpunkten: `https://<your-region>.api.cognitive.microsoft.com/sts/v1.0/issueToken`.


## <a name="errors"></a>Fel

Ett felsvar som standard är ett JSON-objekt med namn/värde-par med namnet `error`. Värdet är också ett JSON-objekt med egenskaper:

  * `code`: En server-definierade felkod.

  * `message`: En sträng som ger en läsbara representation av felet.

Exempelvis skulle en kund med en kostnadsfri utvärderingsprenumeration får följande fel när den lediga kvoten är slut:

```
{
  "error": {
    "code":403001,
    "message":"The operation is not allowed because the subscription has exceeded its free quota."
    }
}
```
Felkoden är en 6-siffrig nummer kombinera 3-siffriga HTTP-statuskoden följt av ett 3-siffriga tal till ytterligare kategorisera felet. Vanliga felkoder finns:

| Kod | Beskrivning |
|:----|:-----|
| 400000| En begäran om indata är inte giltig.|
| 400001| ”Omfång”-parametern är ogiltig.|
| 400002| ”Kategori”-parametern är ogiltig.|
| 400003| Ett språk Specificerare är ogiltig eller saknas.|
| 400004| En target skriptet specificerare (”till” skript ”) är ogiltig eller saknas.|
| 400005| En indata-text är ogiltig eller saknas.|
| 400006| Kombinationen av språket och skriptet är inte giltig.|
| 400018| En källa skriptet specificerare (”från” skript ”) är ogiltig eller saknas.|
| 400019| En av det angivna språket stöds inte.|
| 400020| Ett av elementen i matrisen med-indata är inte giltig.|
| 400021| Parametern API-versionen är ogiltig eller saknas.|
| 400023| En parets angivet språk är inte giltig.|
| 400035| Källspråk (”från” fält) är inte giltig.|
| 400036| Målspråk (”till” fält) är ogiltig eller saknas.|
| 400042| Någon av följande alternativ för angivna (”alternativ”) är inte giltig.|
| 400043| Klienten trace-ID (ClientTraceId fält eller rubrik för X-ClientTranceId) är ogiltig eller saknas.|
| 400050| Indatatexten är för långt.|
| 400064| Parametern ”översättning” är ogiltig eller saknas.|
| 400070| Antalet mål skript (ToScript parameter) matchar inte target språk (för att parametern).|
| 400071| Värdet är inte giltigt för TextType.|
| 400072| Matris med indata-text har för många element.|
| 400073| Skript-parametern är inte giltig.|
| 400074| Brödtexten i begäran är inte giltig JSON.|
| 400075| Språk-par och kategori kombinationen är inte giltig.|
| 400077| Den tillåtna storleken har överskridits.|
| 400079| Den anpassade system som begärdes för översättning av från och till språk finns inte.|
| 401000| Begäran har inte behörighet eftersom autentiseringsuppgifterna är saknas eller är ogiltig.|
| 401015| ”De angivna autentiseringsuppgifterna är för API för taligenkänning. Den här begäran kräver autentiseringsuppgifter för Text-API. Använd en prenumeration på Translator Text API ”.|
| 403000| Åtgärden tillåts inte.|
| 403001| Åtgärden tillåts inte eftersom prenumerationen har överskridit den lediga kvoten.|
| 405000| Begärandemetoden stöds inte för den begärda resursen.|
| 408001| Den anpassade översättningssystemet begärt är inte tillgänglig ännu. Försök igen om några minuter.|
| 415000| Content-Type-huvud är ogiltig eller saknas.|
| 429000, 429001, 429002| Servern avvisade begäran eftersom klienten skickar för många förfrågningar. Minska frekvensen av begäranden för att undvika begränsning.|
| 500000| Det uppstod ett oväntat fel. Om felet kvarstår kan du rapportera det med datum/tid för fel, begär identifierare från svarshuvud X-RequestId och klient-ID från begärandehuvudet X-ClientTraceId.|
| 503000| Tjänsten är otillgänglig. Försök igen. Om felet kvarstår kan du rapportera det med datum/tid för fel, begär identifierare från svarshuvud X-RequestId och klient-ID från begärandehuvudet X-ClientTraceId.|

