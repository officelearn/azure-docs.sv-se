---
title: Migrera API guide från v1 till v2 | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur in migrering till den senaste API.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/01/2018
ms.author: v-geberr
ms.openlocfilehash: 060baa6578f8a31234a1a667e2d591b92c39a06f
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264292"
---
# <a name="api-v2-migration-guide"></a>Migreringsguide för API-v2
Version 1 [endpoint](https://aka.ms/v1-endpoint-api-docs) och [redigering](https://aka.ms/v1-authoring-api-docs) API: er att bli inaktuell. Använd den här guiden för att förstå hur du migrerar till version 2 [endpoint](https://aka.ms/luis-endpoint-apis) och [redigering](https://aka.ms/luis-authoring-apis) API: er. 

## <a name="new-azure-regions"></a>Nya Azure-regioner
THOMAS har nya [regioner](https://aka.ms/LUIS-regions) för LUIS APIs. THOMAS innehåller en annan webbplats för regionsgrupper. Programmet måste vara skapad i samma region som du förväntar dig att fråga. Program migreras inte automatiskt regioner. Du exporterar appen från en region och sedan importeras till en annan för att vara tillgängligt i en ny region.

## <a name="authoring-route-changes"></a>Redigera vägen ändringar
Redigering API-flödet har ändrats från med hjälp av den **prog** väg till med hjälp av den **api** vägen.


| version | route |
|--|--|
|1|/Luis/v1.0/**prog**/apps|
|2|/Luis/**api**/v2.0/apps|


## <a name="endpoint-route-changes"></a>Slutpunkten vägändringar
Slutpunkten API har nya querystring parametrar som ett annat svar. Om flaggan utförlig är true, returneras alla avsikter oavsett poäng, i en matris med namnet avsikter, förutom topScoringIntent.

| version | Hämta väg |
|--|--|
|1|/Luis/v1/Application? ID = {appId} & q = {q}|
|2|/ luis/v2.0/apps/{appId}?q={q} [& timezoneOffset] [& detaljerad] [& stavningskontroll] [& fristående] [& bing-stavningskontroll-check-prenumeration-nyckeln] [& loggen]|


V1 endpoint lyckat svar:
```JSON
{
  "odata.metadata":"https://dialogice.cloudapp.net/odata/$metadata#domain","value":[
    {
      "id":"bccb84ee-4bd6-4460-a340-0595b12db294","q":"turn on the camera","response":"[{\"intent\":\"OpenCamera\",\"score\":0.976928055},{\"intent\":\"None\",\"score\":0.0230718572}]"
    }
  ]
}
```

v2 endpoint lyckat svar:
```JSON
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

## <a name="key-management-no-longer-in-api"></a>Hantering av nycklar inte längre i API
Nyckeln prenumeration API: er är inaktuella returnerar 410 GONE.

| version | route |
|--|--|
|1|/Luis/v1.0/prog/subscriptions|
|1|/ luis/v1.0/prog/subscriptions/{subscriptionKey}|

Azure [prenumeration nycklar](luis-how-to-azure-subscription.md) genereras i Azure-portalen. Du tilldelar nyckeln till en THOMAS app på den **[publicera](manage-keys.md)** sidan. Du behöver inte veta faktiska värdet för nyckeln. THOMAS använder prenumerationsnamn för att kontrollera tilldelningen. 

## <a name="new-versioning-route"></a>Ny väg för versionshantering
V2-modellen nu ingår i en [version](luis-how-to-manage-versions.md). Ett versionsnamn är 10 tecken i flödet. Standardversionen är ”0.1”.

| version | route |
|--|--|
|1|/Luis/v1.0/**prog**/apps/ {appId} / entiteter|
|2|/Luis/**api**/v2.0/apps/{appId}/**versioner**/ {versionId} / entiteter|

## <a name="metadata-renamed"></a>Byta namn på metadata
Flera API: er som returnerar THOMAS metadata har nya namn.

| V1 vägnamnet | vägnamnet v2 |
|--|--|
|PersonalAssistantApps |assistenter|
|applicationcultures|kulturer|
|applicationdomains|domäner|
|applicationusagescenarios|usagescenarios|


## <a name="sample-renamed-to-suggest"></a>”Sample” filnamnstillägget ”föreslår”
THOMAS föreslår utterances från befintliga [endpoint utterances](label-suggested-utterances.md) som kan förbättra modellen. I den tidigare versionen detta hette **exempel**. I den nya versionen namnet ändras från exemplet till **föreslår**. Detta kallas **[granska endpoint utterances](https://docs.microsoft.com/azure/cognitive-services/LUIS/label-suggested-utterances)** i THOMAS-webbplatsen.

| version | route |
|--|--|
|1|/Luis/v1.0/**prog**/apps/ {appId} /entities/ {ID för entiteterna} /**exempel**|
|1|/Luis/v1.0/**prog**/apps/ {appId} /intents/ {intentId} /**exempel**|
|2|/Luis/**api**/v2.0/apps/{appId}/**versioner**/ {versionId} /entities/ {ID för entiteterna} /**föreslår**|
|2|/Luis/**api**/v2.0/apps/{appId}/**versioner**/ {versionId} /intents/ {intentId} /**föreslår**|


## <a name="create-app-from-prebuilt-domains"></a>Skapa en app från fördefinierade domäner
[Fördefinierade domäner](luis-how-to-use-prebuilt-domains.md) ger en fördefinierad domänmodell. Fördefinierade domäner kan du snabbt utveckla THOMAS programmet för vanliga domäner. Detta API kan du skapa en ny app baserat på en fördefinierad domän. Svaret är ny appID.

|v2-väg|Verb|
|--|--|
|/Luis/API/v2.0/Apps/customprebuiltdomains  |Get, post|
|/ luis/api/v2.0/apps/customprebuiltdomains/{culture}  |Hämta|

## <a name="importing-1x-app-into-2x"></a>Importera 1.x app till 2.x
Exporterade 1.x appens JSON har vissa områden som du behöver ändra innan du importerar till [THOMAS] [ LUIS] 2.0. 

### <a name="prebuilt-entities"></a>Fördefinierade entiteter 
Den [färdiga entiteter](Pre-builtEntities.md) har ändrats. Kontrollera att du använder V2 färdiga entiteter. Detta innefattar att använda [datetimeV2](pre-builtentities.md?#use-a-prebuilt-datetimev2-entity), i stället för datetime. 

### <a name="actions"></a>Åtgärder
Egenskapen åtgärder är inte längre giltig. Det bör vara en tom 

### <a name="labeled-utterances"></a>Märkt utterances
V1 tillåtna märkt utterances till mellanslag i början eller slutet av ett ord eller en fras. Ta bort utrymmen. 

## <a name="common-reasons-for-http-response-status-codes"></a>Vanliga orsaker till statuskoder för HTTP-svar
Se [LUIS API svarskoder](luis-reference-response-codes.md).

## <a name="next-steps"></a>Nästa steg

Använd API-dokumentationen v2 för att uppdatera befintliga REST-anrop till LIUS [endpoint](https://aka.ms/luis-endpoint-apis) och [redigering](https://aka.ms/luis-authoring-apis) API: er. 

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions