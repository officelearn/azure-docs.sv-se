---
title: Försöka utföra uppgifter igen efter ett fel
description: Kontrol lera om det finns fel och försök igen.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
tags: azure-batch
ms.assetid: 16279b23-60ff-4b16-b308-5de000e4c028
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/15/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: eda567fda13d6caca679d0ce4947e042eca9530d
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77652014"
---
# <a name="detecting-and-handling-batch-service-errors"></a>Identifiera och hantera batch-tjänstens fel

Det är viktigt att komma ihåg att söka efter fel när du arbetar med ett REST-API. Det är inte ovanligt att fel uppstår när du kör batch-jobb.

## <a name="common-errors"></a>Vanliga fel 

- Nätverks haverier – det här är begär Anden som aldrig nått batch-eller batch-svaret nådde inte klienten i tid.
- Interna Server fel – dessa är standard status kod för 5xx HTTP-svar.
- Begränsning kan orsaka fel, till exempel 429 eller 503, status kod HTTP-svar med rubriken försök igen.
- 4xx-fel som innehåller sådana fel som AlreadyExists och InvalidOperation. Det innebär att resursen inte är i rätt tillstånd för tillstånds över gången.

## <a name="when-to-retry"></a>Försök igen

Batch-API: erna meddelar dig om det uppstår ett problem. De kan provas igen och alla innehåller en global återförsöks hanterare för detta ändamål. Det är bäst att använda den här inbyggda mekanismen.

Efter ett haveri bör du vänta en stund (flera sekunder mellan återförsök) innan du försöker igen. Om du försöker igen för ofta eller för snabbt, kommer hanteraren för återförsök att begränsas.


För detaljerad information om varje API och deras standard principer för återförsök, Läs [batch-status och felkoder](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes).
