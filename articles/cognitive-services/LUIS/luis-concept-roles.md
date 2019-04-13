---
title: Roller för entiteter
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
ms.openlocfilehash: 5fa922cb91d34483256faf4dcf70569aa2f17b97
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/12/2019
ms.locfileid: "59522494"
---
# <a name="entity-roles-for-contextual-subtypes"></a>Entiteten roller för sammanhangsberoende undertyper

Roller kan enheter som har gett undertyper. En roll kan användas med alla typer av fördefinierade eller anpassade entitet och används i både exempel yttranden och mönster. 

<a name="example-role-for-entities"></a>
<a name="roles-with-prebuilt-entities"></a>

## <a name="machine-learned-entity-example-of-roles"></a>Datorn lärt dig entitet exempel på roller

I uttryck ”köp en biljett från **New York** till **London**, både New York och London är städer men har en annan betydelse i meningen. New York är ursprung stad och London är målet stad. 

```
buy a ticket from New York to London
```

Roller namnge ett dessa skillnader:

|Entitetstyp|Entitetsnamn|Roll|Syfte|
|--|--|--|--|
|Enkel|Plats|ursprung|där planet lämnar från|
|Enkel|Plats|mål|där planet landar|

## <a name="non-machine-learned-entity-example-of-roles"></a>Icke-machine-lärt dig entitet exempel på roller

I uttryck ”schemalägga möte från 8 till 9”, båda talen, som anger en tid men varje gång har en annan betydelse i uttryck. Roller tillhandahåller namnet för skillnaderna. 

```
Schedule the meeting from 8 to 9
```

|Entitetstyp|Rollnamn|Värde|
|--|--|--|
|Fördefinierade datetimeV2|StartTime|8|
|Fördefinierade datetimeV2|EndTime|9|

## <a name="are-multiple-entities-in-an-utterance-the-same-thing-as-roles"></a>Är samma sak som roller för flera enheter i ett uttryck? 

Flera entiteter kan finnas i ett uttryck och du kan extrahera utan att använda roller. Om kontexten för meningen indikerar med versionen av entiteten har ett värde, så en roll som ska användas. 

### <a name="dont-use-roles-for-duplicates-without-meaning"></a>Använd inte roller för dubbletter utan betydelse

Om uttryck som innehåller en lista över platser, `I want to travel to Seattle, Cairo, and London.`, detta är en lista där varje objekt inte har en ytterligare betydelse. 

### <a name="use-roles-if-duplicates-indicate-meaning"></a>Använd roller om dubbletter visar betydelse

Om uttryck som innehåller en lista över platser med betydelse, `I want to travel from Seattle, with a layover in Londen, landing in Cairo.`, den här innebörden av ursprung, layover och mål ska samlas in med roller.

### <a name="roles-can-indicate-order"></a>Roller kan visa ordning

Om uttryck som har ändrats för att ange order som du vill extrahera, `I want to first start with Seattle, second London, then third Cairo`, du kan extrahera i ett par olika sätt. Du kan tagga de token som indikera vilken roll `first start with`, `second`, `third`. Du kan också använda fördefinierade entiteten **ordningstal** och **GeographyV2** fördefinierade entitet i en sammansatt entitet att samla in idé order och plats. 

## <a name="how-are-roles-used-in-example-utterances"></a>Hur används roller i exempel yttranden?

När en entitet har en roll och entiteten har markerats i en exempel-uttryck, har du valet av Markera bara entiteten och markera den enhet och roll. 

Följande exempel talade använda entiteter och roller:

|Token vy|Visa entitet|
|--|--|
|Jag är intressant veta mer om **Seattle**|Jag vill veta mer om {Location}|
|Köp en biljett från Seattle till New York|Köp en biljett från {plats: ursprunget} till {plats: Destination}|

## <a name="how-are-roles-related-to-hierarchical-entities"></a>Hur roller rör hierarkisk entiteter?

Roller är nu tillgängliga för alla entiteter i exempel yttranden, samt tidigare användning av mönster. Eftersom de är tillgängliga överallt, ersätter de behovet av hierarkiska entiteter. Nya entiteter ska skapas med roller i stället för med hierarkisk entiteter. 

Hierarkisk entiteter gälla så småningom upphör att.

## <a name="how-are-roles-used-in-patterns"></a>Hur används roller i mönster?
I ett mönster mall uttryck används roller inom uttryck: 

|Mönstret med entiteten roller|
|--|
|`buy a ticket from {Location:origin} to {Location:destination}`|


## <a name="role-syntax-in-patterns"></a>Roll-syntax i mönster
Entitets- och rollen omges inom parentes, `{}`. Entiteten och rollen avgränsas med ett kolon. 

## <a name="entity-roles-versus-collaborator-roles"></a>Entiteten roller jämfört med medarbetare roller

Entiteten roller gäller datamodellen på LUIS-app. [Medarbetare](luis-concept-collaborator.md) roller avser nivåer av redigeringen av åtkomst. 

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="next-steps"></a>Nästa steg

* Använd en [praktiska självstudier](tutorial-entity-roles.md) använder entiteten med icke-machine-lärt dig entiteter
* Lär dig hur du lägger till [roller](luis-how-to-add-entities.md#add-a-role-to-pattern-based-entity)
