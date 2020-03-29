---
title: v1 till v2 API-migrering
titleSuffix: Azure Cognitive Services
description: Versions-1-slutpunkten och redigering av API:er för språkförståelse är inaktuella. Använd den här guiden för att förstå hur du migrerar till versions 2-slutpunkt och skapar API:er.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: diberry
ms.openlocfilehash: 2f67bf0951ef8928297c71e8fc9f924cf05c63f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "68932692"
---
# <a name="api-v1-to-v2-migration-guide-for-luis-apps"></a>API v1 till v2 Migreringsguide för LUIS-appar
Slutpunkten för [endpoint](https://aka.ms/v1-endpoint-api-docs) version 1 och [redigering api:er](https://aka.ms/v1-authoring-api-docs) är inaktuella. Använd den här guiden för att förstå hur du migrerar till [versions 2-slutpunkt](https://go.microsoft.com/fwlink/?linkid=2092356) och [skapar](https://go.microsoft.com/fwlink/?linkid=2092087) API:er. 

## <a name="new-azure-regions"></a>Nya Azure-regioner
LUIS har nya [regioner](https://aka.ms/LUIS-regions) för LUIS API:er. LUIS tillhandahåller en annan portal för regiongrupper. Programmet måste skapas i samma region som du förväntar dig att fråga. Program migrerar inte automatiskt regioner. Du exporterar appen från en region och importerar sedan till en annan för att den ska vara tillgänglig i en ny region.

## <a name="authoring-route-changes"></a>Redigera ruttändringar
Api-vägen för redigering har ändrats från att använda **prog-vägen** till att använda **api-vägen.**


| version | route |
|--|--|
|1|/luis/v1.0/**prog**/apps|
|2|/luis/**api**/v2.0/apps|


## <a name="endpoint-route-changes"></a>Ändringar av slutpunktsrutten
Slutpunkts-API:et har nya frågesträngparametrar samt ett annat svar. Om den utförliga flaggan är sann returneras alla avsikter, oavsett poäng, i en matris med namnet avsikter, utöver topScoringIntent.

| version | HÄMTA rutt |
|--|--|
|1|/luis/v1/application?ID={appId}&q={q}|
|2|/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&utförlig][&stavningskontroll][&mellanlagring][&bing-stavningskontroll-prenumeration-nyckel][&logg]|


v1 slutpunkt framgång svar:
```json
{
  "odata.metadata":"https://dialogice.cloudapp.net/odata/$metadata#domain","value":[
    {
      "id":"bccb84ee-4bd6-4460-a340-0595b12db294","q":"turn on the camera","response":"[{\"intent\":\"OpenCamera\",\"score\":0.976928055},{\"intent\":\"None\",\"score\":0.0230718572}]"
    }
  ]
}
```

v2 slutpunkt framgång svar:
```json
{
  "query": "forward to frank 30 dollars through HSBC",
  "topScoringIntent": {
    "intent": "give",
    "score": 0.3964121
  },
  "entities": [
    {
      "entity": "30",
      "type": "builtin.number",
      "startIndex": 17,
      "endIndex": 18,
      "resolution": {
        "value": "30"
      }
    },
    {
      "entity": "frank",
      "type": "frank",
      "startIndex": 11,
      "endIndex": 15,
      "score": 0.935219169
    },
    {
      "entity": "30 dollars",
      "type": "builtin.currency",
      "startIndex": 17,
      "endIndex": 26,
      "resolution": {
        "unit": "Dollar",
        "value": "30"
      }
    },
    {
      "entity": "hsbc",
      "type": "Bank",
      "startIndex": 36,
      "endIndex": 39,
      "resolution": {
        "values": [
          "BankeName"
        ]
      }
    }
  ]
}
```

## <a name="key-management-no-longer-in-api"></a>Nyckelhantering inte längre i API
Api:erna för prenumerationsslutpunktsnyckeln är inaktuella och returnerar 410 BORTA.

| version | route |
|--|--|
|1|/luis/v1.0/prog/subscriptions /abonnemang|
|1|/luis/v1.0/prog/subscriptions/{subscriptionKey}|

[Azure-slutpunktsnycklar](luis-how-to-azure-subscription.md) genereras i Azure-portalen. Du tilldelar nyckeln till en LUIS-app på sidan **[Publicera.](luis-how-to-azure-subscription.md)** Du behöver inte känna till det faktiska nyckelvärdet. LUIS använder prenumerationsnamnet för att göra tilldelningen. 

## <a name="new-versioning-route"></a>Ny versionsväg
V2-modellen finns nu i en [version](luis-how-to-manage-versions.md). Ett versionsnamn är 10 tecken i rutten. Standardversionen är "0.1".

| version | route |
|--|--|
|1|/luis/v1.0/**prog**/apps/{appId}/entities|
|2|/luis/**api**/v2.0/apps/{appId}/**versioner**/{versionId}/entities|

## <a name="metadata-renamed"></a>Metadata har bytt namn
Flera API:er som returnerar LUIS-metadata har nya namn.

| v1-ruttnamn | v2-ruttnamn |
|--|--|
|PersonalAssistantApps |Assistenter|
|applikationskulturer|kulturer|
|applicationdomains|Domäner|
|ansökanusagescenarios|användningscenarios|


## <a name="sample-renamed-to-suggest"></a>"Sample" omdöpt till "föreslå"
LUIS föreslår yttranden från befintliga [slutpunktsyttranden](luis-how-to-review-endpoint-utterances.md) som kan förbättra modellen. I den tidigare versionen fick den här namnet **exempel**. I den nya versionen ändras namnet från exempel till **förslag**. Detta kallas **[Granska slutpunktsyttranden](luis-how-to-review-endpoint-utterances.md)** på LUIS-webbplatsen.

| version | route |
|--|--|
|1|/luis/v1.0/**prog**/apps/{appId}/entities/{entityId}/**exempel**|
|1|/luis/v1.0/**prog**/apps/{appId}/intents/{intentId}/**exempel**|
|2|/luis/**api**/v2.0/apps/{appId}/**versioner**/{versionId}/entities/{entityId}/**föreslå**|
|2|/luis/**api**/v2.0/apps/{appId}/**versioner**/{versionId}/intents/{intentId}/**föreslå**|


## <a name="create-app-from-prebuilt-domains"></a>Skapa app från fördefinierade domäner
[Fördefinierade domäner](luis-how-to-use-prebuilt-domains.md) tillhandahåller en fördefinierad domänmodell. Med fördefinierade domäner kan du snabbt utveckla luis-programmet för vanliga domäner. Med det här API:et kan du skapa en ny app baserad på en fördefinierad domän. Svaret är det nya appID.

|v2-rutt|Verb|
|--|--|
|/luis/api/v2.0/apps/customprebuiltdomains  |få, posta|
|/luis/api/v2.0/apps/customprebuiltdomains/{culture}  |get|

## <a name="importing-1x-app-into-2x"></a>Importera 1.x-app till 2.x
Den exporterade 1.x-appens JSON har vissa områden som du behöver ändra innan du importerar till [LUIS][LUIS] 2.0. 

### <a name="prebuilt-entities"></a>Fördefinierade entiteter 
De [fördefinierade entiteterna](luis-prebuilt-entities.md) har ändrats. Kontrollera att du använder V2 fördefinierade entiteter. Detta inkluderar användning av [datetimeV2](luis-reference-prebuilt-datetimev2.md), i stället för datetime. 

### <a name="actions"></a>Åtgärder
Egenskapen Actions är inte längre giltig. Det bör vara en tom 

### <a name="labeled-utterances"></a>Märkta yttranden
V1 tillät etiketterade yttranden att inkludera blanksteg i början eller slutet av ordet eller frasen. Tog bort mellanslagen. 

## <a name="common-reasons-for-http-response-status-codes"></a>Vanliga orsaker till HTTP-svarsstatuskoder
Se [LUIS API-svarskoder](luis-reference-response-codes.md).

## <a name="next-steps"></a>Nästa steg

Använd v2 API-dokumentationen för att uppdatera befintliga REST-anrop till [LUIS-slutpunkt](https://go.microsoft.com/fwlink/?linkid=2092356) och [skapa](https://go.microsoft.com/fwlink/?linkid=2092087) API:er. 

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions
