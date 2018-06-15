---
title: Förstå hur roller används i mönstret-baserade enheter - Azure | Microsoft Docs
description: Lär dig hur rollen används i en mönster-baserade entitet för att namnge en undertyp kontextuella entitet.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/08/2018
ms.author: v-geberr;
ms.openlocfilehash: ab6100e33fb767528b87c6afde4c5ef275fc7c81
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2018
ms.locfileid: "35356369"
---
# <a name="entity-roles-in-patterns-are-contextual-subtypes"></a>Entiteten roller i mönster är sammanhangsberoende undertyper
Roller är namngivna, kontextuella undertyper för en entitet som används i [mönster](luis-concept-patterns.md).

Till exempel i utterance `buy a ticket from New York to London`, både New York och London är orter men var och en har en annan betydelse i meningen. New York är ursprung orten och London är målet stad. 

Roller namnge ett dessa skillnader:

|Entitet|Roll|Syfte|
|--|--|--|
|Plats|ursprung|där planet lämnar från|
|Plats|Mål|där de hamnar plan|

## <a name="how-are-roles-used-in-patterns"></a>Hur används roller i mönster?
I ett mönster mallen utterance används roller i utterance: 

```
buy a ticket from {Location:origin} to {Location:destination}
```

## <a name="role-syntax-in-patterns"></a>Syntax för roll i mönster
Enhet och roll omges av parenteser, `{}`. Entiteten och rollen avgränsas med ett kolon. 

## <a name="roles-versus-hierarchical-entities"></a>Roller jämfört med hierarkiska entiteter
Hierarkisk enheter ger samma kontextuella information som roller men endast i utterances i **intents**. På liknande sätt roller ger samma kontextuella information som hierarkisk enheter men endast i **mönster**.

|Kontextuella learning|Används i|
|--|--|
|hierarkisk entiteter|Avsikter|
|roles|Mönster|

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du lägger till [roller](luis-how-to-add-entities.md#add-role-to-pattern-based-entity)
