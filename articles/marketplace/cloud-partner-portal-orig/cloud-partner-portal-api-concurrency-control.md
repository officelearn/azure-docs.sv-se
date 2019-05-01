---
title: Samtidighetskontroll | Azure Marketplace
description: 'Samtidighet kontroll strategier för Cloud Partner Portal som publicera API: er.'
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 8cdcfd84a2f3bd4f920b97392255237db173cbf9
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64935601"
---
# <a name="concurrency-control"></a>Samtidighetskontroller

För varje anrop till Cloud Partner Portal publicera API: er måste uttryckligen ange vilken samtidighetsstrategi för att använda. Det gick inte att ange den **If-Match** rubrik resulterar i ett felsvar HTTP 400. Vi erbjuder två olika metoder för samtidighetskontroll.

-   **Optimistisk** -verifierar klienten utför uppdateringen om data har ändrats sedan den lästes senast data.
-   **Wins den sista** -klienten uppdaterar direkt data, oavsett om ett annat program den har ändrats sedan senast lästes tid.

<a name="optimistic-concurrency-workflow"></a>Optimistisk samtidighet arbetsflöde
-------------------------------

Vi rekommenderar att du använder strategi med Optimistisk samtidighet med följande arbetsflöde för att garantera att inga oväntade ändringar görs i dina resurser.

1.  Hämta en entitet med hjälp av API: erna. Svaret innehåller ett ETag-värde som anger den lagrade versionen av entiteten (vid tidpunkten för svaret).
2.  Vid tidpunkten för uppdatering, inkluderar du det här samma ETag-värdet i obligatoriska **If-Match** huvudet i begäran.
3.  API: et jämför ETag-värdet som tas emot i begäran med det aktuella ETag-värdet för entiteten i en atomisk transaktion.
    *   Om de ETag-värdena är olika, API: et returnerar en `412 Precondition Failed` HTTP-svar. Det här felet indikerar att antingen en annan process har uppdaterats entiteten eftersom klienten hämtade den senast eller att ETag-värdet som anges i begäran är felaktig.
    *  Om ETag-värden är samma, eller **If-Match** huvudet innehåller jokertecknet asterisk (`*`), API: et utförs den begärda åtgärden. API-åtgärden dessutom uppdateras det lagrade ETag-värdet för entiteten.


> [!NOTE]
> Att ange jokertecknet (*) i den **If-Match** rubrik resulterar i API: et med samtidighetsstrategi senaste-en-wins. I det här fallet ETag jämförelsen genomförs inte och resursen uppdateras utan alla kontroller. 
