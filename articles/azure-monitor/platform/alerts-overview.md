---
title: Översikt över aviseringar och meddelande övervakning i Azure
description: Översikt över aviseringar i Azure. Aviseringar, klassiska aviseringar, aviserings gränssnittet.
author: rboucher
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 01/28/2018
ms.author: robb
ms.subservice: alerts
ms.openlocfilehash: defc317618dfffd0e2b28c75b6168ec1dbda36b7
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2019
ms.locfileid: "70735115"
---
# <a name="overview-of-alerts-in-microsoft-azure"></a>Översikt över aviseringar i Microsoft Azure 

I den här artikeln beskrivs vilka aviseringar som är, deras fördelar och hur du kommer igång med dem.  




## <a name="what-are-alerts-in-microsoft-azure"></a>Vad är aviseringar i Microsoft Azure?
Aviseringar proaktivt meddela dig när viktiga villkor finns i dina övervaknings data. De gör att du kan identifiera och åtgärda problem innan användarna av systemet ser dem. 

Den här artikeln beskriver den enhetliga aviserings upplevelsen i Azure Monitor, som nu innehåller aviseringar som hanterades av Log Analytics och Application Insights. [Föregående aviserings upplevelse](alerts-classic.overview.md) och aviserings typer kallas för **klassiska aviseringar**. Du kan visa den här äldre upplevelsen och äldre aviserings typ genom att klicka på **Visa klassiska aviseringar** överst på aviserings sidan. 

## <a name="overview"></a>Översikt

Diagrammet nedan visar flödet av aviseringar. 

![Aviserings flöde](media/alerts-overview/Azure-Monitor-Alerts.svg)

Varnings regler skiljs från aviseringar och åtgärder som vidtas när en avisering utlöses. 

**Aviserings regel** – varnings regeln samlar in mål och kriterier för aviseringar. Varnings regeln kan vara i ett aktiverat eller inaktiverat tillstånd. Aviseringar utlöses bara när de är aktiverade. 

Nyckelattribut för en varnings regel är:

**Mål resurs** – definierar omfattning och signaler som är tillgängliga för aviseringar. Ett mål kan vara vilken Azure-resurs som helst. Exempel mål: en virtuell dator, ett lagrings konto, en skalnings uppsättning för virtuella datorer, en Log Analytics-arbetsyta eller en Application Insights-resurs. För vissa resurser (t. ex. Virtual Machines) kan du ange flera resurser som mål för varnings regeln.

**Signal** -signaler genereras av mål resursen och kan vara av flera typer. Mått, aktivitets logg, Application Insights och logg.

**Kriterier** – kriterier är en kombination av signal och logik som tillämpas på en mål resurs. Exempel: 
   - Procent processor > 70%
   - Server svars tid > 4 MS 
   - Antal resultat för en logg fråga > 100

**Aviserings namn** – ett speciellt namn för aviserings regeln som kon figurer ATS av användaren

**Aviserings Beskrivning** – en beskrivning av aviserings regeln som kon figurer ATS av användaren

**Allvarlighets** grad – allvarlighets graden för aviseringen när kriteriet som anges i varnings regeln är uppfyllt. Allvarlighets graden kan vara mellan 0 och 4.
Allvarlighets grad 0 = kritisk allvarlighets grad 1 = fel allvarlighets grad 2 = varning allvarlighets grad 3 = information allvarlighets grad 4 = utförlig

**Åtgärd** – en speciell åtgärd som vidtas när aviseringen utlöses. Mer information finns i [Åtgärds grupper](../../azure-monitor/platform/action-groups.md).

## <a name="what-you-can-alert-on"></a>Vad du kan Avisera om

Du kan varna för mått och loggar på det sätt som beskrivs i [övervaka data källor](../../azure-monitor/platform/data-sources-reference.md). Dessa inkluderar, men är inte begränsade till:
- Mät värden
- Loggs öknings frågor
- Aktivitets logg händelser
- Hälso tillståndet för den underliggande Azure-plattformen
- Test för webbplats tillgänglighet

Tidigare Azure Monitor Mät värden, Application Insights, Log Analytics och Service Health hade separata aviserings funktioner. Med tiden kan Azure förbättra och kombinera både användar gränssnittet och olika metoder för aviseringar. Denna konsolidering pågår fortfarande. Därför finns det fortfarande vissa aviserings funktioner som ännu inte finns i det nya varnings systemet.  

| **Övervaka källa** | **Signal typ**  | **Beskrivning** | 
|-------------|----------------|-------------|
| Tjänstens hälsa | Aktivitetslogg  | Stöds ej. Se [skapa aktivitets logg aviseringar för tjänst meddelanden](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).  |
| Application Insights | Test av webb tillgänglighet | Stöds ej. Se [webb test aviseringar](../../azure-monitor/app/monitor-web-app-availability.md). Tillgängligt för alla webbplatser som är instrumenterade att skicka data till Application Insights. Få ett meddelande när tillgänglighet eller svars tider för en webbplats är under förväntningarna. |

## <a name="manage-alerts"></a>Hantera aviseringar
Du kan ange status för en avisering för att ange var den finns i lösnings processen. När kriteriet som anges i varnings regeln uppfylls skapas eller utlöses en avisering, den har statusen *ny*. Du kan ändra statusen när du bekräftar en avisering och när du stänger den. Alla status ändringar lagras i historiken för aviseringen.

Följande aviserings tillstånd stöds.

| State | Beskrivning |
|:---|:---|
| Ny | Problemet har precis identifierats och har ännu inte granskats. |
| Godkänt | En administratör har granskat aviseringen och börjat arbeta med den. |
| Stängd | Problemet har åtgärd ATS. När en avisering har stängts kan du öppna den igen genom att ändra den till ett annat tillstånd. |

**Aviserings tillståndet** är annorlunda och oberoende av **övervaknings villkoret**. Aviserings tillstånd anges av användaren. Övervaknings villkoret anges av systemet. När en varning utlöses anges övervaknings villkoret för aviseringen till *utlöst*. När det underliggande villkoret som orsakade aviseringen om att brand figurer rensas, anges övervaknings villkoret som *löst*. Aviserings statusen har inte ändrats förrän användaren ändrar det. Lär dig [hur du ändrar status för dina aviseringar och smarta grupper](https://aka.ms/managing-alert-smart-group-states).

## <a name="smart-groups"></a>Smarta grupper 
Smarta grupper är i för hands version. 

Smarta grupper är agg regeringar för aviseringar baserade på Machine Learning-algoritmer som kan hjälpa till att minska aviserings bruset och hjälpa till med fel sökning. [Lär dig mer om smarta grupper](https://aka.ms/smart-groups) och [hur du hanterar dina smarta grupper](https://aka.ms/managing-smart-groups).


## <a name="alerts-experience"></a>Aviserings upplevelse 
Sidan standard varningar innehåller en sammanfattning av de aviseringar som skapas inom ett visst tidsintervall. Den visar den totala aviseringen för varje allvarlighets grad med kolumner som anger det totala antalet aviseringar i varje tillstånd för varje allvarlighets grad. Välj någon av allvarlighets graderna för att öppna sidan [alla aviseringar](#all-alerts-page) som filtrerats efter allvarlighets grad.

Du kan också [program mässigt räkna upp de aviserings instanser som genereras i dina prenumerationer med hjälp av REST-API: er](#manage-your-alert-instances-programmatically).

> [!NOTE]
   >  Endast aviseringar som genererats under de senaste 30 dagarna kan nås i UX-eller REST-API: erna.

Den visar eller spårar inte äldre [klassiska aviseringar](#classic-alerts). Du kan ändra prenumerationer eller filter parametrar för att uppdatera sidan. 

![Sidan aviseringar](media/alerts-overview/alerts-page.png)

Du kan filtrera den här vyn genom att välja värden i list menyerna längst upp på sidan.

| Kolumn | Beskrivning |
|:---|:---|
| Subscription | Välj de Azure-prenumerationer som du vill visa aviseringar för. Alternativt kan du välja att välja alla dina prenumerationer. Endast aviseringar som du har åtkomst till i de valda prenumerationerna ingår i vyn. |
| Resource group | Välj en enskild resurs grupp. Endast aviseringar med mål i den valda resurs gruppen ingår i vyn. |
| Tidsintervall | Endast aviseringar som har utlösts inom den valda tids perioden tas med i vyn. De värden som stöds är den senaste timmen, de senaste 24 timmarna, de senaste 7 dagarna och de senaste 30 dagarna. |

Välj följande värden överst på aviserings sidan för att öppna en annan sida.

| Value | Beskrivning |
|:---|:---|
| Totalt antal aviseringar | Det totala antalet aviseringar som matchar de valda kriterierna. Välj det här värdet för att öppna vyn alla aviseringar utan filter. |
| Smarta grupper | Det totala antalet smarta grupper som har skapats från de aviseringar som matchar de valda kriterierna. Välj det här värdet för att öppna listan Smart grupper i vyn alla aviseringar.
| Totalt antal aviseringsregler | Det totala antalet aviserings regler i den valda prenumerationen och resurs gruppen. Välj det här värdet för att öppna regel vyn filtrerad på den valda prenumerationen och resurs gruppen.


## <a name="manage-alert-rules"></a>Hantera varningsregler
Klicka på **Hantera aviserings regler** för att visa sidan **regler** . **Regler** är en enda plats för att hantera alla aviserings regler i dina Azure-prenumerationer. Den visar alla varnings regler och kan sorteras baserat på mål resurser, resurs grupper, regel namn eller status. Aviserings regler kan också redige ras, aktive ras eller inaktive ras från den här sidan.  

 ![aviseringar-regler](./media/alerts-overview/alerts-preview-rules.png)


## <a name="create-an-alert-rule"></a>Skapa en varningsregel
Aviseringar kan skapas på ett konsekvent sätt oavsett övervaknings tjänst eller signal typ. Alla utlösta aviseringar och relaterad information finns på en enda sida.
 
Du skapar en ny aviserings regel med följande tre steg:
1. Välj _målet_ för aviseringen.
1. Välj _signalen_ från tillgängliga signaler för målet.
1. Ange vilken _logik_ som ska användas för data från signalen.
 
Den här förenklade redigerings processen kräver inte längre att du vet vilken övervaknings källa eller vilka signaler som stöds innan du väljer en Azure-resurs. Listan över tillgängliga signaler filtreras automatiskt baserat på den mål resurs du väljer. Baserat på det målet vägleds du också genom att definiera logiken i varnings regeln automatiskt.  

Du kan lära dig mer om hur du skapar aviserings regler i [skapa, Visa och hantera aviseringar med hjälp av Azure Monitor](../../azure-monitor/platform/alerts-metric.md).

Aviseringar är tillgängliga i flera Azure Monitoring Services. Information om hur och när du ska använda var och en av dessa tjänster finns i [övervaka Azure-program och-resurser](../../azure-monitor/overview.md). 


## <a name="all-alerts-page"></a>Sidan alla aviseringar 
Klicka på totalt antal aviseringar för att visa sidan alla aviseringar. Här kan du Visa en lista över aviseringar som har skapats inom den valda tids perioden. Du kan visa en lista över enskilda aviseringar eller en lista över de smarta grupper som innehåller aviseringarna. Välj banderollen överst på sidan för att växla mellan vyer.

![Sidan alla aviseringar](media/alerts-overview/all-alerts-page.png)

Du kan filtrera vyn genom att välja följande värden i list menyerna längst upp på sidan.

| Kolumn | Beskrivning |
|:---|:---|
| Subscription | Välj de Azure-prenumerationer som du vill visa aviseringar för. Alternativt kan du välja att välja alla dina prenumerationer. Endast aviseringar som du har åtkomst till i de valda prenumerationerna ingår i vyn. |
| Resource group | Välj en enskild resurs grupp. Endast aviseringar med mål i den valda resurs gruppen ingår i vyn. |
| Resurstyp | Välj en eller flera resurs typer. Endast aviseringar med mål av den valda typen tas med i vyn. Den här kolumnen är bara tillgänglig efter att en resurs grupp har angetts. |
| Resource | Välj en resurs. Endast aviseringar med den resursen som mål ingår i vyn. Den här kolumnen är bara tillgänglig efter att en resurs typ har angetts. |
| severity | Välj en allvarlighets grad för aviseringar eller Välj *alla* om du vill inkludera aviseringar för alla allvarlighets grader. |
| Övervakningsvillkor | Välj ett övervaknings villkor eller Välj *alla* för att inkludera aviseringar om villkor. |
| Aviseringstillstånd | Välj ett aviserings tillstånd eller Välj *alla* om du vill inkludera aviseringar för tillstånd. |
| Övervaka tjänsten | Välj en tjänst eller Välj *alla* om du vill inkludera alla tjänster. Endast aviseringar som skapats av regler som använder tjänsten som mål ingår. |
| Tidsintervall | Endast aviseringar som har utlösts inom den valda tids perioden tas med i vyn. De värden som stöds är den senaste timmen, de senaste 24 timmarna, de senaste 7 dagarna och de senaste 30 dagarna. |

Välj **kolumner** överst på sidan för att välja vilka kolumner som ska visas. 

## <a name="alert-details-page"></a>Sidan aviserings information
Sidan aviserings information visas när du väljer en avisering. Den innehåller information om aviseringen och gör det möjligt att ändra dess tillstånd.

![Aviseringsinformation](media/alerts-overview/alert-detail2.png)

Sidan aviserings information innehåller följande avsnitt.

| Section | Beskrivning |
|:---|:---|
| Sammanfattning | Visar egenskaper och annan viktig information om aviseringen. |
| Historik | Visar en lista över varje åtgärd som vidtas av aviseringen och eventuella ändringar som gjorts i aviseringen. För närvarande begränsad till tillstånds ändringar. |
| Diagnostik | Information om den smarta gruppen som aviseringen ingår i. Antalet *aviseringar* avser antalet aviseringar som ingår i den smarta gruppen. Innehåller andra aviseringar i samma smarta grupp som skapades under de senaste 30 dagarna oavsett tids filter på sidan aviserings lista. Välj en avisering om du vill visa information om den. |

## <a name="role-based-access-control-rbac-for-your-alert-instances"></a>Rollbaserad åtkomst kontroll (RBAC) för dina aviserings instanser

Användningen och hanteringen av varnings instanser kräver att användaren har de inbyggda RBAC-rollerna för övervakning av [deltagare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) eller [övervaknings läsare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader). Dessa roller stöds på alla Azure Resource Managers omfång, från prenumerations nivå till detaljerade tilldelningar på en resurs nivå. Om en användare till exempel bara har åtkomst till "övervaknings deltagare" för den virtuella datorn "ContosoVM1" kan han eller hon använda och hantera enbart aviseringar som genereras på ContosoVM1.

## <a name="manage-your-alert-instances-programmatically"></a>Hantera aviserings instanser program mässigt

Det finns många scenarier där du vill fråga efter aviseringar som genererats mot din prenumeration. Detta kan vara att skapa anpassade vyer utanför Azure Portal eller analysera aviseringar för att identifiera mönster och trender.

Du kan fråga efter aviseringar som har genererats med dina prenumerationer antingen med hjälp av [Aviseringshantering REST API](https://aka.ms/alert-management-api) eller genom att använda [Azure Resource Graph-REST API för aviseringar](https://docs.microsoft.com/rest/api/azureresourcegraph/resources/resources).

Med [Azures resurs diagram REST API för aviseringar](https://docs.microsoft.com/rest/api/azureresourcegraph/resources/resources) kan du fråga efter aviserings instanser i stor skala. Detta rekommenderas för scenarier där du måste hantera aviseringar som genererats över flera prenumerationer. 

Följande exempel förfrågan till API: et returnerar antalet aviseringar inom en prenumeration:

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
Aviseringar kan frågas om de [viktigaste](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-definitions#essentials-fields) fälten.

[Aviseringshantering REST API](https://aka.ms/alert-management-api) kan användas för att få mer information om enskilda aviseringar, inklusive deras ["aviserings Sammanhangs fält"](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-definitions#alert-context-fields) .

## <a name="classic-alerts"></a>Klassiska aviseringar 

Aviserings funktionen för Azure Monitor mått och aktivitets logg före juni 2018 kallas "varningar (klassisk)". 

Mer information finns i [klassiska aviseringar](./../../azure-monitor/platform/alerts-classic.overview.md)


## <a name="next-steps"></a>Nästa steg

- [Läs mer om smarta grupper](https://aka.ms/smart-groups)
- [Lär dig mer om åtgärds grupper](../../azure-monitor/platform/action-groups.md)
- [Hantera dina varnings instanser i Azure](https://aka.ms/managing-alert-instances)
- [Hantera smarta grupper](https://aka.ms/managing-smart-groups)






