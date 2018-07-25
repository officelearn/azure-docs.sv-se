---
title: Förstå hur roller som används i mönstret-baserade enheter – Azure | Microsoft Docs
description: Lär dig hur en roll ska användas i en mönsterbaserad entitet för att ge ett namn till en sammanhangsberoende entitet.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/08/2018
ms.author: diberry
ms.openlocfilehash: d2692cdce9da7428bd7b30c4feaf7347792618f5
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2018
ms.locfileid: "39222711"
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

```
buy a ticket from {Location:origin} to {Location:destination}
```

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
