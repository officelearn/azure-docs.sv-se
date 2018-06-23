---
title: Språk förstå (THOMAS) API HTTP svarskoder - Azure | Microsoft Docs
titleSuffix: Azure
description: 'Förstå vilka HTTP-svarskoder returneras från THOMAS redigering och slutpunkten API: er'
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: article
ms.date: 04/16/2018
ms.author: v-geberr
ms.openlocfilehash: 9c7381d9dc2ecf302c85c6b4f1f24b24a28d9ebe
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352704"
---
# <a name="luis-api-http-response-codes"></a>LUIS API HTTP svarskoder
Den [redigering](https://aka.ms/luis-authoring-apis) och [endpoint](https://aka.ms/luis-endpoint-apis) API: er för HTTP-svar returkoder. När svarsmeddelanden innehåller information som är specifika för en begäran, är HTTP-status svarskoden Allmänt. 

## <a name="common-status-codes"></a>Vanliga statuskoder
I följande tabell visas några av de vanligaste http-statuskoder svar för den [redigering](https://aka.ms/luis-authoring-apis) och [endpoint](https://aka.ms/luis-endpoint-apis) API: er:

|Kod|API|Förklaring|
|:--|--|--|
|400|Redigering, slutpunkt|begäran parametrarna är felaktiga vilket innebär att nödvändiga parametrar saknas eller är felaktiga, eller för stor|
|400|Redigering, slutpunkt|begärans brödtext är felaktig och JSON finns saknas, är felaktig eller för stor|
|401|Redigering|används för slutpunkten prenumeration nyckel, i stället för redigering av nyckeln|
|401|Redigering, slutpunkt|Ogiltig, är tomt eller har en felaktig nyckel|
|401|Redigering, slutpunkt| nyckeln matchar inte region|
|401|Redigering|du är inte ägare eller deltagare|
|401|Redigering|Ogiltig order för API-anrop|
|403|Redigering, slutpunkt|totala månatliga viktiga kvot överskriden|
|409|Slutpunkt|programmet fortfarande läses in|
|410|Slutpunkt|programmet måste retrained och publiceras igen|
|414|Slutpunkt|frågan överskrider antalet tillåtna tecken|
|429|Redigering, slutpunkt|Hastighetsbegränsning har överskridits (begäranden per sekund)|