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
ms.date: 12/17/2018
ms.author: diberry
ms.openlocfilehash: 958194d49cd403caeaf9dd21dd90a02cab098e45
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55881465"
---
# <a name="entity-roles-in-patterns-are-contextual-subtypes"></a>Entiteten roller i mönster är sammanhangsberoende undertyper
Roller är namngivna, sammanhangsberoende undertyper för en entitet som används bara i [mönster](luis-concept-patterns.md).

Till exempel i uttryck `buy a ticket from New York to London`, både New York och London är städer men har en annan betydelse i meningen. New York är ursprung stad och London är målet stad. 

Roller namnge ett dessa skillnader:

|Entitet|Roll|Syfte|
|--|--|--|
|Plats|ursprung|där planet lämnar från|
|Plats|mål|där planet landar|
|Fördefinierade datetimeV2|till|slutdatum|
|Fördefinierade datetimeV2|från|Startdatum|

## <a name="how-are-roles-used-in-patterns"></a>Hur används roller i mönster?
I ett mönster mall uttryck används roller inom uttryck: 

|Mönstret med entiteten roller|
|--|
|`buy a ticket from {Location:origin} to {Location:destination}`|


## <a name="role-syntax-in-patterns"></a>Roll-syntax i mönster
Entitets- och rollen omges inom parentes, `{}`. Entiteten och rollen avgränsas med ett kolon. 


[!INCLUDE [H2 Roles versus hierarchical entities](../../../includes/cognitive-services-luis-hier-roles.md)] 

## <a name="example-role-for-entities"></a>Exempelroll för entiteter

En roll är en sammanhangsmässigt inlärda placering för en entitet i ett uttryck. Det är mest effektivt när uttryck har fler än en av den typ av målentitet. Det enklaste exemplet för alla enhetstypen är att skilja mellan en till och från platsen. Platsen kan visas i en mängd olika enhetstyper. 

En exempel-användningsfall överföra en anställd byter avdelning till en annan där varje avdelning är ett objekt i en lista. Exempel: 

`Move [PersonName] from [Department:from] to [Department:to]`. 

I den returnerade förutsägelsen båda avdelning entiteterna kommer att returneras i JSON-svar och var och en innehåller namnet på rollen. 

## <a name="roles-with-prebuilt-entities"></a>Roller med förskapade entiteter

Använd roller med förskapade entiteter för att ge betydelse till olika instanser av färdiga enheten inom ett uttryck. 

### <a name="roles-with-datetimev2"></a>Roller med datetimeV2

Entiteten fördefinierade datetimeV2, gör ett bra jobb så här fungerar en mängd olika i datum och tider i yttranden. Du kanske vill ange datum- och tidsintervall annorlunda än fördefinierade entitetens standard förståelse. 

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du lägger till [roller](luis-how-to-add-entities.md#add-a-role-to-pattern-based-entity)
