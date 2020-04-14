---
title: Samtidighetskontroll | Azure Marketplace
description: Strategier för samtidighetskontroll för API:er för molnpartnerportalen.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 302ba8d550f5e91efe12c620d766550958d3bf68
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81256407"
---
# <a name="concurrency-control"></a>Samtidighetskontroll

> [!NOTE]
> Api:erna för Cloud Partner Portal är integrerade med Partner Center och fortsätter att fungera när dina erbjudanden har migrerats till Partner Center. Integrationen medför små förändringar. Granska ändringarna i [Cloud Partner Portal API Reference](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) för att säkerställa att koden fortsätter att fungera efter migreringen till Partner Center.

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
