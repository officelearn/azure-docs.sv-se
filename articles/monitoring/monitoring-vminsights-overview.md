---
title: Vad är Azure Monitor för virtuella datorer? | Microsoft Docs
description: Azure Monitor för virtuella datorer är en funktion i Azure Monitor som kombinerar hälsotillstånd och prestanda för övervakning av Azure VM-operativsystem, samt automatiskt identifierar programkomponenter och beroenden till andra resurser och mappar kommunikationen varandra. Den här artikeln innehåller en översikt.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: magoedte
ms.openlocfilehash: 26fcc3eb78af53360cca57382b4c06b017f36c0e
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47063276"
---
# <a name="what-is-azure-monitor-for-vms"></a>Vad är Azure Monitor för virtuella datorer?

Azure Monitor för virtuella datorer övervakar dina Azure-datorer (VM) i stor skala genom att analysera prestanda och hälsa för Windows och Linux-datorer, inklusive olika processer och sammankopplade beroenden på andra resurser och externa processer. Lösningen har stöd för övervakning av prestanda och programberoenden för virtuella datorer finns på plats eller en annan molnleverantör.  Det innehåller tre viktiga funktioner för att leverera den här ingående information:

* Logiska komponenterna i Azure virtuella datorer som kör Windows och Linux-operativsystem mäts baserat på en uppsättning förkonfigurerade health-villkor och aviseringar när det utvärderade villkoret uppfylls.  
* Core prestandamått från processor, minne, disk och nätverkskort av gästoperativsystemet för virtuella datorer samlas in och visas i fördefinierade populära prestandadiagram.
* Beroendekarta visar de identifierade sammankopplade komponenterna med den virtuella datorn från flera resursgrupper och prenumerationer.  

Dessa funktioner är uppdelade i tre perspektiv:

* Hälsa
* Prestanda
* Karta

>[!NOTE]
>Funktionen hälsotillstånd är bara tillgängligt för Azure-datorer.
>

Integrering med Log Analytics levererar kraftfulla aggregering, filtrering och möjligheten att utföra trendanalys av data över tid. Omfattande övervakning för din arbetsbelastning uppnås inte fristående med Azure Monitor, Service Map eller Log Analytics.  

Du kan visa dessa data i kontexten för enskild virtuell dator från den virtuella datorn direkt eller med Azure Monitor får ett sammansatt vy över dina virtuella datorer baserat på följande perspektiv för varje funktion:

* Health - virtuella datorer som är relaterade till en resursgrupp
* Kartan och prestanda – virtuella datorer som konfigurerats för att rapportera till en specifik Log Analytics-arbetsyta

![Virtuell dator insikter perspektiv från portalen](./media/monitoring-vminsights-overview/vminsights-azmon-directvm-01.png)

DevOps kan effektivt leverera förutsägbar prestanda och tillgänglighet för viktiga program genom att identifiera kritiska operativsystemhändelser och flaskhalsar i prestanda, nätverksproblem, och förstå om ett problem beror på andra beroenden.  

## <a name="data-usage"></a>Dataanvändning 

Så snart som du integrera Azure Monitor för virtuella datorer, data som samlas in av dina virtuella datorer samlas in och lagras i Azure Monitor.  Azure Monitor för virtuella datorer debiteras för data som matas in och kvar, antalet health-villkor som skapats av mått time series-övervakade sätt, regler, meddelanden som skickas, per priser på Azure Monitor [sidan med priser](https://azure.microsoft.com/pricing/details/monitor/)

Loggstorleken varierar beroende på sträng längder av räknare och kan öka med antalet logiska diskar och nätverkskort.  Om du redan har en arbetsyta och samlar in dessa räknare kan det kommer inte att belasta duplicerade tillämpas.  Om du redan använder Tjänstkarta, är den enda ändringen visas ytterligare data som skickas till Azure Monitor.

## <a name="next-steps"></a>Nästa steg
Granska [publicera Azure Monitor för virtuella datorer](monitoring-vminsights-onboard.md) att förstå kraven och metoder för att övervaka dina virtuella datorer.
