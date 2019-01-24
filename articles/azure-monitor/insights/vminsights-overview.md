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
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2019
ms.author: magoedte
ms.openlocfilehash: 0733b4179793e88759ca1c3330880546650fbe09
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54856391"
---
# <a name="what-is-azure-monitor-for-vms-preview"></a>Vad är Azure Monitor för virtuella datorer (förhandsversion)?

Azure Monitor för virtuella datorer övervakar dina Azure-datorer (VM) och VM-skalningsuppsättningar i stor skala. Tjänsten analyserar prestanda och hälsa för Windows och Linux-datorer, övervaka sina processer och deras beroenden till andra resurser och externa processer. 

Som en lösning för har Azure Monitor för virtuella datorer stöd för övervakning av prestanda och programberoenden för virtuella datorer som hanteras lokalt eller i en annan molnleverantör. Tre huvudfunktioner ger djupgående insikter:

* **Logiska komponenterna i Azure virtuella datorer som kör Windows och Linux**: Mäts mot förkonfigurerade health-villkor, och de varnar dig när det utvärderade villkoret uppfylls.  

* **Fördefinierade, trender prestandadiagram**: Visa core prestandamått från gästoperativsystemet för virtuell dator.

* **Beroendekarta**: Visar komponenterna som är sammankopplade med den virtuella datorn från olika resursgrupper och prenumerationer.  

Funktionerna är uppdelade i tre perspektiv:

* Hälsa
* Prestanda
* Karta

>[!NOTE]
>För närvarande kan erbjuds funktionen hälsotillstånd endast för virtuella Azure-datorer och VM-skalningsuppsättning uppsättningar. Funktioner för prestanda och kartan har stöd för både virtuella Azure-datorer och virtuella datorer som finns i din miljö eller annan molnleverantör.

Integrering med Log Analytics ger kraftfulla aggregering och filtrering och den kan analysera datatrender över tid. Sådana omfattande arbetsbelastningsövervakning uppnås inte med Azure Monitor, Service Map eller Log Analytics enbart.  

Du kan visa dessa data i en enda virtuell dator från den virtuella datorn direkt eller du kan använda Azure Monitor för att leverera en sammansatt vy av dina virtuella datorer. Den här vyn är baserad på varje funktion perspektiv:

* **Hälsotillstånd**: De virtuella datorerna är relaterade till en resursgrupp.
* **Kartan** och **prestanda**: De virtuella datorerna konfigureras att rapportera till en viss Log Analytics-arbetsyta.

![Virtuell dator insikter perspektiv i Azure portal](./media/vminsights-overview/vminsights-azmon-directvm-01.png)

Azure Monitor för virtuella datorer kan leverera förutsägbar prestanda och tillgänglighet för viktiga program. Den identifierar kritiska operativsystemhändelser flaskhalsar i prestanda och nätverksproblem. Azure Monitor för virtuella datorer kan också hjälpa dig att förstå om ett problem är relaterat till andra beroenden.  

## <a name="data-usage"></a>Dataanvändning 

När du distribuerar Azure Monitor för virtuella datorer är de data som samlas in av dina virtuella datorer samlas in och lagras i Azure Monitor. Baserat på priserna på den [Azure Monitor sidan med priser](https://azure.microsoft.com/pricing/details/monitor/), Azure Monitor för virtuella datorer debiteras för:
* De data som har samlats in och lagras.
* Antal hälsotillstånd kriterier mått tidsserier som övervakas.
* Varningsregler som skapas.
* Meddelanden som skickas. 

Loggstorleken varierar beroende på sträng längder av räknare och det kan öka med antalet logiska diskar och nätverkskort. Om du redan har en arbetsyta och samlar in dessa räknare, tillämpas inga dubbla avgifter. Om du redan använder Tjänstkarta, är den enda ändringen visas ytterligare data som skickas till Azure Monitor.

## <a name="next-steps"></a>Nästa steg
För att förstå de krav och metoder som hjälper dig att övervaka dina virtuella datorer, granska [distribuera Azure Monitor för virtuella datorer](vminsights-onboard.md).
