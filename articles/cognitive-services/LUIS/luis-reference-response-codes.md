---
title: API HTTP-svars koder – LUIS
titleSuffix: Azure Cognitive Services
description: 'Förstå vilka HTTP-svarskoder returneras från LUIS redigerings- och slutpunkt-API: er'
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 03/04/2019
ms.author: diberry
ms.openlocfilehash: 405381a2d80ee6469b2b90bcb25923004fe55882
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270254"
---
# <a name="common-api-response-codes-and-their-meaning"></a>Vanliga API-svars koder och deras innebörd

API: erna för [redigering](https://go.microsoft.com/fwlink/?linkid=2092087) och [slut punkter](https://go.microsoft.com/fwlink/?linkid=2092356) returnerar http-svars koder. Svarsmeddelanden innehåller information som är specifika för en begäran, är HTTP-svarsstatuskod allmän.

## <a name="common-status-codes"></a>Vanliga statuskoder
I följande tabell visas några av de vanligaste HTTP-svars status koderna för [redigerings](https://go.microsoft.com/fwlink/?linkid=2092087) -och [slut punkts](https://go.microsoft.com/fwlink/?linkid=2092356) -API: erna:

|Kod|API|Förklaring|
|:--|--|--|
|400|Redigering, slutpunkten|begärans-parametrarna är felaktiga vilket innebär att de obligatoriska parametrarna är saknas, är för stor eller har en felaktig|
|400|Redigering, slutpunkten|brödtexten för begäran är felaktig JSON finns saknas, är för stor eller har en felaktig|
|401|Redigering|används slutpunktsnyckeln för prenumerationen, i stället för redigering av nyckel|
|401|Redigering, slutpunkten|Ogiltig, är tomt eller har en felaktig nyckel|
|401|Redigering, slutpunkten| nyckel matchar inte region|
|401|Redigering|du är inte ägare eller deltagare|
|401|Redigering|Ogiltig order för API-anrop|
|403|Redigering, slutpunkten|Totalt antal månatliga viktiga lagringskvoten har överskridits|
|409|Slutpunkt|programmet fortfarande läses in|
|410|Slutpunkt|programmet måste modellkomponenten och publiceras igen|
|414|Slutpunkt|frågan överskrider antalet tillåtna tecken|
|429|Redigering, slutpunkten|Hastighetsbegränsning överskriden (förfrågningar per sekund)|

## <a name="next-steps"></a>Nästa steg

* REST API [redigering](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) och [slut punkts](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78) dokumentation
