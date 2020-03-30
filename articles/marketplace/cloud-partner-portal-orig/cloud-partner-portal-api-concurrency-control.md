---
title: Samtidighetskontroll | Azure Marketplace
description: Strategier för samtidighetskontroll för API:er för molnpartnerportalen.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 6d0f035d77e74f157b793b9edf3ab5d3494096d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288656"
---
# <a name="concurrency-control"></a>Samtidighetskontroll

Varje anrop till Cloud Partner Portal-publicerings-API:erna måste uttryckligen ange vilken samtidighetskontrollstrategi som ska användas. Om du inte anger **If-Match-huvudet** resulterar det i ett HTTP 400-felsvar. Vi erbjuder två strategier för samtidighetskontroll.

-   **Optimistisk** - Klienten som utför uppdateringen verifierar om data har ändrats sedan den senast läste data.
-   **Senaste vinner** - Klienten uppdaterar data direkt, oavsett om ett annat program har ändrat den sedan den senaste lästiden.

<a name="optimistic-concurrency-workflow"></a>Arbetsflöde för optimistisk samtidighet
-------------------------------

Vi rekommenderar att du använder den optimistiska samtidighetsstrategin, med följande arbetsflöde, för att garantera att inga oväntade ändringar görs i dina resurser.

1.  Hämta en entitet med API:erna. Svaret innehåller ett ETag-värde som identifierar den lagrade versionen av entiteten (vid tidpunkten för svaret).
2.  Vid tidpunkten för uppdateringen, inkludera samma ETag-värde i det obligatoriska **if-match-begäranden.**
3.  API:et jämför ETag-värdet som tas emot i begäran med det aktuella ETag-värdet för entiteten i en atomtransaktion.
    *   Om ETag-värdena är olika `412 Precondition Failed` returnerar API:et ett HTTP-svar. Det här felet anger att antingen en annan process har uppdaterat entiteten sedan klienten senast hämtade den eller att ETag-värdet som angavs i begäran är felaktigt.
    *  Om ETag-värdena är desamma eller **om-matchning-huvudet** innehåller jokertecknet asterisk (`*`) utför API:et den begärda åtgärden. API-åtgärden uppdaterar också det lagrade ETag-värdet för entiteten.


> [!NOTE]
> Ange jokertecknet (*) i **If-Match-huvudet** resulterar i API:et med hjälp av strategin Senaste en-vinner samtidighet. I det här fallet sker inte ETag-jämförelsen och resursen uppdateras utan några kontroller. 
