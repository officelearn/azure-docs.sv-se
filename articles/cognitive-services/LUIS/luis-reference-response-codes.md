---
title: API HTTP-svarskoder - LUIS
titleSuffix: Azure Cognitive Services
description: Förstå vilka HTTP-svarskoder som returneras från LUIS Authoring and Endpoint API:er
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270254"
---
# <a name="common-api-response-codes-and-their-meaning"></a>Vanliga API-svarskoder och deras innebörd

[Redigerings-](https://go.microsoft.com/fwlink/?linkid=2092087) och slutpunkts-API:erna returnerar HTTP-svarskoder. [endpoint](https://go.microsoft.com/fwlink/?linkid=2092356) Svarsmeddelanden innehåller information som är specifik för en begäran, men HTTP-svarsstatuskoden är allmän.

## <a name="common-status-codes"></a>Vanliga statuskoder
I följande tabell visas några av de vanligaste HTTP-svarsstatuskoderna för [redigerings-](https://go.microsoft.com/fwlink/?linkid=2092087) och slutpunkts-API:erna: [endpoint](https://go.microsoft.com/fwlink/?linkid=2092356)

|Kod|API|Förklaring|
|:--|--|--|
|400|Redigering, slutpunkt|parametrarna för begäran är felaktiga, vilket innebär att de parametrar som krävs saknas, missbildas eller är för stora|
|400|Redigering, slutpunkt|begärans kropp är felaktig, vilket innebär att JSON saknas, missbildas eller är för stor|
|401|Redigering|använd slutpunktsprenumerationsnyckel i stället för att skapa nyckel|
|401|Redigering, slutpunkt|ogiltig, felaktig eller tom nyckel|
|401|Redigering, slutpunkt| nyckeln matchar inte region|
|401|Redigering|du inte är ägare eller medarbetare|
|401|Redigering|ogiltig ordning för API-anrop|
|403|Redigering, slutpunkt|total gräns för månatlig nyckelkvot överskriden|
|409|Slutpunkt|programmet laddas fortfarande|
|410|Slutpunkt|ansökan måste omskolas och publiceras på nytt|
|414|Slutpunkt|frågan överskrider maxteckengränsen|
|429|Redigering, slutpunkt|Hastighetsgränsen överskrids (begäranden/sekund)|

## <a name="next-steps"></a>Nästa steg

* DOKUMENTATION för REST [API-redigering](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) och [slutpunkt](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78)
