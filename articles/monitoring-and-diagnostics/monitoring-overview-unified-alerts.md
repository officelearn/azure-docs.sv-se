---
title: Enhetlig aviseringar i Azure-Monitor | Microsoft Docs
description: Beskrivning av enhetlig aviseringar i Azure som du kan hantera aviseringar och varningarna regler i Azure-tjänster.
author: manishsm-msft
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2018
ms.author: mamit,bwren
ms.custom: ''
ms.openlocfilehash: 699dff42846ee1f9d42980feca55d8a79e2514e3
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34839851"
---
# <a name="unified-alerts-in-azure-monitor"></a>Enhetlig aviseringar i Azure-Monitor

## <a name="overview"></a>Översikt

> [!NOTE]
>  En ny enhetlig avisering miljö där du kan hantera varningar från flera prenumerationer och ger avisering tillstånd och smarta grupper är för närvarande tillgänglig som förhandsversion. Finns det [i den här artikeln för senaste](#enhanced-unified-alerts-experience-public-preview) för en beskrivning av det här förbättrad upplevelse och processen för att aktivera den.


Den här artikeln beskriver enhetlig avisering upplevelse i Azure-Monitor. Den [tidigare avisering erfarenhet](monitoring-overview-alerts.md) är tillgänglig från den **aviseringar (klassisk)** alternativ på menyn Azure-Monitor. 

## <a name="features-of-the-unified-alert-experience"></a>Funktioner i enhetlig avisering miljö

Enhetlig miljö har följande fördelar över klassiska upplevelsen:

-   **Bättre meddelandesystem**: enhetlig aviseringar Använd [åtgärdsgrupper]( https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups), som är namngiven grupper av meddelanden och åtgärder som kan återanvändas i flera aviseringar. 
- **Enhetlig redigering upplevelse** – du kan hantera aviseringar och avisering regler för statistik, loggar och aktivitet logga via Azure-Monitor, logganalys och Application Insights på ett ställe. 
- **Visa utlöses logganalys aviseringar i Azure-portalen** -Visa aviseringar från logganalys med andra aviseringar från andra källor i Azure-portalen. Tidigare var dessa i en separat portal.
- **Uppdelning av Fired aviseringar och Varningsregler** -varning regler nu skiljer sig från aviseringar. En aviseringsregel är definitionerna av ett villkor som utlöser en varning. En avisering är en instans av en regel för varning att.
- **Bättre arbetsflöde** -enhetlig aviseringen upplevelse vid upprättande guidar dig genom processen att konfigurera en aviseringsregel.
 
Mått aviseringar har följande förbättringar över klassiska mått aviseringar:

-   **Förbättrad latens**: mått aviseringar kan köras så ofta som var en minut. Klassiska mått aviseringar körs alltid en frekvens på 5 minuter. Logga varningar fortfarande ha en fördröjning som är längre än en minut på grund av tiden är tar att mata in loggarna. 
-   **Stöd för flerdimensionella mått**: du kan meddela om dimensionell mått så att du kan övervaka en specifik instans av måttet.
-   **Mer kontroll över mått villkor**: du kan definiera bättre Varningsregler som stöd för övervakning av högsta minsta, genomsnittlig och totala värden för mått.
-   **Kombinerade övervakning av flera mått**: du kan övervaka upp till två med en enskild regel. En varning aktiveras om båda mått bryta mot sina respektive tröskelvärden för den angivna tidsperioden.
-   **Mått från loggar** (begränsad förhandsversion): vissa logga data hamnar i logganalys nu kan extraheras och konverteras till Azure-Monitor mått och sedan visas på samma sätt som andra mått. 


## <a name="alert-rules"></a>Varningsregler
Enhetlig aviseringar upplevelsen använder följande begrepp för att separera Varningsregler från aviseringar när enhetlig redigering användarupplevelse av alla olika aviseringstyper.

| Objekt | Definition |
|:---|:---|
| Varningsregel | Definition av villkoret för att skapa en avisering. Består av en _mål resurs_, _signal_, _kriterier_, och _logik_. En aviseringsregel är endast aktiv om den är i ett _aktiverat_ tillstånd.
| Målresurs | Definierar de enskilda resurserna och signalerar till tillgängliga för aviseringar. Ett mål kan vara Azure-resurser.<br>Exempel: virtuell dator, storage-konto, skaluppsättning för virtuell dator, logganalys-arbetsytan, Application Insights-resurs |
| Signal | Källan för data som sänds av målresursen. Signaltyper som stöds är *mått*, *aktivitetsloggen*, *Programinsikter*, och *loggen*. |
| Kriterie | Kombinationen av _signal_ och _logik_ tillämpas på en målresurs.<br>Exempel: Procent CPU > 70%, serversvarstid > 4 ms, antal resultat av en logg fråga > 100 osv. |
| Logik | Användardefinierade logik för att kontrollera om signalen förväntades intervallvärden /. |
| Åtgärd | Åtgärd som ska utföras när aviseringen utlöses. Flera åtgärder kan uppstå när en avisering utlöses. Dessa aviseringar stöd för åtgärdsgrupper.<br>Exempel: e-posta en e-postadress som anropar en Webhooksadressen. |
| Övervakningsvillkor | Anger om det villkor som skapas en avisering om mått senare har lösts. Mått Varningsregler exempel ett viss mått med jämna mellanrum. Om villkoren i regeln är uppfyllt, skapas en ny avisering med ett villkor för Fired.  När måttet samplas igen, om villkoret är uppfyllt fortfarande sedan händer ingenting.  Om villkoren inte uppfylls om ändras villkoret för aviseringen till löst. Nästa gång som villkoret är uppfyllt sedan en ny avisering skapas med ett villkor för Fired. |


## <a name="alert-pages"></a>Aviseringen sidor
Enhetlig aviseringar ger en enda plats om du vill visa och hantera alla dina Azure-aviseringar. I följande avsnitt beskrivs funktionerna i varje enskild sida enhetlig miljö.

### <a name="alerts-overview-page"></a>Aviseringsöversikt
**Aviseringar** översiktssidan visas en aggregerade sammanfattning av alla Eldad aviseringar och det totala antalet aktiverat Varningsregler. Ändra prenumerationer eller filterparametrarna uppdaterar aggregat och aviseringarna utlöses lista.

 ![Översikt över aviseringar](./media/monitoring-overview-unified-alerts/alerts-preview-overview2.png) 

### <a name="alert-rules-management"></a>Varningsregler management
**Regler** är en enstaka sida för att hantera alla Varningsregler över dina Azure-prenumerationer. Den listar alla Varningsregler och sorteras utifrån target-resurser, resursgrupper, regelnamn eller status. Varningsregler kan också vara redigerade nd aktiveras eller inaktiveras från den här sidan.

 ![regler för aviseringar](./media/monitoring-overview-unified-alerts/alerts-preview-rules.png)


## <a name="creating-an-alert-rule"></a>Skapa en aviseringsregel
Aviseringar kan skrivas i ett konsekvent sätt oavsett övervakningstjänsten eller skicka en signal typen. Alla utlöses aviseringar och relaterad information finns i en sida.
 
Du kan skapa en ny avisering regel med följande tre steg:
1. Välj den _mål_ för aviseringen.
1. Välj den _signal_ från de tillgängliga signalerna för målet.
1. Ange den _logik_ ska tillämpas på data från signalen.
 
Förenklad redigering processen kräver inte längre att användaren vet övervakning käll- eller signaler som stöds innan du väljer en Azure-resurs. Listan över tillgängliga signalerar filtreras automatiskt baserat på valda målresurs och hjälper dig att definiera logiken i regeln.

Du kan lära dig mer om hur du skapar Varningsregler i [skapa, visa och hantera aviseringar via Azure-Monitor](monitor-alerts-unified-usage.md).

Aviseringar är tillgänglig över flera Azure övervaka tjänster. Information om hur och när du använder var och en av dessa tjänster finns [övervakning Azure-program och resurser](./monitoring-overview.md). Följande tabell innehåller en lista över vilka typer av Varningsregler tillgängliga över Azure och vad stöds för närvarande av enhetlig avisering miljö.

| **Övervakare för källa** | **Typen av signal**  | **Beskrivning** | 
|-------------|----------------|-------------|
| Övervakare för Azure | Mått  | Kallas även [nära realtid mått aviseringar](monitoring-near-real-time-metric-alerts.md), de stöder utvärderar mått villkor så ofta som en minut och tillåter flera mått och flerdimensionellt mått regler. En lista över resurstyper som stöds finns i [nyare mått aviseringar för Azure-tjänster i Azure portal](monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported).<br>[Klassiska mått aviseringar](monitoring-overview-alerts.md) stöds inte i den nya upplevelsen för aviseringar. Du hittar dem under aviseringar (klassisk) i Azure-portalen. Klassiska aviseringar stöd för vissa typer av mått som ännu inte har flyttats till de nya aviseringarna. En fullständig lista finns [stöds mått](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-supported-metrics). |
| Log Analytics | Logs  | Ta emot meddelanden eller köra automatiserade åtgärder när en logg sökfråga uppfyller vissa villkor. Aviseringar i logganalys [kopieras till den nya upplevelsen](monitoring-alerts-extend.md). En [förhandsgranskning av *logganalys loggar som mått* ](monitoring-alerts-extend-tool.md) är tillgänglig. Förhandsversionen kan du vidta vissa typer av loggar och omvandla dem till statistik, där du kan sedan meddela på dem med hjälp av den nya aviseringsdata upplevelsen. Förhandsgranskningen är användbart om du har Azure-loggar som du vill hämta tillsammans med intern Azure-Monitor mått. |
| Aktivitetsloggar | Aktivitetslogg | Innehåller poster för alla skapa, uppdatera och ta bort åtgärder som skapats av det valda målet. |
| Service Health | Aktivitetslogg  | Stöds inte i enhetlig aviseringar. Se [Skapa aktivitet loggen aviseringar på tjänstmeddelanden](monitoring-activity-log-alerts-on-service-notifications.md).  |
| Application Insights | Logs  | Innehåller loggar med prestandainformation om programmets. Du kan definiera villkoren för åtgärder som ska utföras baserat på programdata med analytics-fråga. |
| Application Insights | Mått | Stöds inte i enhetlig aviseringar. Se [mått aviseringar]. (.. /Application-Insights/App-Insights-Alerts.MD) |
| Application Insights | Tillgänglighetstester för webbprogram | Stöds inte i enhetlig aviseringar.  Se [Web test varningar](../application-insights/app-insights-monitor-web-app-availability.md). Tillgänglig för alla webbplatser som instrumenterats ska skicka data till Application Insights. Få ett meddelande när tillgänglighet och svarstider på en webbplats som är lägre än förväntningar. |

## <a name="enhanced-unified-alerts-public-preview"></a>Förbättrad enhetlig aviseringar (förhandsversion)
> [!NOTE]
>  Funktioner i det här avsnittet kommer snart. Den kan inte visas i din version av portalen ännu. 

En förbättrad enhetlig aviseringar upplevelse gavs ut som förhandsversion för Azure-Monitor den 1 juni 2018. Det här upplevelsen bygger på fördelarna med [unified aviseringar](#overview) ut mars 2018 och ger dig möjlighet att hantera och sammanställa enskilda aviseringar förutom att ändra tillstånd för avisering. Det här avsnittet beskrivs de nya funktionerna och hur du navigerar nya aviseringen sidor i Azure-portalen.

### <a name="features-enhanced-unified-alerts"></a>Funktioner som förbättrad unified aviseringar

Den nya miljön innehåller följande funktioner som inte är tillgängliga i den klassiska enhetlig miljön:

- **Visa aviseringar från alla prenumerationer** -du kan nu visa och hantera enskilda instanser av varningar över flera prenumerationer i en enda vy.
- **Hantera tillståndet för aviseringar** -aviseringar har nu ett tillstånd som anger om den har bekräftas för stängd.
- **Ordna aviseringar med Smart grupper** -Smart grupper automatiskt gruppera relaterade aviseringar så att du kan hantera dem som en uppsättning i stället för individuellt.

### <a name="enable-enhanced-unified-alerts"></a>Aktivera utökad enhetlig aviseringar
Aktivera den nya upplevelsen för enhetlig avisering genom att klicka på banderoll överst på sidan aviseringar. Den här processen skapar en avisering butik som innehåller de senaste 30 dagarna av Eldad aviseringar över tjänster som stöds. När den nya upplevelsen har aktiverats kan du växla fram och tillbaka mellan nya och gamla upplevelsen genom att klicka på listen.

> [!NOTE]
>  Det kan ta några minuter för den nya upplevelsen aktiveras först.

![Banderoll](media/monitoring-overview-unified-alerts/opt-in-banner.png)

När du aktiverar den nya upplevelsen kommer den att registreras alla prenumerationer som du har åtkomst till. Även om hela prenumerationen aktiveras kommer endast användare som väljs den nya upplevelsen att kunna visa den. Andra användare med åtkomst till prenumerationen måste separat aktivera upplevelsen.

Aktivera avisering avanmäla påverkar inte konfigurationen av åtgärdsgrupper eller meddelanden i din Varningsregler. Detta ändrar bara hur du visa och hantera Eldad instanser av aviseringar i Azure-portalen.

### <a name="smart-groups"></a>Smart grupper
Smart grupper minska bruset genom att du kan hantera relaterade aviseringar som en enhet i stället för hantera enskilda aviseringar. Du kan visa information om smarta grupper och ställa in tillståndet liknar en avisering. Varje avisering är medlem i en smart grupp.

Smart grupper skapas automatiskt med hjälp av maskininlärning att kombinera relaterade aviseringar som representerar ett enda problem. När en avisering skapas, läggs algoritmen till en ny smart grupp eller en befintlig smart grupp baserat på information som historiska mönster, likartade egenskaper och likartade struktur. Algoritmen anser för närvarande bara aviseringar från samma Övervakare tjänsten inom en prenumeration. Smart grupper kan minska upp till 99% av aviseringen brus i denna konsolidering. Du kan visa orsaken till att aviseringar ingick i en grupp i detaljsida Smart grupp.

Namnet på en smart grupp är namnet på den första aviseringen. Du kan inte skapa eller byta namn på ett smart grupp.


### <a name="alert-states"></a>Tillstånd för avisering
Förbättrad enhetlig aviseringar begreppet tillstånd för avisering. Du kan ange tillstånd för en avisering för att ange om det är i lösningsprocessen.  När en avisering skapas, har den statusen *ny*. Du kan ändra status när du har godkänt en avisering och när du har stängt. Tillståndsändringar lagras i historiken för aviseringen.

Dessa aviseringstillstånd stöds.

| Status | Beskrivning |
|:---|:---|
| Ny | Bara problemet har identifierats och ännu inte granskad. |
| Bekräftad | En administratör har granskat aviseringen och igång på den. |
| Stängd | Problemet har lösts. När en avisering har stängts kan öppna du det min ändra till ett annat tillstånd. |

Tillståndet för en avisering skiljer sig övervakarens villkor. Mått Varningsregler kan ange en avisering till ett villkor för _löst_ när felet är inte längre uppfyllt. Tillstånd för avisering anges av användaren och är oberoende av övervakarens villkor. Även om systemet kan ange villkoret Övervakaren för att matcha, ändras inte tillstånd för avisering förrän användaren ändrar den.

#### <a name="changing-the-state-of-an-alert-or-smart-group"></a>Ändra tillstånd för en avisering eller smart grupp
Du kan ändra status för en enskild varning eller hantera flera aviseringar tillsammans genom att ställa in tillståndet för en smart grupp.

Ändra tillstånd för en avisering genom att klicka på **ändra tillstånd för avisering** i vyn Detaljer för aviseringen eller ändra tillståndet för en smart grupp genom att klicka på **ändra smart tillstånd** i vyn Detaljer. Du kan ändra tillståndet för flera objekt i taget genom att markera dem som en lista och klicka på **ändring av tillstånd** överst på sidan. Välj ett nytt tillstånd i listrutan och du kan också ange en kommentar i båda fallen. Om du vill ändra ett enstaka objekt ha du också ett alternativ för att tillämpa samma ändringarna alla aviseringar i gruppen smart.

![Ändra tillstånd](media/monitoring-overview-unified-alerts/change-tate.png)

### <a name="alerts-page"></a>Sidan varningar
Aviseringar standardsida ger en översikt över aviseringar som skapas inom ett visst tidsintervall. Totalt antal aviseringar för varje allvarlighetsgrad visas med kolumner som identifierar det totala antalet aviseringar i varje läge för varje allvarlighetsgrad. Klicka på någon av de allvarlighetsgraderna att öppna den [alla aviseringar](#all-alerts-page) sidan filtrerad efter den allvarlighetsgraden.

![Sidan varningar](media/monitoring-overview-unified-alerts/alerts-page.png)

Du kan filtrera den här vyn genom att välja värden i den nedrullningsbara listorna överst på sidan.

| Kolumn | Beskrivning |
|:---|:---|
| Prenumeration | Välj upp till 5 Azure-prenumerationer. Bara aviseringar i de valda prenumerationerna ingår i vyn. |
| Resursgrupp | Välj en enskild resursgrupp. Endast aviseringar med mål i den valda resursgruppen tas med i vyn. |
| Tidsintervall | Bara de aviseringar som utlöses i det markerade tidsfönstret tas med i vyn. Värden som stöds är efter timme, senaste 24 timmarna, de senaste 7 dagarna och de senaste 30 dagarna. |

Klicka på följande värden överst på sidan aviseringar för att öppna en annan sida.

| Värde | Beskrivning |
|:---|:---|
| Antal aviseringar | Totalt antal aviseringar som matchar de valda villkoren. Klicka på det här värdet om du vill öppna vyn alla varningar med några filter. |
| Smart grupper | Totalt antal smarta grupper som skapas från aviseringar som matchar de valda villkoren. Klicka på det här värdet om du vill öppna listan Smart grupper i vyn alla aviseringar.
| Totalt antal aviseringsregler | Totalt antal Varningsregler i den valda gruppen för prenumeration och resurs. Klicka på det här värdet om du vill öppna vyn regler filtrerade på den valda prenumerationer och resursgruppen.


### <a name="all-alerts-page"></a>Sidan alla varningar 
Sidan alla aviseringar kan du visa en lista över aviseringar som har skapats i den valda tidsperioden. Du kan antingen visa en lista med enskilda varningar eller en lista över smarta grupper som innehåller aviseringarna. Klicka på banderoll överst på sidan för att växla mellan lägen.

![Sidan alla varningar](media/monitoring-overview-unified-alerts/all-alerts-page.png)

Du kan filtrera vyn genom att välja följande värden i den nedrullningsbara listorna överst på sidan.

| Kolumn | Beskrivning |
|:---|:---|
| Prenumeration | Välj upp till 5 Azure-prenumerationer. Bara aviseringar i de valda prenumerationerna ingår i vyn. |
| Resursgrupp | Välj en enskild resursgrupp. Endast aviseringar med mål i den valda resursgruppen tas med i vyn. |
| Resurstyp | Välj en eller flera resurstyper. Endast aviseringar med mål för den valda typen ingår i vyn. Den här kolumnen är endast tillgängligt när du har angett en resursgrupp. |
| Resurs | Välj en resurs. Endast aviseringar med resurser som mål tas med i vyn. Den här kolumnen är endast tillgängligt när du har angett en resurstyp. |
| Severity | Välj en allvarlighetsgrad eller *alla* att inkludera aviseringar med alla grader. |
| Övervakningsvillkor | Välj ett villkor för Övervakare eller *alla* att inkludera aviseringar av villkor. |
| Aviseringstillstånd | Välj en avisering tillstånd eller välja *alla* att inkludera aviseringar av tillstånd. |
| Övervaka tjänst | Välj en tjänst eller *alla* att inkludera alla tjänster. Endast de aviseringar som skapats av regler som använder tjänsten som mål ingår. |
| Tidsintervall | Bara de aviseringar som utlöses i det markerade tidsfönstret tas med i vyn. Värden som stöds är efter timme, senaste 24 timmarna, de senaste 7 dagarna och de senaste 30 dagarna. |

Klicka på **kolumner** längst upp på sidan för att välja vilka kolumner som ska visas. Du kan ta bort alla kolumner utom för 

### <a name="alert-detail-page"></a>Aviseringsinformation sida
Den här aviseringen sidan visas när du klickar på en avisering. Den innehåller information om aviseringen och kan du ändra dess tillstånd.

![Aviseringsinformation](media/monitoring-overview-unified-alerts/alert-detail.png)

Sidan aviseringsinformation innehåller följande avsnitt.

| Section | Beskrivning |
|:---|:---|
| Essentials | Visar egenskaper och annan viktig information om aviseringen. |
| Historik | Visar alla åtgärder som vidtas av aviseringen och ändringar som görs till aviseringen. Det här är för tillfället begränsad till tillståndet ändras. |
| Smartkort-grupp | Information om gruppen smart aviseringen ingår i. Den **antalet aviseringar** avser antalet aviseringar som ingår i gruppen smart. Detta omfattar de andra aviseringar som ingår i samma smart gruppen som skapades under de senaste 30 dagarna.  Det här är oberoende av tidsfiltret i sidan aviseringar. Klicka på en avisering för att visa dess detaljer. |
| Mer information | Visar mer relevant information för aviseringen som är vanligtvis är specifik för typ av datakälla som skapade aviseringen. |


### <a name="smart-group-detail-page"></a>Smart grupp detaljsida
Detaljsida för Smart grupp visas när du klickar på en smart grupp. Den innehåller information om gruppen smart, inklusive det resonemang som används för att skapa gruppen, och kan du ändra dess tillstånd.
 
![Information om smartkort grupp](media/monitoring-overview-unified-alerts/smart-group-detail.png)


Detaljsida för Smart grupp innehåller följande avsnitt.

| Section | Beskrivning |
|:---|:---|
| Aviseringar | Listar de enskilda aviseringar som ingår i gruppen smart. Klicka på en avisering för att öppna dess detaljsida för aviseringen. |
| Historik | Visar varje åtgärd som smart gruppen och eventuella ändringar. Det här är för tillfället begränsad till tillståndsändringar av och aviseringar medlemskap. |

## <a name="next-steps"></a>Nästa steg
- [Lär dig hur du använder den nya upplevelsen aviseringar för att skapa, visa och hantera aviseringar](monitor-alerts-unified-usage.md)
- [Lär dig mer om loggen aviseringar i aviseringar upplevelsen](monitor-alerts-unified-log.md)
- [Lär dig mer om mått aviseringar i aviseringar upplevelsen](monitoring-near-real-time-metric-alerts.md)
- [Lär dig mer om aktiviteten loggen aviseringar i aviseringar upplevelsen](monitoring-activity-log-alerts-new-experience.md)
