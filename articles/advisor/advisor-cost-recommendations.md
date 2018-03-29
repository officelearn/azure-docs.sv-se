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
ms.openlocfilehash: 7a8807a580f1a7f1fe67e026a8fbd4cc0e96c41c
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="advisor-cost-recommendations"></a>Kostnad Advisor-rekommendationer

Advisor hjälper dig att optimera och minska din övergripande Azure tillbringar med att identifiera inaktiv och underutnyttjade resurser. Du kan hämta cost rekommendationerna från den **kostnaden** på Advisor-instrumentpanelen.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Optimera virtuella tillbringar genom att ändra storlek på eller stänga av underutnyttjade instanser 
Även om vissa Programscenarier kan resultera i lågt utnyttjande av design, kan du ofta spara pengar genom att hantera storlek och antalet virtuella datorer. Advisor övervakar din användning av virtuella datorer på 14 dagar och identifierar låg belastning virtuella datorer. Virtuella datorer vars CPU-användning är 5 procent eller mindre och nätverksanvändning är 7 MB eller mindre för fyra eller flera dagar betraktas som låg belastning virtuella datorer.

Advisor visar de uppskattade kostnaderna för fortsätter att köra den virtuella datorn så att du kan välja att stänga av eller ändra storlek på den.

Om du vill ha mer aggressivt på identifiera underutnyttjade virtuella datorer, kan du justera regeln Genomsnittlig CPU-användning på grundval av per prenumeration.

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
