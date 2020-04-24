---
title: Försöka utföra uppgifter igen efter ett fel
description: Kontrol lera om det finns fel och försök igen.
ms.topic: article
ms.date: 02/15/2020
ms.custom: seodec18
ms.openlocfilehash: 8addc4418f268a2c27b730543bdb309ef45fd5f4
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82116544"
---
# <a name="detecting-and-handling-batch-service-errors"></a>Identifiera och hantera batch-tjänstens fel

Det är viktigt att komma ihåg att söka efter fel när du arbetar med ett REST-API. Det är inte ovanligt att fel uppstår när du kör batch-jobb.

## <a name="common-errors"></a>Vanliga fel 

- Nätverks haverier – det här är begär Anden som aldrig nått batch-eller batch-svaret nådde inte klienten i tid.
- Interna Server fel – dessa är standard status kod för 5xx HTTP-svar.
- Begränsning kan orsaka fel, till exempel 429 eller 503, status kod HTTP-svar med rubriken försök igen.
- 4xx-fel som innehåller sådana fel som AlreadyExists och InvalidOperation. Det innebär att resursen inte är i rätt tillstånd för tillstånds över gången.

Detaljerad information om de olika typerna av felkoder och särskilda felkoder finns i [batch-status och felkoder](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes).

## <a name="when-to-retry"></a>Försök igen

Batch-API: erna meddelar dig om det uppstår ett problem. De kan provas igen och alla innehåller en global återförsöks hanterare för detta ändamål. Det är bäst att använda den här inbyggda mekanismen.

Efter ett haveri bör du vänta en stund (flera sekunder mellan återförsök) innan du försöker igen. Om du försöker igen för ofta eller för snabbt, kommer hanteraren för återförsök att begränsas.

### <a name="for-more-information"></a>Mer information  

[Batch-API: er och verktyg](batch-apis-tools.md) länkar till information om API-Referensinformation. .NET-API: t kan till exempel ha en [RetryPolicyProvider-klass]( https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.retrypolicyprovider?view=azure-dotnet) där den nödvändiga principen för återförsök ska anges. 

För detaljerad information om varje API och deras standard principer för återförsök, Läs [batch-status och felkoder](https://docs.microsoft.com/rest/api/batchservice/batch-status-and-error-codes).
