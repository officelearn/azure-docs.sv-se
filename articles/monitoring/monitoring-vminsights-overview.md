---
title: Vad är Azure Monitor för virtuella datorer (förhandsversion)? | Microsoft Docs
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
ms.date: 11/07/2018
ms.author: magoedte
ms.openlocfilehash: 8629c27d4a039a702da5f76170fc312dadccaf6a
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2018
ms.locfileid: "51612091"
---
# <a name="what-is-azure-monitor-for-vms-preview"></a>Vad är Azure Monitor för virtuella datorer (förhandsversion)?

Azure Monitor för virtuella datorer övervakar dina Azure-datorer (VM) och Azure-datorer anger i stor skala genom att analysera prestanda och hälsa för Windows och Linux-datorer, inklusive olika processer och sammankopplade beroenden på andra resurser och externa processer. Lösningen har stöd för övervakning av prestanda och programberoenden för virtuella datorer finns på plats eller en annan molnleverantör. Det innehåller tre viktiga funktioner för att leverera den här ingående information:

* Logiska komponenterna i Azure virtuella datorer som kör Windows och Linux-operativsystem mäts baserat på en uppsättning förkonfigurerade health-villkor och aviseringar när det utvärderade villkoret uppfylls.  
* Core prestandamått från processor, minne, disk och nätverkskort av gästoperativsystemet för virtuella datorer samlas in och visas i fördefinierade populära prestandadiagram.
* Beroendekarta visar de identifierade sammankopplade komponenterna med den virtuella datorn från flera resursgrupper och prenumerationer.  

Dessa funktioner är uppdelade i tre perspektiv:

* Hälsa
* Prestanda
* Karta

>[!NOTE]
>För närvarande är funktionen hälsotillstånd bara tillgängligt för virtuella Azure-datorer och VM-skalningsuppsättning uppsättningar. Prestanda och kartan stöder både virtuella Azure-datorer och virtuella datorer som finns i din miljö eller annan molnleverantör.
>

Integrering med Log Analytics levererar kraftfulla aggregering, filtrering och möjligheten att utföra trendanalys av data över tid. Omfattande övervakning för din arbetsbelastning uppnås inte fristående med Azure Monitor, Service Map eller Log Analytics.  

Du kan visa dessa data i kontexten för enskild virtuell dator från den virtuella datorn direkt eller med Azure Monitor får ett sammansatt vy över dina virtuella datorer baserat på följande perspektiv för varje funktion:

* Health - virtuella datorer som är relaterade till en resursgrupp
* Kartan och prestanda – virtuella datorer som konfigurerats för att rapportera till en specifik Log Analytics-arbetsyta

![Virtuell dator insikter perspektiv från portalen](./media/monitoring-vminsights-overview/vminsights-azmon-directvm-01.png)

DevOps kan effektivt leverera förutsägbar prestanda och tillgänglighet för viktiga program genom att identifiera kritiska operativsystemhändelser och flaskhalsar i prestanda, nätverksproblem, och förstå om ett problem beror på andra beroenden.  

## <a name="data-usage"></a>Dataanvändning 

Så snart som du integrera Azure Monitor för virtuella datorer, data som samlas in av dina virtuella datorer samlas in och lagras i Azure Monitor. Azure Monitor för virtuella datorer debiteras för data som matas in och kvar, antalet health-villkor som skapats av mått time series-övervakade sätt, regler, meddelanden som skickas, per priser på Azure Monitor [sidan med priser](https://azure.microsoft.com/pricing/details/monitor/)

Loggstorleken varierar beroende på sträng längder av räknare och kan öka med antalet logiska diskar och nätverkskort. Om du redan har en arbetsyta och samlar in dessa räknare kan det kommer inte att belasta duplicerade tillämpas. Om du redan använder Tjänstkarta, är den enda ändringen visas ytterligare data som skickas till Azure Monitor.

## <a name="next-steps"></a>Nästa steg
Granska [publicera Azure Monitor för virtuella datorer](monitoring-vminsights-onboard.md) att förstå kraven och metoder för att övervaka dina virtuella datorer.
