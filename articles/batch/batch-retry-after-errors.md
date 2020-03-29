---
title: Försöker igen aktiviteter efter ett fel
description: Sök efter fel och försök igen.
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
ms.openlocfilehash: 94ed936e619461a2dbf7ec837c2d80e21c01c88e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919999"
---
# <a name="detecting-and-handling-batch-service-errors"></a>Identifiera och hantera batch-tjänstfel

Det är viktigt att komma ihåg att söka efter fel när du arbetar med ett REST-tjänst-API. Det är inte ovanligt att fel uppstår när batch-jobb körs.

## <a name="common-errors"></a>Vanliga fel 

- Nätverksfel – det här är begäranden som aldrig nådde Batch eller batch-svaret nådde inte klienten i tid.
- Interna serverfel - dessa är standard 5xx statuskod HTTP-svar.
- Begränsning kan orsaka fel som 429 eller 503 statuskod HTTP-svar med återförsök-efter-huvudet.
- 4xx fel som innehåller sådana fel som AlreadyExists och InvalidOperation. Det innebär att resursen inte är i rätt tillstånd för tillståndsövergången.

Detaljerad information om de olika typerna av felkoder och specifika felkoder finns i [Batchstatus och felkoder](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes).

## <a name="when-to-retry"></a>När du ska försöka igen

Batch-API:erna meddelar dig om det finns ett fel. De kan alla göras om och de innehåller alla en global hanterare för återförsök för detta ändamål. Det är bäst att använda denna inbyggda mekanism.

Efter ett fel bör du vänta lite (flera sekunder mellan återförsök) innan du försöker igen. Om du försöker igen för ofta eller för snabbt begränsas återförsökshanteraren.

### <a name="for-more-information"></a>Mer information  

[Batch-API:er och verktyg](batch-apis-tools.md) länkar till API-referensinformation. .NET API, till exempel, har en [RetryPolicyProvider klass]( https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.retrypolicyprovider?view=azure-dotnet) där den nödvändiga återförsöksprincipen ska anges. 

Detaljerad information om varje API och deras standardprinciper för återförsök finns [i Batchstatus och felkoder](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes).
