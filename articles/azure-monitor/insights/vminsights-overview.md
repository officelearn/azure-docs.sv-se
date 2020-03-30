---
title: Vad är Azure Monitor för virtuella datorer?
description: Översikt över Azure Monitor för virtuella datorer som övervakar hälso- och prestanda för virtuella Azure-datorer förutom att automatiskt identifiera och mappa programkomponenter och deras beroenden.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/11/2020
ms.openlocfilehash: 6f65ba96f768a4b88aa13c073cc66920b8c5e049
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480495"
---
# <a name="what-is-azure-monitor-for-vms"></a>Vad är Azure Monitor för virtuella datorer?

Azure Monitor för virtuella datorer övervakar dina Azure-virtuella datorer (VM) och skaluppsättningar för virtuella datorer i stor skala. Den analyserar prestanda och hälsotillstånd för dina virtuella datorer med Windows och Linux, och övervakar deras processer och beroenden på andra resurser och i externa processer. Den innehåller stöd för övervakning av prestanda och programberoenden för virtuella datorer som finns lokalt eller i en annan molnleverantör. Följande viktiga funktioner ger en djupgående insikt:

- **Fördefinierade trendprestandadiagram:** Visa kärnprestandamått från operativsystemet gäst-VM.

- **Beroendekarta**: Visar de sammankopplade komponenterna med den virtuella datorn från olika resursgrupper och prenumerationer.  

>[!NOTE]
>Vi [meddelade](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
) nyligen ändringar vi gör i hälsofunktionen baserat på den feedback vi har fått från våra offentliga förhandsversionskunder. Med tanke på antalet förändringar vi kommer att göra, kommer vi att sluta erbjuda hälsofunktionen för nya kunder. Befintliga kunder kan fortsätta att använda hälsofunktionen. För mer information, se vår [allmänna tillgänglighet FAQ](vminsights-ga-release-faq.md).  

Integrering med Azure Monitor Logs ger kraftfull aggregering och filtrering, vilket gör att Azure Monitor för virtuella datorer kan analysera datatrender över tid. Du kan visa dessa data i en enda virtuell dator direkt från den virtuella datorn, eller så kan du använda Azure Monitor för att leverera en aggregerad vy över dina virtuella datorer där vyn stöder Azure-resurskontext- eller arbetsytekontextlägen. Mer information finns i [översikt över åtkomstlägen](../platform/design-logs-deployment.md#access-mode).

![Insights-perspektiv för virtuella datorer i Azure-portalen](media/vminsights-overview/vminsights-azmon-directvm.png)

Azure Monitor för virtuella datorer kan leverera förutsägbara prestanda och tillgänglighet för viktiga program. Den identifierar flaskhalsar och nätverksproblem och kan också hjälpa dig att förstå om ett problem är relaterat till andra beroenden.  

## <a name="data-usage"></a>Dataanvändning

När du distribuerar Azure Monitor för virtuella datorer intas och lagras de data som samlas in av dina virtuella datorer i Azure Monitor. Prestanda- och beroendedata som samlas in lagras på en Log Analytics-arbetsyta. Baserat på de priser som publiceras på [prissidan för Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/)debiteras Azure Monitor för virtuella datorer för:

- De data som förtärs och lagras.
- Varningsreglerna som skapas.
- Meddelandena som skickas. 

Loggstorleken varierar beroende på stränglängderna för prestandaräknare och kan öka med antalet logiska diskar och nätverkskort som allokerats till den virtuella datorn. Om du redan har en arbetsyta och samlar in dessa räknare tillämpas inga dubblettavgifter. Om du redan använder Service Map är den enda ändringen du ser de ytterligare anslutningsdata som skickas till Azure Monitor.

## <a name="next-steps"></a>Nästa steg

Om du vill förstå de krav och metoder som hjälper dig att övervaka dina virtuella datorer läser du [Distribuera Azure Monitor för virtuella datorer](vminsights-enable-overview.md).
