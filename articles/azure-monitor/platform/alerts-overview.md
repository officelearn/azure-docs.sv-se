---
title: Översikt över aviseringar och övervakning av meddelande i Azure
description: Översikt över aviseringar i Azure. Aviseringar, klassiska aviseringar, gränssnittet aviseringar.
author: rboucher
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 01/28/2018
ms.author: robb
ms.subservice: alerts
ms.openlocfilehash: c389f2ab9e67cbb1fd1a6a0c9ee274bca7d4c99d
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/04/2019
ms.locfileid: "67560423"
---
# <a name="overview-of-alerts-in-microsoft-azure"></a>Översikt över aviseringar i Microsoft Azure 

Den här artikeln beskriver vilka aviseringar har sina fördelar, och hur du kommer igång med dem.  




## <a name="what-are-alerts-in-microsoft-azure"></a>Vad är aviseringar i Microsoft Azure?
Aviseringar meddelar proaktivt dig när det är viktigt villkor finns i dina övervakningsdata. De gör att du kan identifiera och lösa problem innan användarna av systemet uppmärksamma dem. 

Den här artikeln beskriver den avisering enhetlig upplevelsen i Azure Monitor, som nu innehåller aviseringar som hanterades av Log Analytics och Application Insights. Den [tidigare avisering erfarenhet](alerts-classic.overview.md) och aviseringstyper kallas **klassiska aviseringar**. Du kan visa den här äldre upplevelse och äldre typ av avisering genom att klicka på **Visa klassiska aviseringar** överst på sidan avisering. 

## <a name="overview"></a>Översikt

Diagrammet nedan visar flödet av aviseringar. 

![Varningsflödet](media/alerts-overview/Azure-Monitor-Alerts.svg)

Varningsregler är avgränsade från aviseringar och åtgärder som vidtas när en avisering utlöses. 

**Varningsregeln** -regeln samlar in mål- och villkoren för aviseringar. Varningsregeln kan vara i ett aktiverat eller inaktiverat läge. Aviseringar kan bara aktiveras när det är aktiverat. 

Viktiga punkter för en varningsregel är:

**Målresurs** – definierar omfattningen och signalerar till tillgängliga för aviseringar. Ett mål kan vara en Azure-resurs. Exempel mål: en virtuell dator, ett lagringskonto, en skalningsuppsättning för virtuell dator, en Log Analytics-arbetsyta eller en Application Insights-resurs. För vissa resurser (t.ex. virtuella datorer), du kan ange flera resurser som mål för regeln.

**Signal** – signalerar genereras av målresursen och kan vara av flera typer. Mått, aktivitet log, Application Insights och Log.

**Kriterier** – villkor är kombination av Signal och logik som tillämpas på en målresurs. Exempel: 
   - Processorprocentandel > 70%
   - Svarstid för servern > 4 ms 
   - Resultatantal på en fråga > 100 log

**Varningens namn** – ett visst namn för regeln konfigurerats av användaren

**Aviseringsbeskrivning** – en beskrivning för regeln konfigurerats av användaren

**Allvarlighetsgrad** – allvarlighetsgraden för aviseringen när villkoren som har angetts i regeln uppfylls. Allvarlighetsgrad kan vara mellan 0 och 4.

**Åtgärden** – en specifik åtgärd vidtas när aviseringen utlöses. Mer information finns i [åtgärdsgrupper](../../azure-monitor/platform/action-groups.md).

## <a name="what-you-can-alert-on"></a>Du kan meddela på

Du kan meddela mått och loggar enligt beskrivningen i [övervakning datakällor](../../azure-monitor/platform/data-sources-reference.md). Dessa inkludera, men är inte begränsade till:
- Måttvärden
- Loggsökningsfrågor
- Händelser i aktivitetsloggen
- Hälsotillståndet för underliggande Azure-plattformen
- Tester för tillgänglighet för webbplats

Tidigare hade Azure Monitor-mått, Application Insights, logganalys och Tjänstehälsa för separata aviseringar funktioner. Framöver kommer Azure förbättrad och kombineras både användargränssnitt och olika metoder för aviseringar. Denna konsolidering pågår fortfarande. Därför finns fortfarande vissa varningar funktioner inte ännu i det nya systemet för aviseringar.  

| **Övervaka källan** | **Signaltyp**  | **Beskrivning** | 
|-------------|----------------|-------------|
| Service Health | Aktivitetslogg  | Stöds ej. Se [skapa aviseringar för aktivitetsloggen för tjänstmeddelanden](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).  |
| Application Insights | Webbtester för tillgänglighet | Stöds ej. Se [webbtestaviseringar](../../azure-monitor/app/monitor-web-app-availability.md). Tillgänglig för alla webbplatser som är utrustade för att skicka data till Application Insights. Få ett meddelande när tillgänglighet och svarstider på en webbplats som är lägre än förväntningar. |

## <a name="manage-alerts"></a>Hantera aviseringar
Du kan ange tillståndet för en avisering för att ange var den är i lösningsprocessen. När villkoren som har angetts i regeln uppfylls, en avisering skapas eller utlöstes, den har statusen *New*. Du kan ändra status när du har godkänt en avisering och när du stänger den. Alla tillståndsändringar lagras i historiken för aviseringen.

Dessa aviseringstillstånd stöds.

| Status | Beskrivning |
|:---|:---|
| Ny | Problemet bara har identifierats och har inte har granskat. |
| Bekräftad | En administratör har granskat aviseringen och mellanrum. |
| Stängd | Problemet har lösts. När en avisering har stängts kan öppna du det genom att ändra det till ett annat tillstånd. |

**Avisera tillstånd** är annorlunda och oberoende av den **övervaka villkor**. Tillstånd för avisering har angetts av användaren. Övervakarens villkor har angetts av systemet. När en avisering utlöses aviseringens övervakarens villkor har angetts *utlösta*. När det underliggande villkoret som orsakade aviseringen utlöses raderar övervakarens villkor har angetts till *löst*. Aviseringstillståndet ändras inte förrän användaren ändrar den. Lär dig [hur du ändrar status för dina aviseringar och smart grupper](https://aka.ms/managing-alert-smart-group-states).

## <a name="smart-groups"></a>Smarta grupper 
Smart grupper finns i förhandsversion. 

Smart grupper är sammanställning av aviseringar baserat på machine learning-algoritmer som kan hjälpa att minska aviseringsbruset och underlätta felsökning. [Läs mer om Smart grupper](https://aka.ms/smart-groups) och [hur du hanterar dina smarta grupper](https://aka.ms/managing-smart-groups).


## <a name="alerts-experience"></a>Aviseringsgränssnittet 
Aviseringar standardsidan ger en översikt över aviseringar som har skapats inom ett visst tidsintervall. Den visar totalt antal aviseringar för varje allvarlighetsgrad med kolumner som identifierar det totala antalet aviseringar i varje status för varje allvarlighetsgrad. Välj någon av allvarlighetsgrader att öppna den [alla aviseringar](#all-alerts-page) sidan filtreras efter den allvarlighetsgraden.

Du kan också [programmässigt räknar upp aviseringsinstanser som genereras på dina prenumerationer med hjälp av REST API: er](#manage-your-alert-instances-programmatically).

Inte visa eller spåra äldre [klassiska aviseringar](#classic-alerts). Du kan ändra prenumerationer eller filtrera parametrar för att uppdatera sidan. 

![Sidan varningar](media/alerts-overview/alerts-page.png)

Du kan filtrera den här vyn genom att välja värden i listrutorna överst på sidan.

| Kolumn | Beskrivning |
|:---|:---|
| Prenumeration | Välj de Azure-prenumerationer som du vill visa aviseringarna. Du kan välja att markera alla dina prenumerationer. Endast aviseringar som du har åtkomst till i de valda prenumerationerna ingår i vyn. |
| Resursgrupp | Välj en enskild resursgrupp. Endast aviseringar med mål i den valda resursgruppen ingår i vyn. |
| Tidsintervall | Bara de aviseringar som utlösts inom det valda tidsfönstret ingår i vyn. Värden som stöds är den senaste timmen, de senaste 24 timmarna, de senaste 7 dagarna och de senaste 30 dagarna. |

Välj följande värden överst på sidan aviseringar för att öppna en annan sida.

| Värde | Beskrivning |
|:---|:---|
| Totalt antal aviseringar | Det totala antalet aviseringar som matchar de valda villkoren. Välj det här värdet för att öppna vyn alla aviseringar med inget filter. |
| Smarta grupper | Det totala antalet smart grupper som har skapats från de aviseringar som matchar de valda villkoren. Välj det här värdet för att öppna listan över smart i vyn alla aviseringar.
| Totalt antal aviseringsregler | Det totala antalet Varningsregler i den valda prenumerationen och resursgruppen. Välj det här värdet för att öppna vyn regler filtreras på den valda prenumerationen och resursgruppen.


## <a name="manage-alert-rules"></a>Hantera Varningsregler
Klicka på **hantera aviseringsregler** att visa den **regler** sidan. **Regler** är en enda plats för att hantera alla aviseringsregler för alla Azure-prenumerationer. Den visar en lista över alla aviseringsregler och kan sorteras utifrån target-resurser, resursgrupper, regelnamn eller status. Varningsregler kan även redigeras, aktiveras eller inaktiveras från den här sidan.  

 ![regler för aviseringar](./media/alerts-overview/alerts-preview-rules.png)


## <a name="create-an-alert-rule"></a>Skapa en varningsregel
Aviseringar kan skrivas i ett konsekvent sätt oavsett övervakningstjänsten eller skicka en signal typen. Alla utlösta aviseringar och relaterade detaljer är tillgängliga i en sida.
 
Du kan skapa en ny aviseringsregel med följande tre steg:
1. Välj den _target_ för aviseringen.
1. Välj den _signal_ från de tillgängliga signalerna för målet.
1. Ange den _logic_ som ska tillämpas på data från signalen.
 
Förenklad redigering processen kräver inte längre att du enkelt se övervakningen käll- eller signaler som stöds innan du väljer en Azure-resurs. Listan över tillgängliga signaler filtreras automatiskt baserat på vilken resurs som du väljer. Även baserat på vars mål, vägleds du genom definiera logiken varningsregelns automatiskt.  

Du kan lära dig mer om hur du skapar aviseringsregler i [skapa, visa och hantera aviseringar med hjälp av Azure Monitor](../../azure-monitor/platform/alerts-metric.md).

Aviseringar är tillgängliga i flera Azure-övervakningstjänster. Information om hur och när du ska använda var och en av dessa tjänster finns i [övervakning av Azure-program och resurser](../../azure-monitor/overview.md). 


## <a name="all-alerts-page"></a>Alla aviseringssidan 
Klicka på totalt antal aviseringar att visa aviseringssidan alla. Här kan du visa en lista över aviseringar som har skapats i den valda tidsperioden. Du kan visa en lista över enskilda aviseringar eller en lista över de smarta grupper som innehåller aviseringarna. Välj banderoll överst på sidan för att växla mellan vyer.

![Alla aviseringssidan](media/alerts-overview/all-alerts-page.png)

Du kan filtrera vyn genom att välja följande värden i listrutorna överst på sidan.

| Kolumn | Beskrivning |
|:---|:---|
| Prenumeration | Välj de Azure-prenumerationer som du vill visa aviseringarna. Du kan välja att markera alla dina prenumerationer. Endast aviseringar som du har åtkomst till i de valda prenumerationerna ingår i vyn. |
| Resursgrupp | Välj en enskild resursgrupp. Endast aviseringar med mål i den valda resursgruppen ingår i vyn. |
| Resurstyp | Välj en eller flera resurstyper. Endast aviseringar med mål för det valda ingår i vyn. Den här kolumnen är endast tillgänglig när en resursgrupp har angetts. |
| Resurs | Välj en resurs. Endast aviseringar med den här resursen som ett mål som ingår i vyn. Den här kolumnen är endast tillgänglig när du har angett en resurstyp. |
| Severity | Välj en allvarlighetsgrad för avisering, eller välj *alla* att inkludera aviseringar för alla allvarlighetsgrader. |
| Övervakningsvillkor | Välj ett övervakningsvillkor eller välj *alla* att inkludera aviseringar av villkor. |
| Aviseringstillstånd | Väljer du en aviseringstillståndet eller *alla* att inkludera aviseringar av tillstånd. |
| Övervaka tjänsten | Välj en tjänst, eller välj *alla* att inkludera alla tjänster. Det ingår bara aviseringar som har skapats av regler som använder tjänsten som mål. |
| Tidsintervall | Bara de aviseringar som utlösts inom det valda tidsfönstret ingår i vyn. Värden som stöds är den senaste timmen, de senaste 24 timmarna, de senaste 7 dagarna och de senaste 30 dagarna. |

Välj **kolumner** överst på sidan för att välja vilka kolumner som ska visas. 

## <a name="alert-details-page"></a>Information om sidan
Sidan aviseringsinformation visas när du väljer en avisering. Det ger information om aviseringen och du kan ändra tillståndet.

![Aviseringsinformation](media/alerts-overview/alert-detail2.png)

Sidan information om innehåller följande avsnitt.

| Section | Beskrivning |
|:---|:---|
| Sammanfattning | Visar egenskaperna och andra viktiga information om aviseringen. |
| Historik | Visar en lista över varje åtgärd som aviseringen och ändringar som görs till aviseringen. För närvarande begränsad till tillståndsändringar. |
| Diagnostik | Information om gruppen smart aviseringen ingår i. Den *antal aviseringar* avser antalet aviseringar som ingår i gruppen smart. Innehåller andra aviseringar i samma smart grupp som har skapats under de senaste 30 dagarna oavsett tidsfiltret i sidan med listan över aviseringar. Välj en avisering för att visa dess information. |

## <a name="role-based-access-control-rbac-for-your-alert-instances"></a>Rollbaserad åtkomstkontroll (RBAC) för din aviseringsinstanser

Användning och hantering av aviseringsinstanser kräver att användaren har inbyggda RBAC-roller antingen [övervaka deltagare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) eller [övervaka läsare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader). Dessa roller stöds i alla Azure Resource Manager-omfattningen från prenumerationsnivån till detaljerade tilldelningar på en resursnivå. Till exempel om en användare har bara ”monitoring deltagare-åtkomst för den virtuella datorn 'ContosoVM1', kan sedan han använda och hantera bara de aviseringar som genereras på 'ContosoVM1'.

## <a name="manage-your-alert-instances-programmatically"></a>Hantera din aviseringsinstanser programmässigt

Det finns många scenarier där du kan också programmera en fråga för aviseringar som genereras mot din prenumeration. Detta kan vara att skapa anpassade vyer utanför Azure-portalen eller analysera dina aviseringar för att identifiera mönster och trender.

Du kan fråga efter aviseringar som genereras mot dina prenumerationer som antingen genom att använda den [Alert Management REST API](https://aka.ms/alert-management-api) eller genom att använda den [Azure Resource Graph REST API för aviseringar](https://docs.microsoft.com/rest/api/azureresourcegraph/resources/resources).

Den [Azure Resource Graph REST API för aviseringar](https://docs.microsoft.com/rest/api/azureresourcegraph/resources/resources) gör att du kan fråga efter aviseringsinstanser i stor skala. Detta rekommenderas för scenarier där du måste hantera aviseringar för många prenumerationer. 

Följande exempelbegäran-API: et returnerar antalet aviseringar i en prenumeration:

```json
{
  "subscriptions": [
    <subscriptionId>
  ],
  "query": "where type =~ 'Microsoft.AlertsManagement/alerts' | summarize count()",
  "options": {
            "dataset":"alerts"
  }
}
```
Aviseringarna kan frågas för sina [”grundläggande”](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-definitions#essentials-fields) fält.

Den [avisering Management REST API](https://aka.ms/alert-management-api) kan användas för att få mer information om specifika aviseringar, inklusive deras ['aviseringskontext'](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-definitions#alert-context-fields) fält.

## <a name="classic-alerts"></a>Klassiska aviseringar 

Azure Monitor-mått och aktivitet loggen avisering funktionen innan juni 2018 kallas ”aviseringar (klassisk)”. 

Mer information finns i [klassisk aviseringar](./../../azure-monitor/platform/alerts-classic.overview.md)


## <a name="next-steps"></a>Nästa steg

- [Läs mer om Smart grupper](https://aka.ms/smart-groups)
- [Lär dig mer om åtgärdsgrupper](../../azure-monitor/platform/action-groups.md)
- [Hantera dina aviseringar instanser i Azure](https://aka.ms/managing-alert-instances)
- [Hantera smarta grupper](https://aka.ms/managing-smart-groups)






