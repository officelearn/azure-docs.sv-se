---
title: Övervaka användning och uppskattade kostnader i Azure Monitor
description: Översikt över processen med att använda Azure Monitor-användning och uppskattade kostnader
author: dalekoetke
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: mbullwin
ms.reviewer: Dale.Koetke
ms.subservice: ''
ms.openlocfilehash: 787618b59cd18dd4c38892ddf0861808211671cb
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936628"
---
# <a name="monitoring-usage-and-estimated-costs-in-azure-monitor"></a>Övervaka användning och uppskattade kostnader i Azure Monitor

> [!NOTE]
> Den här artikeln beskriver hur du visar användning och uppskattade kostnader i flera Azure-övervakningsfunktioner för olika pris modeller. Relaterade artiklar för vissa komponenter i Azure Monitor är:
> - [Hantera användning och kostnader med Azure Monitor loggar](manage-cost-storage.md) beskriver hur du styr dina kostnader genom att ändra din data lagrings period och hur du analyserar och varnar för din data användning.
> - [Hantera användning och kostnader för Application Insights](../../azure-monitor/app/pricing.md) beskriver hur du analyserar data användningen i Application Insights.

I övervakaren hub i Azure Portal i **användning och uppskattade kostnader** sidan förklaras användningen av grundläggande övervakningsfunktioner som [aviseringar, mått, meddelanden](https://azure.microsoft.com/pricing/details/monitor/), [Azure Log Analytics ](https://azure.microsoft.com/pricing/details/log-analytics/), och [Azure Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). För kunder på priser avtal innan April 2018, omfattar detta även logganalys-användning som köpts via insikterna och Analytics erbjuder.

På den här sidan kan användare visa sina resursanvändningen för de senaste 31 dagarna, aggregerade per prenumeration. Drill-moduler visar trender för användning under perioden på 31 dagar. Stora mängder data behöver samlas för den här beräkningen så ha tålamod när sidan läses in.

Det här exemplet visar övervakad användning och en uppskattning av de resulterande kostnaderna:

![Användning och uppskattade kostnader portal skärmbild](./media/usage-estimated-costs/001.png)

Välj länken i kolumnen månatlig användning för att öppna ett diagram som visar trender för användning under de senaste 31 dagarna:

![Ingår per nod liggande diagram skärmbild](./media/usage-estimated-costs/002.png)

Här är en annan liknande användning och kostnadssammanfattning. Det här exemplet visar en prenumeration i den nya prissättningsmodellen från April 2018 förbrukningsbaserad. Observera bristen på valfri nod-baserade fakturering. Datainmatning och kvarhållning för Log Analytics och Application Insights rapporteras nu på en ny mätare som vanligt.

![Användning och uppskattade kostnader portal skärmbild – April 2018 priser](./media/usage-estimated-costs/003.png)

## <a name="pricing-model"></a>Prismodell

I April 2018 en [nya övervakning prismodellen släpptes](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/).  Den här funktionen har molnbaserad, konsumtions-baserade priser ("betala per användning"). Du betalar bara för det du använder, utan åtaganden för nod-baserade. Mer information om den nya prismodellen finns tillgängliga för [aviseringar, mått, meddelanden](https://azure.microsoft.com/pricing/details/monitor/), [Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) och [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). 

Förutom modellen betala per användning i september 2019 lade vi till kapacitets reservationer för Log Analytics som gör att du kan spara så mycket som 25% jämfört med priset betala per användning. Med kapacitets reservations priset kan du köpa en reservation som börjar på 100 GB/dag. All användning ovanför reservations nivån debiteras enligt priset för betala per användning. [Läs mer](https://azure.microsoft.com/pricing/details/monitor/) om priser för kapacitets reservationer.

För kunder Kom igång med Log Analytics eller Application Insights efter 2 April 2018 är det enda alternativet i den nya prismodellen. För kunder som redan använder dessa tjänster kan flyttas till den nya prismodellen är valfritt.

## <a name="assessing-the-impact-of-the-new-pricing-model"></a>Utvärdera effekten av den nya prismodellen
Den nya prismodellen har olika påverkan på varje kund baserat på deras övervakning användningsmönster. För kunder som använder Log Analytics eller Application Insights innan den 2 April 2018 den **användning och uppskattade kostnader** sida i Azure Monitor beräknar ändringar i kostnaderna om de flyttar till den nya prismodellen. Det ger sätt att flytta en prenumeration till den nya modellen. De flesta kunder är den nya prismodellen för stora fördelar. Det kanske inte är fallet för kunder med särskilt hög användningsmönster eller regioner för högre kostnad.

Se en uppskattning av dina kostnader för prenumerationerna som du har valt på den **användning och uppskattade kostnader** väljer du den blå banderollen längst upp på sidan. Det är bäst att göra det här en prenumeration i taget, eftersom det är den nivå där den nya prissättningsmodellen kan antas.

![Övervaka användning och uppskattade kostnader i nya prissättning modell-skärmbild](./media/usage-estimated-costs/004.png)

Den nya sidan visar en liknande version av den föregående sidan med en grön banderoll:

![Övervaka användning och uppskattade kostnader i aktuell prissättning modell-skärmbild](./media/usage-estimated-costs/005.png)

På sidan visas även en annan uppsättning mätare som motsvarar den nya prismodellen. Den här listan är ett exempel:

- Insight and Analytics\Overage per nod
- Insight and Analytics\Included per nod
- Programmet Insights\Basic Överförbrukade Data
- Insights\Included programdata

Den nya prismodellen har inte nod-baserade inkluderade data-allokeringar. Därför mätarnas datainmatning kombineras till en ny vanliga datainmatning mätare som kallas **delade Services\Data inmatning**. 

Det finns en annan ändring som matas in i Log Analytics eller Application Insights i regioner med högre kostnader. Data för dessa regioner för hög kostnad visas med de nya regionala mätarna. Ett exempel är **datainmatning (USA, västra centrala)** .

> [!NOTE]
> Den uppskattade kostnader inte ta med i kontonivå per nod rättigheter för Operations Management Suite (OMS)-prenumeration per prenumeration. Kontakta representanten för ditt konto för en mer detaljerad beskrivning av den nya prissättningsmodellen i det här fallet.

## <a name="new-pricing-model-and-operations-management-suite-subscription-entitlements"></a>Ny prisnivå modell och Operations Management Suite-prenumerationens rättigheter

Kunder som har köpt Microsoft Operations Management Suite E1 och E2 är berättigade till per nod datainmatning rättigheter för [Log Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite) och [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-pricing). Att ta emot dessa rättigheter för Log Analytics-arbetsytor och Application Insights-resurser i en viss prenumeration: 

- Prenumerationens prismodellen måste vara i pre-April 2018-modellen.
- Log Analytics-arbetsytor bör använda den ”Per nod (OMS)” prisnivå.
- Application Insights-resurser bör använda ”företaget”-prisplan.

Beroende på antalet noder i sviten som din organisation har köpt flytta några prenumerationer på den nya prissättningsmodellen kan vara fördelaktigt, men det kräver noggrann beräkningen. I allmänhet rekommenderas bara för att hålla dig i förväg April 2018 modellen enligt beskrivningen ovan.

> [!WARNING]
> Om din organisation har köpt Microsoft Operations Management Suite E1 och E2, är det oftast bäst att hålla dina prenumerationer i prissättningsmodellen för pre-April 2018. 
>

## <a name="changes-when-youre-moving-to-the-new-pricing-model"></a>Ändringar när du flyttar till den nya prismodellen

Den nya prismodellen förenklar Log Analytics och Application Insights prisalternativ till endast en enda nivå (eller plan). Flyttar en prenumeration till den nya prissättningen modellen kommer:

- Ändra prisnivån för varje Log Analytics till en ny Per GB-nivå (kallas ”pergb2018” i Azure Resource Manager)
- Alla Application Insights-resurser i företagsplanen ändras till Basic-avtal.

En översikt visar effekterna av dessa ändringar.

> [!WARNING]
> Här viktigt om du använder Azure Resource Manager eller PowerShell för att distribuera [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-template-workspace-configuration) eller [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-powershell) i en prenumeration som du har flyttat till den nya prismodellen. Om du anger en nivå/prisplanen än ”pergb2018” för Log Analytics eller ”grundläggande” för Application Insights, snarare än misslyckas distributionen på grund av att ange ett ogiltigt priser nivå /-planen, kommer att kunna **men använder den enda giltiga prisavtalnivå/** (Detta gäller inte för den Log Analytics kostnadsfria nivån där ett ogiltigt prisnivå nivå meddelande genereras).
>

## <a name="moving-to-the-new-pricing-model"></a>Flytta till den nya prismodellen

Om du har bestämt dig för att använda den nya pris modellen för en prenumeration går du till varje Application Insights resurs, öppnar **användnings-och uppskattade kostnader** och kontrollerar att det finns på den grundläggande pris nivån och går till varje Log Analytics arbets yta, öppnar var och en av  **Sidan pris nivå** och ändra till pris nivån **per GB (2018)** . 

> [!NOTE]
> Kravet att alla Application Insights resurser och Log Analytics arbets ytor inom en bestämd prenumeration antar den senaste pris sättnings modellen har tagits bort, vilket ger bättre flexsaldo och enklare konfiguration. 

## <a name="automate-moving-to-the-new-pricing-model"></a>Automatisera flyttas till den nya prismodellen

Som nämnts ovan är det inte längre något som krävs för att flytta alla övervaknings resurser i en prenumeration till den nya pris modellen samtidigt, och därför kommer ``migratetonewpricingmodel``-åtgärden inte längre att ha någon verkan. Nu kan du flytta Application Insights-resurser och Log Analytics arbets ytor separat till de senaste pris nivåerna.  

Automatisering av den här ändringen dokumenteras för Application Insights som använder [set-AzureRmApplicationInsightsPricingPlan](https://docs.microsoft.com/powershell/module/azurerm.applicationinsights/set-azurermapplicationinsightspricingplan) med ``-PricingPlan "Basic"`` och Log Analytics med [set-AzureRmOperationalInsightsWorkspace](https://docs.microsoft.com/powershell/module/AzureRM.OperationalInsights/Set-AzureRmOperationalInsightsWorkspace) med ``-sku "PerGB2018"``. 
