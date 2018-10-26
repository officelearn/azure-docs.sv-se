---
title: Förstå hur roller som används i mönstret-baserade enheter
titleSuffix: Azure Cognitive Services
description: Roller är namngivna, sammanhangsberoende undertyper för en entitet som används i ett mönster. Till exempel i uttryck-köp en biljett från New York till London både New York och London är städer men var och en har en annan betydelse i meningen. New York är ursprung stad och London är målet stad.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 94a02a06335b35a36c35d4b69776b1eb630b402d
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50086036"
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


[!include[H2 Roles versus hierarchical entities](../../../includes/cognitive-services-luis-hier-roles.md)] 

## <a name="roles-with-prebuilt-entities"></a>Roller med förskapade entiteter

Använd roller med förskapade entiteter för att ge betydelse till olika instanser av färdiga enheten inom ett uttryck. 

### <a name="roles-with-datetimev2"></a>Roller med datetimeV2

Entiteten fördefinierade datetimeV2, gör ett bra jobb så här fungerar en mängd olika i datum och tider i yttranden. Du kanske vill ange datum- och tidsintervall annorlunda än fördefinierade entitetens standard förståelse. 

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du lägger till [roller](luis-how-to-add-entities.md#add-role-to-pattern-based-entity)
