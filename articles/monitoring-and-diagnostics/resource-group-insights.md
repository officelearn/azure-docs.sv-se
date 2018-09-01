---
title: Azure Monitor-resursgrupp insikter | Microsoft Docs
description: Förstå hälsotillstånd och prestanda för dina distribuerade program och tjänster på resursgruppsnivå med Azure Monitor
services: azure-monitor
author: NumberByColors
manager: carmonm
ms.service: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: conceptual
ms.date: 08/29/2018
ms.reviewer: mbullwin
ms.author: daviste
ms.openlocfilehash: 723006d37ed0570e32790a0bb70a3dce5a87ade8
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/31/2018
ms.locfileid: "43345090"
---
# <a name="monitor-resource-groups-with-azure-monitor-preview"></a>Övervaka resursgrupper med Azure Monitor (förhandsversion)

Moderna program är ofta komplexa och högdistribuerade med diskreta delar som fungerar tillsammans för att leverera en tjänst. Eftersom den här komplexiteten tillhandahåller Azure Monitor övervakning insikter för resursgrupper. Detta gör det enkelt att sortera och diagnostisera problem uppstå din enskilda resurser och erbjuder sammanhang vad gäller hälsotillstånd och prestanda för resursgruppen&mdash;och ditt program&mdash;som helhet.

## <a name="access-insights-for-resource-groups"></a>Få användbar information för resursgrupper

1. Välj **resursgrupper** från vänster navigeringsfält.
2. Välj en av dina resursgrupper som du vill utforska. (Om du har ett stort antal resursgrupper filtrering av prenumerationen kan ibland vara till hjälp.)
3. Om du vill få fram användbar information för en resursgrupp, klickar du på **Insights** i menyn till vänster på valfri resursgrupp.

![Skärmbild av insikter översikt översiktssidan med resursgrupper](.\media\resource-group-insights\0001-overview.png)

## <a name="resources-with-active-alerts-and-health-issues"></a>Resurser med aktiva aviseringar och hälsoproblem

På översiktssidan visas hur många aviseringar bränts och fortfarande är aktiva, tillsammans med den aktuella Azure Resource Health för varje resurs. Tillsammans, den här informationen kan hjälpa dig att snabbt upptäcka alla resurser som har problem. Aviseringar hjälpa dig att identifiera problem i din kod och hur du har konfigurerat din infrastruktur. Azure Resource Health ytor-problem med själva Azure-plattformen, som inte är specifika för dina enskilda program.

![Skärmbild av Azure Resource Health-fönstret](.\media\resource-group-insights\0002-overview.png)

### <a name="azure-resource-health"></a>Azure Resource Health

Om du vill visa Azure Resource Health, kontrollera den **visa Azure Resource Health** kryssrutan ovanför tabellen. Den här kolumnen är dold som standard för att laddas den snabbt.

![Skärmbild med resource health graph har lagts till](.\media\resource-group-insights\0003-overview.png)

Som standard är resurserna som grupperade efter appnivån och resurstypen. **Appnivån** är en enkel kategorisering av resurstyper, som finns bara inom ramen för resource group insights-översiktssidan. Resurstyper som rör programkod, compute infrastruktur, nätverk, lagring och databaser. Hanteringsverktyg hämta sina egna app-lager och alla andra resurser är kategoriserade som tillhör den **andra** appnivån. Den här grupperingen kan du snabbt se med ett ögonkast vilka delsystem i ditt program är felfria och defekta.

## <a name="diagnose-issues-in-your-resource-group"></a>Diagnostisera problem i din resursgrupp

På sidan med resursgrupper insights tillhandahåller flera andra verktyg som är avsett att hjälpa dig att diagnostisera problem

   |         |          |
   | ---------------- |:-----|
   | [**Aviseringar**](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)      |  Visa, skapa och hantera dina aviseringar. |
   | [**Mått**](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) | Visualisera och utforska dina mått utifrån data.    |
   | [**Aktivitetsloggar**](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) | Prenumeration på händelser som har inträffat i Azure.  |
   | [**Programkarta**](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) | Går det distribuerade programmet topologin för att identifiera flaskhalsar i prestanda eller fel hotspots. |

## <a name="failures-and-performance"></a>Fel och prestanda

Vad händer om du har lagt märke till ditt program är långsam eller användare som har rapporterat fel? Det tar tid att söka igenom alla dina resurser för att isolera problem.

Den **prestanda** och **fel** flikar förenklar processen genom att föra samman prestanda och fel diagnostiska vyer för många vanliga typer av resurser.

De flesta typer av resurser gör det öppnas ett galleri för Azure Monitor arbetsboksmallar. Varje arbetsbok som du skapar kan anpassas sparade, delade med ditt team och återanvända i framtiden för att diagnostisera liknande problem.

### <a name="investigate-failures"></a>Undersök fel

Att testa flikväljaren fel **fel** under **Undersök** på den vänstra menyn.

Vänster menyraden ändras efter att ditt val har gjorts, erbjuder dig nya alternativ.

![Skärmbild av felet översiktsfönstret](.\media\resource-group-insights\00004-failures.png)

När App Service är valt, visas en galleriet i Azure Monitor arbetsboksmallar.

![Skärmbild av galleriet för arbetsboken](.\media\resource-group-insights\0005-failure-insights-workbook.png)

Välja mallen för fel Insights öppnas arbetsboken.

![Skärmbild av rapport om misslyckade](.\media\resource-group-insights\0006-failure-visual.png)

Du kan välja någon av raderna. Markeringen visas sedan i en grafisk detaljer.

![Skärmbild av information om felet](.\media\resource-group-insights\0007-failure-details.png)

Arbetsböcker abstrahera bort svårt arbetet med att skapa anpassade rapporter och visualiseringar i ett lättanvänt format. Även om vissa användare kan bara vill justera parametrarna fördefinierade, är arbetsböcker helt anpassningsbar.

Välj för att få en uppfattning om hur den här arbetsboken fungerar internt **redigera** i det översta fältet.

![Skärmbild av ytterligare redigera alternativet](.\media\resource-group-insights\0008-failure-edit.png)

Ett antal **redigera** rutor visas nära de olika elementen i arbetsboken. Välj den **redigera** rutan under tabellen av åtgärder.

![Skärmbild av redigeringsrutor](.\media\resource-group-insights\0009-failure-edit-graph.png)

Detta visar den underliggande Log Analytics-frågan som driver tabellvisualiseringen.

 ![Skärmbild av frågefönstret för log analytics](.\media\resource-group-insights\0010-failure-edit-query.png)

Du kan ändra frågan direkt. Eller du kan använda den som en referens och lånar från den när du utformar dina egna anpassade parametriserade arbetsboken.

### <a name="investigate-performance"></a>Undersök prestanda

Prestanda erbjuder en egen galleri med arbetsböcker. För App Service erbjuder färdiga programprestanda arbetsboken följande vy:

 ![Skärmbild av prestandavy](.\media\resource-group-insights\0011-performance.png)

I det här fallet om du väljer Redigera visas att den här uppsättningen visuella effekter drivs av Azure Monitor Metrics.

 ![Skärmbild av prestandavy med Azure-mått](.\media\resource-group-insights\0012-performance-metrics.png)

## <a name="next-steps"></a>Nästa steg

- [Azure Monitor-arbetsböcker](https://docs.microsoft.com/azure/application-insights/app-insights-usage-workbooks)
- [Azure Resource Health](https://docs.microsoft.com/azure/service-health/resource-health-overview)
- [Azure Monitor-aviseringar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)
