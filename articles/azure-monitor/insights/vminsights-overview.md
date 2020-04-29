---
title: Vad är Azure Monitor for VMs?
description: Översikt över Azure Monitor for VMs som övervakar hälso tillstånd och prestanda för virtuella Azure-datorer, förutom att automatiskt identifiera och mappa program komponenter och deras beroenden.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/11/2020
ms.openlocfilehash: 6f65ba96f768a4b88aa13c073cc66920b8c5e049
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79480495"
---
# <a name="what-is-azure-monitor-for-vms"></a>Vad är Azure Monitor for VMs?

Azure Monitor for VMs övervakar dina virtuella Azure-datorer (VM) och skalnings uppsättningar för virtuella datorer i stor skala. Den analyserar prestanda och hälsotillstånd för dina virtuella datorer med Windows och Linux, och övervakar deras processer och beroenden på andra resurser och i externa processer. Den innehåller stöd för övervakning av prestanda-och program beroenden för virtuella datorer som finns lokalt eller i en annan moln leverantör. Följande viktiga funktioner ger djupgående insikt:

- **Fördefinierade prestanda diagram**: Visa kärn prestanda mått från det virtuella gäst operativ systemet.

- **Beroende karta**: visar de sammankopplade komponenterna med den virtuella datorn från olika resurs grupper och prenumerationer.  

>[!NOTE]
>Vi har nyligen [lanserat ändringar](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
) som vi gör i hälso funktionen baserat på feedback vi har fått från våra kunder med offentlig för hands version. Med tanke på antalet ändringar vi kommer att göra, kommer vi att sluta erbjuda hälso funktionen för nya kunder. Befintliga kunder kan fortsätta att använda hälso funktionen. Mer information finns i [vanliga frågor och svar om allmän tillgänglighet](vminsights-ga-release-faq.md).  

Integrering med Azure Monitor loggar ger kraftfull agg regering och filtrering, vilket gör att Azure Monitor for VMs analysera data trender över tid. Du kan visa dessa data i en enda virtuell dator från den virtuella datorn direkt, eller så kan du använda Azure Monitor för att leverera en sammanställd vy över dina virtuella datorer där vyn har stöd för Azure resurs kontext eller arbets ytans kontext läge. Mer information finns i [Översikt över åtkomst lägen](../platform/design-logs-deployment.md#access-mode).

![Den virtuella datorns insikter perspektiv i Azure Portal](media/vminsights-overview/vminsights-azmon-directvm.png)

Azure Monitor for VMs kan leverera förutsägbara prestanda och tillgänglighet för viktiga program. Den identifierar Flask halsar och nätverks problem och kan också hjälpa dig att förstå om ett problem är relaterat till andra beroenden.  

## <a name="data-usage"></a>Dataanvändning

När du distribuerar Azure Monitor for VMs matas de data som samlas in av dina virtuella datorer in och lagras i Azure Monitor. Prestanda-och beroende data som samlas in lagras i en Log Analytics-arbetsyta. Baserat på prissättningen som publiceras på [sidan Azure Monitor prissättning](https://azure.microsoft.com/pricing/details/monitor/)faktureras Azure Monitor for VMS för:

- De data som matas in och lagras.
- De aviserings regler som skapas.
- De meddelanden som skickas. 

Logg storleken varierar med sträng längden för prestanda räknare och kan öka med antalet logiska diskar och nätverkskort som allokeras till den virtuella datorn. Om du redan har en arbets yta och samlar in dessa räknare tillämpas inga dubbla avgifter. Om du redan använder Tjänstkarta, är den enda ändringen som visas de ytterligare anslutnings data som skickas till Azure Monitor.

## <a name="next-steps"></a>Nästa steg

Om du vill förstå de krav och metoder som hjälper dig att övervaka dina virtuella datorer kan du läsa [distribuera Azure Monitor for VMS](vminsights-enable-overview.md).
