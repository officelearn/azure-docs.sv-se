---
title: Insikter för Azure Monitor Resource Group | Microsoft-dokument
description: Förstå hälsotillståndet och prestanda för dina distribuerade program och tjänster på resursgruppsnivå med Azure Monitor
ms.subservice: ''
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 09/19/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 6d97e40bf2bf2298fb53609621db8ff2c6f1038f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663546"
---
# <a name="monitor-resource-groups-with-azure-monitor-preview"></a>Övervaka resursgrupper med Azure Monitor (förhandsversion)

Moderna applikationer är ofta komplexa och distribueras med många diskreta delar som arbetar tillsammans för att leverera en tjänst. Azure Monitor känner igen den här komplexiteten och ger övervakningsinsikter för resursgrupper. Detta gör det enkelt att triage och diagnostisera eventuella problem dina individuella resurser möter, samtidigt som sammanhang om hälsa och prestanda för resursgruppen&mdash;och ditt program&mdash;som helhet.

## <a name="access-insights-for-resource-groups"></a>Få tillgång till insikter för resursgrupper

1. Välj **Resursgrupper** i navigeringsfältet på vänster sida.
2. Välj en av dina resursgrupper som du vill utforska. (Om du har ett stort antal resursgrupper som filtrerar efter prenumeration kan det ibland vara till hjälp.)
3. Om du vill komma åt statistik för en resursgrupp klickar du på **Insikter** på menyn till vänster i en resursgrupp.

![Översiktssida för resursgruppsstatistik](./media/resource-group-insights/0001-overview.png)

## <a name="resources-with-active-alerts-and-health-issues"></a>Resurser med aktiva aviseringar och hälsoproblem

Översiktssidan visar hur många aviseringar som har avfyrats och fortfarande är aktiva, tillsammans med den aktuella Azure Resource Health för varje resurs. Tillsammans kan den här informationen hjälpa dig att snabbt hitta alla resurser som har problem. Aviseringar hjälper dig att identifiera problem i koden och hur du har konfigurerat infrastrukturen. Azure Resource Health ytor problem med Azure-plattformen själv, som inte är specifika för dina enskilda program.

![Skärmbild av hälsofönstret för Azure Resource](./media/resource-group-insights/0002-overview.png)

### <a name="azure-resource-health"></a>Azure Resource Health

Om du vill visa Azure Resource Health markerar du rutan **Visa Azure Resource Health** ovanför tabellen. Den här kolumnen är dold som standard för att hjälpa sidan att läsas in snabbt.

![Skärmbild med resurshälsodiagram tillagt](./media/resource-group-insights/0003-overview.png)

Som standard grupperas resurserna efter applager och resurstyp. **Applager** är en enkel kategorisering av resurstyper, som bara finns inom ramen för översiktssidan för resursgruppsinsikter. Det finns resurstyper relaterade till programkod, beräkningsinfrastruktur, nätverk, lagring + databaser. Hanteringsverktyg får sina egna applager och alla andra resurser kategoriseras som tillhörande det **andra** applagret. Den här gruppningen kan hjälpa dig att snabbt se vilka delsystem i ditt program som är felfria och ohälsosamma.

## <a name="diagnose-issues-in-your-resource-group"></a>Diagnostisera problem i resursgruppen

Sidan Resursgruppsstatistik innehåller flera andra verktyg som kan hjälpa dig att diagnostisera problem

   |         |          |
   | ---------------- |:-----|
   | [**Varningar**](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)      |  Visa, skapa och hantera aviseringar. |
   | [**Statistik**](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) | Visualisera och utforska dina måttbaserade data.    |
   | [**Aktivitetsloggar**](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) | Händelser på prenumerationsnivå som har inträffat i Azure.  |
   | [**Programkarta**](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) | Navigera i det distribuerade programmets topologi för att identifiera flaskhalsar eller felpunkter för prestanda. |

## <a name="failures-and-performance"></a>Fel och prestanda

Vad händer om du har märkt att ditt program körs långsamt eller om användarna har rapporterat fel? Det är tidskrävande att söka igenom alla dina resurser för att isolera problem.

Flikarna **Prestanda** och **fel förenklar** den här processen genom att sammanföra prestanda- och feldiagnostiska vyer för många vanliga resurstyper.

De flesta resurstyper öppnar ett galleri med Azure Monitor-arbetsboksmallar. Varje arbetsbok som du skapar kan anpassas, sparas, delas med ditt team och återanvändas i framtiden för att diagnostisera liknande problem.

### <a name="investigate-failures"></a>Undersöka fel

Om du vill testa fliken Fel väljer du **Fel** under **Undersök** i menyn till vänster.

Menyraden till vänster ändras när du har gjort ditt val och erbjuder nya alternativ.

![Skärmbild av översiktsfönstret Fel](./media/resource-group-insights/00004-failures.png)

När App Service väljs visas ett galleri med Azure Monitor-arbetsboksmallar.

![Skärmbild av programarbetsboksgalleriet](./media/resource-group-insights/0005-failure-insights-workbook.png)

Om du väljer mallen för Felstatistik öppnas arbetsboken.

![Skärmbild av felrapport](./media/resource-group-insights/0006-failure-visual.png)

Du kan markera någon av raderna. Markeringen visas sedan i en grafisk detaljvy.

![Skärmbild av felinformation](./media/resource-group-insights/0007-failure-details.png)

Arbetsböcker abstraherar det svåra arbetet med att skapa anpassade rapporter och visualiseringar till ett enkelt förbrukningsbart format. Medan vissa användare kanske bara vill justera de fördefinierade parametrarna, är arbetsböcker helt anpassningsbara.

Om du vill få en uppfattning om hur arbetsboken fungerar internt väljer du **Redigera** i det övre fältet.

![Skärmbild av ytterligare redigeringsalternativ](./media/resource-group-insights/0008-failure-edit.png)

Ett antal **redigeringsrutor** visas i närheten av arbetsbokens olika element. Markera rutan **Redigera** under operationssagen.

![Skärmbild av redigeringsrutor](./media/resource-group-insights/0009-failure-edit-graph.png)

Detta visar den underliggande loggfrågan som driver tabellvisualiseringen.

 ![Skärmbild av loggfrågefönstret](./media/resource-group-insights/0010-failure-edit-query.png)

Du kan ändra frågan direkt. Du kan också använda den som referens och låna av den när du utformar en egen anpassad parameteriserad arbetsbok.

### <a name="investigate-performance"></a>Undersök prestanda

Performance erbjuder ett eget galleri med arbetsböcker. För App Service erbjuder den fördefinierade programprestandaarbetsboken följande vy:

 ![Skärmbild av prestandavyn](./media/resource-group-insights/0011-performance.png)

I det här fallet, om du väljer redigera ser du att den här uppsättningen visualiseringar drivs av Azure Monitor Metrics.

 ![Skärmbild av prestandavyn med Azure-mått](./media/resource-group-insights/0012-performance-metrics.png)

## <a name="troubleshooting"></a>Felsökning

### <a name="enabling-access-to-alerts"></a>Aktivera åtkomst till aviseringar

Om du vill se aviseringar i Azure Monitor för resursgrupper måste någon med en ägar- eller deltagarroll för den här prenumerationen öppna Azure Monitor för resursgrupper för alla resursgrupper i prenumerationen. Detta gör det möjligt för alla med läsbehörighet att se aviseringar i Azure Monitor för resursgrupper för alla resursgrupper i prenumerationen. Om du har en ägar- eller deltagarroll uppdaterar du den här sidan om några minuter.

Azure Monitor för resursgrupper förlitar sig på Azure Monitor Alerts Management-systemet för att hämta aviseringsstatus. Hantering av aviseringar är inte konfigurerad för varje resursgrupp och prenumeration som standard, och den kan bara aktiveras av någon med en ägar- eller deltagarroll. Det kan aktiveras antingen genom att:
* Öppna Azure Monitor för resursgrupper för alla resursgrupper i prenumerationen.
* Eller genom att gå till prenumerationen, klicka på **Resursleverantörer**och sedan klicka **på Registrera för aviseringar.Management**.

## <a name="next-steps"></a>Nästa steg

- [Azure Monitor-arbetsböcker](https://docs.microsoft.com/azure/application-insights/app-insights-usage-workbooks)
- [Azure Resource Health](https://docs.microsoft.com/azure/service-health/resource-health-overview)
- [Azure-övervakarevarningar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)
