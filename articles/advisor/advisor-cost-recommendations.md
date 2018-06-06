---
title: Azure kostnaden för Advisor-rekommendationer | Microsoft Docs
description: Använd Azure Advisor för att optimera kostnader för Azure-distributioner.
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: ade6ef996c00c0c06d5b8e44815520e6e4ab7e9f
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34735875"
---
# <a name="advisor-cost-recommendations"></a>Kostnad Advisor-rekommendationer

Advisor hjälper dig att optimera och minska din övergripande Azure tillbringar med att identifiera inaktiv och underutnyttjade resurser. Du kan hämta cost rekommendationerna från den **kostnaden** på Advisor-instrumentpanelen.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Optimera virtuella tillbringar genom att ändra storlek på eller stänga av underutnyttjade instanser 
Även om vissa Programscenarier kan resultera i lågt utnyttjande av design, kan du ofta spara pengar genom att hantera storlek och antalet virtuella datorer. Advisor övervakar din användning av virtuella datorer på 14 dagar och identifierar låg belastning virtuella datorer. Virtuella datorer vars CPU-användning är 5 procent eller mindre och nätverksanvändning är 7 MB eller mindre för fyra eller flera dagar betraktas som låg belastning virtuella datorer.

Advisor visar de uppskattade kostnaderna för fortsätter att köra den virtuella datorn så att du kan välja att stänga av eller ändra storlek på den.

Om du vill ha mer aggressivt på identifiera underutnyttjade virtuella datorer, kan du justera regeln Genomsnittlig CPU-användning på grundval av per prenumeration.

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>Minska kostnaderna genom att eliminera avetableras ExpressRoute-kretsar
Advisor identifierar ExpressRoute-kretsar som har statusen providern av *inte etablerats* för mer än en månad och rekommenderar att du tar bort kretsen om du inte planerar att etablera kretsen med anslutningen providern.

## <a name="buy-virtual-machine-reserved-instances-to-save-money-over-pay-as-you-go-costs"></a>Köp reserverade instanser av virtuella datorer och spara pengar jämfört med Betala per användning
Advisor granskar din användning av virtuella datorer under de senaste 30 dagarna och avgöra om du kan spara pengar genom att köpa reserverade instanser. Advisor visas regioner och storlekar där du eventuellt har de flesta besparingar och visar de uppskattade besparingarna från hur du köper reserverade instanser. 

Du kan före köpa grundläggande kostnaderna för dina virtuella datorer med reserverade instanser. Rabatter kopplas automatiskt till nya eller befintliga virtuella datorer som har samma storlek och region som din reserverade instanser. [Läs mer om Azure reserverade VM-instanser.](https://azure.microsoft.com/pricing/reserved-vm-instances/)

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
