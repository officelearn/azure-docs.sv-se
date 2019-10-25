---
title: Vad är Azure Monitor for VMs (för hands version)? | Microsoft Docs
description: Azure Monitor for VMs är en funktion i Azure Monitor som kombinerar hälso-och prestanda övervakning av operativ systemet på den virtuella Azure-datorn samt identifierar program komponenter och beroenden automatiskt med andra resurser och mappar kommunikationen mellan dem. Den här artikeln innehåller en översikt.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/15/2019
ms.openlocfilehash: c6135f3ab90a2002c3cf0c8d26211d66d0c637e8
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802400"
---
# <a name="what-is-azure-monitor-for-vms-preview"></a>Vad är Azure Monitor for VMs (för hands version)?

Azure Monitor for VMs övervakar dina virtuella Azure-datorer (VM) och skalnings uppsättningar för virtuella datorer i stor skala. Den analyserar prestanda och hälsotillstånd för dina virtuella datorer med Windows och Linux, och övervakar deras processer och beroenden på andra resurser och i externa processer. 

Den innehåller stöd för övervakning av prestanda-och program beroenden för virtuella datorer som finns lokalt eller i en annan moln leverantör. Tre viktiga funktioner ger djupgående insikt:

- **Logiska komponenter för virtuella Azure-datorer och skalnings uppsättningar för virtuella datorer som kör Windows och Linux**: mäts mot förkonfigurerade hälso kriterier och varnar dig när det utvärderade villkoret är uppfyllt.  

- **Fördefinierade prestanda diagram**: Visa kärn prestanda mått från det virtuella gäst operativ systemet.

- **Beroende karta**: visar de sammankopplade komponenterna med den virtuella datorn från olika resurs grupper och prenumerationer.  

Funktionerna är organiserade i tre perspektiv:

- Hälso- och sjukvård
- Prestanda
- Karta

>[!NOTE]
>Vi har nyligen [lanserat ändringar](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
) vi gör ändringar i hälso funktionen baserat på feedback som vi har fått från våra kunder med offentlig för hands version. Med tanke på antalet ändringar vi kommer att göra, kommer vi att sluta erbjuda hälso funktionen för nya kunder. Befintliga kunder kan fortsätta att använda hälso funktionen. Mer information finns i [vanliga frågor och svar om allmän tillgänglighet](vminsights-ga-release-faq.md).  

Integrering med Azure Monitor loggar ger kraftfull agg regering och filtrering och kan analysera data trender över tid. Sådan omfattande övervakning av arbets belastningar kan inte uppnås med Azure Monitor eller Tjänstkarta ensam rätt.  

Du kan visa dessa data i en enda virtuell dator från den virtuella datorn direkt, eller så kan du använda Azure Monitor för att leverera en sammanställd vy av dina virtuella datorer. Den här vyn baseras på varje funktions perspektiv:

- **Hälso tillstånd**: de virtuella datorerna är relaterade till en resurs grupp.
- **Kart** -och **prestanda**: de virtuella datorerna är konfigurerade att rapportera till en speciell Log Analytics-arbetsyta.

![Den virtuella datorns insikter perspektiv i Azure Portal](./media/vminsights-overview/vminsights-azmon-directvm-01.png)

Azure Monitor for VMs kan leverera förutsägbara prestanda och tillgänglighet för viktiga program. Den identifierar kritiska operativ Systems händelser, prestanda Flask halsar och nätverks problem. Azure Monitor for VMs kan också hjälpa dig att förstå om ett problem är relaterat till andra beroenden.  

## <a name="data-usage"></a>Data användning

När du distribuerar Azure Monitor for VMs matas de data som samlas in av dina virtuella datorer in och lagras i Azure Monitor. Hälso villkors mått lagras i Azure Monitor i en databas för tids serier, och prestanda-och beroende data som samlas in lagras i en Log Analytics arbets yta. Baserat på prissättningen som publiceras på [sidan Azure Monitor prissättning](https://azure.microsoft.com/pricing/details/monitor/)faktureras Azure Monitor for VMS för:

- De data som matas in och lagras.
- Antalet hälso villkor som ska övervakas.
- De aviserings regler som skapas.
- De meddelanden som skickas. 

Logg storleken varierar med sträng längden för prestanda räknare och kan öka med antalet logiska diskar och nätverkskort som allokeras till den virtuella datorn. Om du redan har en arbets yta och samlar in dessa räknare tillämpas inga dubbla avgifter. Om du redan använder Tjänstkarta, är den enda ändringen som visas de ytterligare anslutnings data som skickas till Azure Monitor.

## <a name="next-steps"></a>Nästa steg

Om du vill förstå de krav och metoder som hjälper dig att övervaka dina virtuella datorer kan du läsa [distribuera Azure Monitor for VMS](vminsights-enable-overview.md).
