---
title: Vad är Azure Monitor för virtuella datorer (förhandsversion)? | Microsoft Docs
description: Översikt över Azure Monitor for VMs som övervakar hälso tillstånd och prestanda för virtuella Azure-datorer, förutom att automatiskt identifiera och mappa program komponenter och deras beroenden.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/14/2019
ms.openlocfilehash: 1dcce3ab9f975fcf5910c382df3489d5d4ed425a
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77670675"
---
# <a name="what-is-azure-monitor-for-vms-preview"></a>Vad är Azure Monitor för virtuella datorer (förhandsversion)?

Azure Monitor för virtuella datorer övervakar dina Azure-datorer (VM) och VM-skalningsuppsättningar i stor skala. Den analyserar prestanda och hälsotillstånd för dina virtuella datorer med Windows och Linux, och övervakar deras processer och beroenden på andra resurser och i externa processer. 

Den innehåller stöd för övervakning av prestanda-och program beroenden för virtuella datorer som finns lokalt eller i en annan moln leverantör. Följande viktiga funktioner ger djupgående insikt:

- **Fördefinierade prestanda diagram**: Visa kärn prestanda mått från det virtuella gäst operativ systemet.

- **Beroende karta**: visar de sammankopplade komponenterna med den virtuella datorn från olika resurs grupper och prenumerationer.  

>[!NOTE]
>Vi har nyligen [lanserat ändringar](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
) som vi gör i hälso funktionen baserat på feedback vi har fått från våra kunder med offentlig för hands version. Med tanke på antalet ändringar vi kommer att göra, kommer vi att sluta erbjuda hälso funktionen för nya kunder. Befintliga kunder kan fortsätta att använda hälso funktionen. Mer information finns i [vanliga frågor och svar om allmän tillgänglighet](vminsights-ga-release-faq.md).  

Integrering med Azure Monitor loggar ger kraftfull agg regering och filtrering och kan analysera data trender över tid. Sådan omfattande övervakning av arbets belastningar kan inte uppnås med Azure Monitor eller Tjänstkarta ensam rätt.  

Du kan visa dessa data i en enda virtuell dator från den virtuella datorn direkt, eller så kan du använda Azure Monitor för att leverera en sammanställd vy över dina virtuella datorer där vyn har stöd för Azure resurs kontext eller arbets ytans kontext läge. Mer information finns i [Översikt över åtkomst lägen](../platform/design-logs-deployment.md#access-mode).

![Virtuell dator insikter perspektiv i Azure portal](./media/vminsights-overview/vminsights-azmon-directvm.png)

Azure Monitor for VMs kan leverera förutsägbara prestanda och tillgänglighet för viktiga program. Den identifierar Flask halsar i prestanda och nätverks problem. Azure Monitor for VMs kan också hjälpa dig att förstå om ett problem är relaterat till andra beroenden.  

## <a name="data-usage"></a>Dataanvändning

När du distribuerar Azure Monitor för virtuella datorer är de data som samlas in av dina virtuella datorer samlas in och lagras i Azure Monitor. Prestanda-och beroende data som samlas in lagras i en Log Analytics-arbetsyta. Baserat på prissättningen som publiceras på [sidan Azure Monitor prissättning](https://azure.microsoft.com/pricing/details/monitor/)faktureras Azure Monitor for VMS för:

- De data som har samlats in och lagras.
- Varningsregler som skapas.
- Meddelanden som skickas. 

Logg storleken varierar med sträng längden för prestanda räknare och kan öka med antalet logiska diskar och nätverkskort som allokeras till den virtuella datorn. Om du redan har en arbetsyta och samlar in dessa räknare, tillämpas inga dubbla avgifter. Om du redan använder Tjänstkarta, är den enda ändringen visas ytterligare data som skickas till Azure Monitor.

## <a name="next-steps"></a>Nästa steg

Om du vill förstå de krav och metoder som hjälper dig att övervaka dina virtuella datorer kan du läsa [distribuera Azure Monitor for VMS](vminsights-enable-overview.md).
