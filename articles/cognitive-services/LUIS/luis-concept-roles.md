---
title: Roller för entiteter – LUIS
titleSuffix: Azure Cognitive Services
description: Roller är namngivna, sammanhangsberoende undertyper för en entitet som används i ett mönster. Till exempel i uttryck `buy a ticket from New York to London`, både New York och London är städer men har en annan betydelse i meningen. New York är ursprung stad och London är målet stad.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 9c437450ead6f05a36725588215ac03b83790373
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563933"
---
# <a name="entity-roles-for-contextual-subtypes"></a>Enhets roller för Sammanhangs beroende under typer

Roller låter entiteter ha namngivna under typer. En roll kan användas med en fördefinierad eller anpassad entitetstyp och används i båda exemplen yttranden och Patterns. 

<a name="example-role-for-entities"></a>
<a name="roles-with-prebuilt-entities"></a>

## <a name="machine-learned-entity-example-of-roles"></a>Exempel på enhets exempel på roller

I uttryck "Köp ett ärende från **New York** till **London**är både New York och London, men var och en har en annan innebörd i meningen. New York är ursprung stad och London är målet stad. 

```
buy a ticket from New York to London
```

Roller namnge ett dessa skillnader:

|Entitetstyp|Entitetsnamn|Role|Syfte|
|--|--|--|--|
|Enkel|Location|ursprung|där planet lämnar från|
|Enkel|Location|mål|där planet landar|

## <a name="non-machine-learned-entity-example-of-roles"></a>Exempel på en icke-maskin som har lärts till ett entitets-exempel

I uttryck "schemalägger mötet från 8 till 9", och båda talen anger en tid, men varje tid har en annan betydelse i uttryck. Roller ger samma namn som skillnaderna. 

```
Schedule the meeting from 8 to 9
```

|Entitetstyp|Rollnamn|Värde|
|--|--|--|
|Fördefinierade datetimeV2|/St|8|
|Fördefinierade datetimeV2|Slut|9|

## <a name="are-multiple-entities-in-an-utterance-the-same-thing-as-roles"></a>Är flera entiteter i en uttryck samma sak som roller? 

Flera entiteter kan finnas i en uttryck och kan extraheras utan att använda roller. Om meningen i meningen anger att entitetens version har ett värde, ska en roll användas. 

### <a name="dont-use-roles-for-duplicates-without-meaning"></a>Använd inte roller för dubbletter utan betydelse

Om uttryck innehåller en lista över platser `I want to travel to Seattle, Cairo, and London.`, är det här en lista där varje objekt inte har någon ytterligare innebörd. 

### <a name="use-roles-if-duplicates-indicate-meaning"></a>Använd roller om dubbletter indikerar betydelse

Om uttryck innehåller en lista över platser med betydelse, `I want to travel from Seattle, with a layover in Londen, landing in Cairo.`ska den här innebörden av ursprung, Layover och mål samlas in med roller.

### <a name="roles-can-indicate-order"></a>Roller kan indikera ordningen

Om uttryck har ändrats för att Visa ordningen som du ville extrahera, `I want to first start with Seattle, second London, then third Cairo`kan du extrahera på ett par olika sätt. Du kan tagga de token som anger rollen, `first start with`, `second`, `third`. Du kan också använda det förinställda enhets **numret** och **GeographyV2** -förbyggda entiteten i en sammansatt entitet för att avbilda idén med order och plats. 

## <a name="how-are-roles-used-in-example-utterances"></a>Hur används roller i exemplet yttranden?

När en entitet har en roll och entiteten är markerad i ett exempel uttryck kan du välja att bara välja entiteten eller välja entiteten och rollen. 

I följande exempel yttranden används entiteter och roller:

|Token-vy|Vyn entitet|
|--|--|
|Jag är intressant att lära sig mer om **Seattle**|Jag är intresse rad av att lära sig mer om {location}|
|Köp ett ärende från Seattle till New York|Köp ett ärende från {Location: Origin} till {Location: destination}|

## <a name="how-are-roles-used-in-patterns"></a>Hur används roller i mönster?
I ett mönster mall uttryck används roller inom uttryck: 

|Mönstret med entiteten roller|
|--|
|`buy a ticket from {Location:origin} to {Location:destination}`|


## <a name="role-syntax-in-patterns"></a>Roll-syntax i mönster
Entitets- och rollen omges inom parentes, `{}`. Entiteten och rollen avgränsas med ett kolon. 

## <a name="entity-roles-versus-collaborator-roles"></a>Enhets roller jämfört med samarbets roller

Enhets roller gäller för LUIS-appens data modell. [Samarbets](luis-concept-collaborator.md) roller gäller för nivåer av redigerings åtkomst. 

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="next-steps"></a>Nästa steg

* Använd en [praktisk självstudie](tutorial-entity-roles.md) med hjälp av enhets roller med entiteter som inte har registrerats av enheten
* Lär dig hur du lägger till [roller](luis-how-to-add-entities.md#add-a-role-to-pattern-based-entity)
