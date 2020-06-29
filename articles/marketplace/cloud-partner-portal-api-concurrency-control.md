---
title: Concurrency-kontroll – Azure Marketplace
description: 'Samtidiga kontroll strategier för API: erna för Cloud Partner Portal Publishing.'
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: dec18daea2f4f257e3bb21ee0477e3629b2e630b
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2020
ms.locfileid: "85516372"
---
# <a name="concurrency-control"></a>Concurrency-kontroll

> [!NOTE]
> Cloud Partner Portal API: er är integrerade med partner Center och fortsätter att fungera när dina erbjudanden har migrerats till Partner Center. I integrationen presenteras små ändringar. Granska ändringarna som anges i [Cloud Partner Portal API-referensen](./cloud-partner-portal-api-overview.md) för att se till att koden fortsätter att fungera efter migreringen till Partner Center.

Varje anrop till API: erna för Cloud Partner Portal publicering måste uttryckligen ange vilken strategi för concurrency-kontroll som ska användas. Om du inte kan ange **If-Match-** huvudet uppstår ett HTTP 400-felsvar. Vi erbjuder två strategier för samtidiga kontroller.

-   **Optimistiskt** – klienten som utför uppdateringen verifieras om data har ändrats sedan den senast läste data.
-   **Senaste en WINS** – klienten uppdaterar data direkt, oavsett om det har ändrats av ett annat program sedan den senaste läsnings tiden.

<a name="optimistic-concurrency-workflow"></a>Optimistiskt concurrency-arbetsflöde
-------------------------------

Vi rekommenderar att du använder en optimistisk samtidighets strategi, med följande arbets flöde, för att garantera att inga oväntade ändringar görs i dina resurser.

1.  Hämta en entitet med hjälp av API: erna. Svaret innehåller ett ETag-värde som identifierar den aktuella lagrade versionen av entiteten (vid tidpunkten för svaret).
2.  Vid tidpunkten för uppdateringen inkluderar du samma ETag-värde i det obligatoriska **If-Match-** huvudet för begäran.
3.  API: n jämför det ETag-värde som togs emot i begäran med det aktuella ETag-värdet för entiteten i en atomisk transaktion.
    *   Om ETag-värdena skiljer sig, returnerar API: et ett `412 Precondition Failed` http-svar. Det här felet anger att antingen en annan process har uppdaterat entiteten sedan klienten senast hämtade den eller att ETag-värdet som angavs i begäran är felaktigt.
    *  Om ETag-värdena är identiska, eller **om-match-** huvudet innehåller jokertecknet asterisk ( `*` ), utför API: et den begärda åtgärden. API-åtgärden uppdaterar även det lagrade ETag-värdet för entiteten.


> [!NOTE]
> Om du anger jokertecken (*) i rubriken **If-Match** i API: et används den sista engångs-WINS-strategin. I detta fall uppstår inte ETag-jämförelsen och resursen uppdateras utan några kontroller. 
