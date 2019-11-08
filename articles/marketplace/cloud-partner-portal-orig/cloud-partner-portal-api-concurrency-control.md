---
title: Concurrency-kontroll | Azure Marketplace
description: 'Samtidiga kontroll strategier för API: erna för Cloud Partner Portal Publishing.'
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 6e2f8922d42e40d14338f06be983d3913b20859d
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819748"
---
# <a name="concurrency-control"></a>Concurrency-kontroll

Varje anrop till API: erna för Cloud Partner Portal publicering måste uttryckligen ange vilken strategi för concurrency-kontroll som ska användas. Om du inte kan ange **If-Match-** huvudet uppstår ett HTTP 400-felsvar. Vi erbjuder två strategier för samtidiga kontroller.

-   **Optimistiskt** – klienten som utför uppdateringen verifieras om data har ändrats sedan den senast läste data.
-   **Senaste en WINS** – klienten uppdaterar data direkt, oavsett om det har ändrats av ett annat program sedan den senaste läsnings tiden.

<a name="optimistic-concurrency-workflow"></a>Optimistiskt concurrency-arbetsflöde
-------------------------------

Vi rekommenderar att du använder en optimistisk samtidighets strategi, med följande arbets flöde, för att garantera att inga oväntade ändringar görs i dina resurser.

1.  Hämta en entitet med hjälp av API: erna. Svaret innehåller ett ETag-värde som identifierar den aktuella lagrade versionen av entiteten (vid tidpunkten för svaret).
2.  Vid tidpunkten för uppdateringen inkluderar du samma ETag-värde i det obligatoriska **If-Match-** huvudet för begäran.
3.  API: n jämför det ETag-värde som togs emot i begäran med det aktuella ETag-värdet för entiteten i en atomisk transaktion.
    *   Om ETag-värdena skiljer sig, returnerar API: et ett `412 Precondition Failed` HTTP-svar. Det här felet anger att antingen en annan process har uppdaterat entiteten sedan klienten senast hämtade den eller att ETag-värdet som angavs i begäran är felaktigt.
    *  Om ETag-värdena är identiska, eller **om-match-** huvudet innehåller jokertecknet asterisk (`*`), utför API: et den begärda åtgärden. API-åtgärden uppdaterar även det lagrade ETag-värdet för entiteten.


> [!NOTE]
> Om du anger jokertecken (*) i rubriken **If-Match** i API: et används den sista engångs-WINS-strategin. I detta fall uppstår inte ETag-jämförelsen och resursen uppdateras utan några kontroller. 
