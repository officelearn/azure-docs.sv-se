---
title: Översikt över aviseringar och meddelandeövervakning i Azure
description: Översikt över aviseringar i Azure. Aviseringar, klassiska aviseringar och varningsgränssnittet.
ms.subservice: alerts
ms.topic: conceptual
ms.date: 01/28/2018
ms.openlocfilehash: 7ca77531ed3e1fae8ec297e430597452c7512aea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274793"
---
# <a name="overview-of-alerts-in-microsoft-azure"></a>Översikt över aviseringar i Microsoft Azure 

I den här artikeln beskrivs vilka aviseringar som är, deras fördelar och hur du kommer igång med dem.  

## <a name="what-are-alerts-in-microsoft-azure"></a>Vad är aviseringar i Microsoft Azure?
Aviseringar meddelar dig proaktivt när viktiga villkor hittas i dina övervakningsdata. De gör att du kan identifiera och åtgärda problem innan användarna av ditt system märker dem. 

I den här artikeln beskrivs den enhetliga varningsupplevelsen i Azure Monitor, som innehåller aviseringar som tidigare hanterades av Log Analytics och Application Insights. Den [tidigare varningsupplevelsen](alerts-classic.overview.md) och varningstyperna kallas *klassiska aviseringar*. Du kan visa den här äldre upplevelsen och den äldre aviseringstypen genom att välja Visa klassiska aviseringar högst upp på **aviseringssidan.** 

## <a name="overview"></a>Översikt

Diagrammet nedan representerar flödet av aviseringar. 

![Diagram över varningsflödet](media/alerts-overview/Azure-Monitor-Alerts.svg)

Varningsregler separeras från aviseringar och de åtgärder som vidtas när en avisering utlöses. Varningsregeln samlar in målet och kriterierna för aviseringar. Varningsregeln kan vara i aktiverat eller inaktiverat tillstånd. Varningar avfyras bara när den är aktiverad. 

Följande är nyckelattribut för en varningsregel:

**Målresurs**: Definierar omfattning och signaler som är tillgängliga för aviseringar. Ett mål kan vara vilken Azure-resurs som helst. Exempelmål: en virtuell dator, ett lagringskonto, en skaluppsättning för virtuella datorer, en Log Analytics-arbetsyta eller en Application Insights-resurs. För vissa resurser (som virtuella datorer) kan du ange flera resurser som mål för aviseringsregeln.

**Signal**: Avges av målresursen. Signaler kan vara av följande typer: mått, aktivitetslogg, Application Insights och logg.

**Villkor**: En kombination av signal och logik som tillämpas på en målresurs. Exempel: 

- Procent cpu > 70%
- Svarstid för servern > 4 ms 
- Resultaträkning av en loggfråga > 100

**Aviseringsnamn**: Ett specifikt namn för varningsregeln som konfigurerats av användaren.

**Aviseringsbeskrivning**: En beskrivning av varningsregeln som konfigurerats av användaren.

**Allvarlighetsgrad**: Allvarlighetsgraden för aviseringen efter att de kriterier som anges i varningsregeln har uppfyllts. Allvarlighetsgrad kan variera från 0 till 4.

- Sev 0 = Kritisk
- Sev 1 = Fel
- Sev 2 = Varning
- Sev 3 = Information
- Sev 4 = Utförlig 

**Åtgärd**: En särskild åtgärd som vidtas när aviseringen utlöses. Mer information finns i [Åtgärdsgrupper](../../azure-monitor/platform/action-groups.md).

## <a name="what-you-can-alert-on"></a>Vad du kan varna på

Du kan avisera mått och loggar, enligt beskrivningen i [övervakningen av datakällor](../../azure-monitor/platform/data-sources.md). Dessa inkludera, men är inte begränsade till:

- Måttvärden
- Loggsökningsfrågor
- Aktivitetslogghändelser
- Hälsotillståndet för den underliggande Azure-plattformen
- Test för webbplatsens tillgänglighet

Tidigare hade Azure Monitor-mått, Application Insights, Log Analytics och Service Health separata aviseringar. Med tiden förbättrade och kombinerade Azure både användargränssnittet och olika metoder för aviseringar. Denna konsolidering pågår fortfarande. Därför finns det fortfarande vissa varningsfunktioner som ännu inte finns i det nya varningssystemet.  

| **Övervaka källa** | **Signaltyp**  | **Beskrivning** |
|-------------|----------------|-------------|
| Service Health: | Aktivitetslogg  | Stöds inte. Se [Skapa aktivitetsloggaviseringar för tjänstmeddelanden](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).  |
| Application Insights | Tester av webbtillgänglighet | Stöds inte. Se [varningar för webbtest](../../azure-monitor/app/monitor-web-app-availability.md). Tillgänglig för alla webbplatser som är instrumenterade för att skicka data till Application Insights. Få ett meddelande när tillgänglighet eller svarstider för en webbplats är lägre än förväntningarna. |

## <a name="manage-alerts"></a>Hantera aviseringar
Du kan ange tillståndet för en avisering för att ange var den befinner sig i lösningsprocessen. När de villkor som anges i aviseringsregeln är uppfyllda skapas eller utlöses en avisering och den har statusen *Ny*. Du kan ändra status när du bekräftar en avisering och när du stänger den. Alla tillståndsändringar lagras i aviseringens historik.

Följande varningstillstånd stöds.

| Status | Beskrivning |
|:---|:---|
| Ny | Problemet har just upptäckts och har ännu inte granskats. |
| Bekräftad | En administratör har granskat aviseringen och börjat arbeta med den. |
| Stängd | Problemet har lösts. När en avisering har stängts kan du öppna den igen genom att ändra den till ett annat tillstånd. |

*Varningstillståndet* är annorlunda och oberoende av *övervakarvillkoret*. Varningstillståndet anges av användaren. Monitor-villkoret ställs in av systemet. När en avisering utlöses är aviseringens övervakarvillkor inställt på *avfyrning*. När det underliggande tillståndet som orsakade att aviseringen tändes är monitorns tillstånd inställt på *att åtgärdas*. Aviseringstillståndet ändras inte förrän användaren ändrar det. Läs om hur du [ändrar läget för aviseringar och smarta grupper](https://aka.ms/managing-alert-smart-group-states).

## <a name="smart-groups"></a>Smarta grupper 

Smarta grupper är aggregeringar av aviseringar baserade på maskininlärningsalgoritmer, vilket kan bidra till att minska varningsbrus och hjälp vid felsökning. [Läs mer om smarta grupper](https://aka.ms/smart-groups) och hur du hanterar dina smarta [grupper.](https://aka.ms/managing-smart-groups)


## <a name="alerts-experience"></a>Upplevelse av varningar 
Sidan Standardaviseringar innehåller en sammanfattning av aviseringar som har skapats inom ett visst tidsintervall. Den visar de totala aviseringarna för varje allvarlighetsgrad, med kolumner som identifierar det totala antalet aviseringar i varje tillstånd för varje allvarlighetsgrad. Välj någon av [allvarlighetsgraderna](#all-alerts-page) för att öppna sidan Alla aviseringar som filtrerats efter allvarlighetsgraden.

Du kan också [programmässigt räkna upp de varningsinstanser som genereras på dina prenumerationer med hjälp av REST-API:er](#manage-your-alert-instances-programmatically).

> [!NOTE]
   >  Du kan bara komma åt aviseringar som genererats under de senaste 30 dagarna.

Den visar inte eller spårar klassiska aviseringar. Du kan ändra prenumerationerna eller filterparametrarna för att uppdatera sidan. 

![Skärmbild av sidan Aviseringar](media/alerts-overview/alerts-page.png)

Du kan filtrera den här vyn genom att välja värden i de nedrullningsbara menyerna högst upp på sidan.

| Kolumn | Beskrivning |
|:---|:---|
| Prenumeration | Välj de Azure-prenumerationer som du vill visa aviseringarna för. Du kan också välja att välja alla dina prenumerationer. Endast aviseringar som du har åtkomst till i de valda prenumerationerna ingår i vyn. |
| Resursgrupp | Välj en enskild resursgrupp. Endast aviseringar med mål i den valda resursgruppen inkluderas i vyn. |
| Tidsintervall | Endast aviseringar som utlöses inom det valda tidsintervallet inkluderas i vyn. Värden som stöds är den senaste timmen, de senaste 24 timmarna, de senaste 7 dagarna och de senaste 30 dagarna. |

Välj följande värden högst upp på sidan Aviseringar för att öppna en annan sida:

| Värde | Beskrivning |
|:---|:---|
| Totalt antal aviseringar | Det totala antalet aviseringar som matchar de valda villkoren. Välj det här värdet om du vill öppna vyn Alla aviseringar utan filter. |
| Smarta grupper | Det totala antalet smarta grupper som har skapats från aviseringarna som matchar de valda villkoren. Välj det här värdet om du vill öppna listan smarta grupper i vyn Alla aviseringar.
| Totalt antal varningsregler | Det totala antalet aviseringsregler i den valda prenumerations- och resursgruppen. Välj det här värdet om du vill öppna vyn Regler filtrerad i den valda prenumerations- och resursgruppen.


## <a name="manage-alert-rules"></a>Hantera aviseringsregler
Om du vill visa sidan **Regler** väljer du **Hantera varningsregler**. Regelsidan är en enda plats för att hantera alla varningsregler för dina Azure-prenumerationer. Den listar alla varningsregler och kan sorteras baserat på målresurser, resursgrupper, regelnamn eller status. Du kan också redigera, aktivera eller inaktivera varningsregler från den här sidan.  

 ![Skärmbild av sidan Regler](./media/alerts-overview/alerts-preview-rules.png)


## <a name="create-an-alert-rule"></a>Skapa en varningsregel
Du kan skapa aviseringar på ett konsekvent sätt, oavsett övervakningstjänst eller signaltyp. Alla avfyrade aviseringar och relaterad information finns på en sida.
 
Så här skapar du en ny aviseringsregel:
1. Välj _målet_ för aviseringen.
1. Välj _signalen_ från de tillgängliga signalerna för målet.
1. Ange den _logik_ som ska tillämpas på data från signalen.
 
Den här förenklade redigeringsprocessen kräver inte längre att du känner till övervakningskällan eller signalerna som stöds innan du väljer en Azure-resurs. Listan över tillgängliga signaler filtreras automatiskt baserat på den målresurs som du väljer. Baserat på det målet vägleds du automatiskt genom att definiera logiken i varningsregeln.  

Du kan läsa mer om hur du skapar varningsregler i [Skapa, visa och hantera aviseringar med Azure Monitor](../../azure-monitor/platform/alerts-metric.md).

Aviseringar är tillgängliga för flera Azure-övervakningstjänster. Information om hur och när du ska använda var och en av dessa tjänster finns i [Övervaka Azure-program och -resurser](../../azure-monitor/overview.md). 


## <a name="all-alerts-page"></a>Sidan Alla aviseringar 
Om du vill visa sidan **Alla aviseringar** väljer du Totalt **antal aviseringar**. Här kan du visa en lista över aviseringar som skapats inom den valda tiden. Du kan visa antingen en lista över de enskilda aviseringarna eller en lista över de smarta grupper som innehåller aviseringarna. Markera banderollen högst upp på sidan om du vill växla mellan vyer.

![Skärmbild av sidan Alla aviseringar](media/alerts-overview/all-alerts-page.png)

Du kan filtrera vyn genom att välja följande värden i de nedrullningsbara menyerna högst upp på sidan:

| Kolumn | Beskrivning |
|:---|:---|
| Prenumeration | Välj de Azure-prenumerationer som du vill visa aviseringarna för. Du kan också välja att välja alla dina prenumerationer. Endast aviseringar som du har åtkomst till i de valda prenumerationerna ingår i vyn. |
| Resursgrupp | Välj en enskild resursgrupp. Endast aviseringar med mål i den valda resursgruppen inkluderas i vyn. |
| Resurstyp | Välj en eller flera resurstyper. Endast aviseringar med mål av den valda typen inkluderas i vyn. Den här kolumnen är endast tillgänglig när en resursgrupp har angetts. |
| Resurs | Välj en resurs. Endast aviseringar med den resursen som ett mål inkluderas i vyn. Den här kolumnen är endast tillgänglig när en resurstyp har angetts. |
| Severity | Välj en allvarlighetsgrad för aviseringar eller välj **Alla** om du vill inkludera aviseringar om alla allvarlighetsgrader. |
| Övervaka tillstånd | Välj ett övervakarvillkor eller välj **Alla** om du vill inkludera aviseringar om alla villkor. |
| Tillstånd för avisering | Välj ett varningstillstånd eller välj **Alla** om du vill inkludera aviseringar för alla tillstånd. |
| Övervaka tjänsten | Välj en tjänst eller välj **Alla** om du vill inkludera alla tjänster. Endast aviseringar som skapas av regler som använder tjänsten som mål ingår. |
| Tidsintervall | Endast aviseringar som utlöses inom det valda tidsintervallet inkluderas i vyn. Värden som stöds är den senaste timmen, de senaste 24 timmarna, de senaste 7 dagarna och de senaste 30 dagarna. |

Välj **Kolumner** högst upp på sidan för att välja vilka kolumner som ska visas. 

## <a name="alert-details-page"></a>Sida med information om aviseringar
När du väljer en avisering innehåller den här sidan information om aviseringen och du kan ändra dess tillstånd.

![Skärmbild av sidan Varningsinformation](media/alerts-overview/alert-detail2.png)

Sidan Aviseringsinformation innehåller följande avsnitt:

| Section | Beskrivning |
|:---|:---|
| Sammanfattning | Visar egenskaper och annan viktig information om aviseringen. |
| Historik | Visar en lista över varje åtgärd som vidtas av aviseringen och eventuella ändringar som gjorts i aviseringen. För närvarande begränsad till tillståndsändringar. |
| Diagnostik | Information om den smarta grupp där aviseringen ingår. Antalet *aviseringar* avser antalet aviseringar som ingår i den smarta gruppen. Innehåller andra aviseringar i samma smarta grupp som har skapats under de senaste 30 dagarna, oavsett tidsfiltret på varningslistesidan. Välj en avisering om du vill visa dess detaljer. |

## <a name="role-based-access-control-rbac-for-your-alert-instances"></a>Rollbaserad åtkomstkontroll (RBAC) för dina varningsinstanser

Förbrukning och hantering av varningsinstanser kräver att användaren har de inbyggda RBAC-rollerna för antingen [övervakningsdeltagare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) eller [övervakningsläsare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader). Dessa roller stöds på alla Azure Resource Manager-scope, från prenumerationsnivå till detaljerade tilldelningar på resursnivå. Om en användare till exempel bara har `ContosoVM1`övervakning av deltagaråtkomst för virtuell `ContosoVM1`dator kan användaren bara använda och hantera aviseringar som genereras på .

## <a name="manage-your-alert-instances-programmatically"></a>Hantera dina varningsinstanser programmässigt

Du kanske vill fråga programmässigt efter aviseringar som genereras mot din prenumeration. Detta kan vara att skapa anpassade vyer utanför Azure-portalen, eller att analysera dina aviseringar för att identifiera mönster och trender.

Du kan fråga efter aviseringar som genereras mot dina prenumerationer antingen med hjälp av [REST-API:et för varningshantering](https://aka.ms/alert-management-api) eller genom att använda [Azure Resource Graph](../../governance/resource-graph/overview.md) och REST API for [Resources](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources).

Med REST-API:et för resursdiagram för resurser kan du fråga efter varningsinstanser i stor skala. Detta rekommenderas när du måste hantera aviseringar som genereras för många prenumerationer. 

Följande exempelbegäran till REST-API:et för resursdiagram returnerar antalet aviseringar inom en prenumeration:

```json
{
  "subscriptions": [
    <subscriptionId>
  ],
  "query": "AlertsManagementResources | where type =~ 'Microsoft.AlertsManagement/alerts' | summarize count()"
}
```

Du kan också se resultatet av den här Resource Graph-frågan i portalen med Azure Resource Graph Explorer: [portal.azure.com](https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/AlertsManagementResources%20%7C%20where%20type%20%3D~%20%27Microsoft.AlertsManagement%2Falerts%27%20%7C%20summarize%20count())

Du kan fråga aviseringarna efter deras [väsentliga](alerts-common-schema-definitions.md#essentials) fält.

Använd [REST-API:et för varningshantering](https://aka.ms/alert-management-api) för att få mer information om specifika aviseringar, inklusive deras [aviseringskontextfält.](alerts-common-schema-definitions.md#alert-context)

## <a name="next-steps"></a>Nästa steg

- [Läs mer om smarta grupper](https://aka.ms/smart-groups)
- [Läs mer om åtgärdsgrupper](../../azure-monitor/platform/action-groups.md)
- [Hantera dina varningsinstanser i Azure](https://aka.ms/managing-alert-instances)
- [Hantera smarta grupper](https://aka.ms/managing-smart-groups)
- [Läs mer om prissättning av Azure-aviseringar](https://azure.microsoft.com/pricing/details/monitor/)






