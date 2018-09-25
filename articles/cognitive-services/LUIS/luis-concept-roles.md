---
title: Förstå hur roller som används i mönstret-baserade enheter
titleSuffix: Azure Cognitive Services
description: Roller är namngivna, sammanhangsberoende undertyper för en entitet som används i ett mönster. Till exempel i uttryck-köp en biljett från New York till London både New York och London är städer men var och en har en annan betydelse i meningen. New York är ursprung stad och London är målet stad.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 033e5e5e054b0a29961ad60e72b1466b51d1df0c
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47035206"
---
# <a name="entity-roles-in-patterns-are-contextual-subtypes"></a>Entiteten roller i mönster är sammanhangsberoende undertyper
Roller är namngivna, sammanhangsberoende undertyper för en entitet som används bara i [mönster](luis-concept-patterns.md).

Till exempel i uttryck `buy a ticket from New York to London`, både New York och London är städer men har en annan betydelse i meningen. New York är ursprung stad och London är målet stad. 

Roller namnge ett dessa skillnader:

|Entitet|Roll|Syfte|
|--|--|--|
|Plats|ursprung|där planet lämnar från|
|Plats|mål|där planet landar|

## <a name="how-are-roles-used-in-patterns"></a>Hur används roller i mönster?
I ett mönster mall uttryck används roller inom uttryck: 

|Mönstret med entiteten roller|
|--|
|`buy a ticket from {Location:origin} to {Location:destination}`|


## <a name="role-syntax-in-patterns"></a>Roll-syntax i mönster
Entitets- och rollen omges inom parentes, `{}`. Entiteten och rollen avgränsas med ett kolon. 

## <a name="roles-versus-hierarchical-entities"></a>Roller jämfört med hierarkisk entiteter
Hierarkisk entiteter innehåller samma sammanhangsberoende information som roller, men bara till yttranden i **avsikter**. På samma sätt roller tillhandahåller samma sammanhangsberoende information som hierarkisk entiteter men endast i **mönster**.

|Sammanhangsberoende learning|Används i|
|--|--|
|hierarkisk entiteter|Avsikter|
|roles|Mönster|

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du lägger till [roller](luis-how-to-add-entities.md#add-role-to-pattern-based-entity)
