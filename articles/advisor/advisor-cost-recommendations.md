---
title: "Azure kostnaden för Advisor-rekommendationer | Microsoft Docs"
description: "Använd Azure Advisor för att optimera kostnader för Azure-distributioner."
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: 7b9c7037271fabd67c1ada80420ad72c340e46bb
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2017
---
# <a name="advisor-cost-recommendations"></a>Kostnad Advisor-rekommendationer

Advisor hjälper dig att optimera och minska din övergripande Azure tillbringar med att identifiera inaktiv och underutnyttjade resurser. Du kan hämta cost rekommendationerna från den **kostnaden** på Advisor-instrumentpanelen.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Optimera virtuella tillbringar genom att ändra storlek på eller stänga av underutnyttjade instanser 
Även om vissa Programscenarier kan resultera i lågt utnyttjande av design, kan du ofta spara pengar genom att hantera storlek och antalet virtuella datorer. Advisor övervakar din användning av virtuella datorer på 14 dagar och identifierar låg belastning virtuella datorer. Virtuella datorer vars CPU-användning är 5 procent eller mindre och nätverksanvändning är 7 MB eller mindre för fyra eller flera dagar betraktas som låg belastning virtuella datorer.

Advisor visar de uppskattade kostnaderna för fortsätter att köra den virtuella datorn så att du kan välja att stänga av eller ändra storlek på den.

Om du vill ha mer aggressivt på identifiera underutnyttjade virtuella datorer, kan du justera regeln Genomsnittlig CPU-användning på grundval av per prenumeration.

## <a name="use-a-cost-effective-solution-to-manage-performance-goals-of-multiple-sql-databases"></a>Använda en kostnadseffektiv lösning för att hantera prestandamål av flera SQL-databaser
Advisor identifierar SQL server-instanser som kan dra nytta av att skapa elastiska databaspooler. Elastiska databaspooler ger en enkel och kostnadseffektiv lösning för att hantera prestandamål av flera databaser som har olika användningsmönster. Läs mer om Azure elastiska pooler [vad är en Azure elastisk pool?](https://azure.microsoft.com/en-us/documentation/articles/sql-database-elastic-pool/).

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>Minska kostnaderna genom att eliminera avetableras ExpressRoute-kretsar
Advisor identifierar ExpressRoute-kretsar som har statusen providern av *inte etablerats* för mer än en månad och rekommenderar att du tar bort kretsen om du inte planerar att etablera kretsen med anslutningen providern.

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Hur du kommer åt kostnaden rekommendationerna i Azure Advisor

1. Logga in på den [Azure-portalen](https://portal.azure.com), och sedan öppna [Advisor](https://aka.ms/azureadvisordashboard).

2.  Advisor-instrumentpanelen, klicka på den **kostnaden** fliken.

## <a name="next-steps"></a>Nästa steg

Mer information om Advisor-rekommendationer finns:
* [Introduktion till Advisor](advisor-overview.md)
* [Kom igång](advisor-get-started.md)
* [Advisor-rekommendationer](advisor-cost-recommendations.md)
* [Advisor-rekommendationer för hög tillgänglighet](advisor-cost-recommendations.md)
* [Advisor säkerhetsrekommendationer](advisor-cost-recommendations.md)
