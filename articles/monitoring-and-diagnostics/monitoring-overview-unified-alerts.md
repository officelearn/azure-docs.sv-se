---
title: Enhetlig aviseringar i Azure Monitor
description: Beskrivning av enhetlig aviseringar i Azure så att du kan hantera aviseringar och varnar regler för Azure-tjänster.
author: manishsm-msft
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: mamit
ms.component: alerts
ms.openlocfilehash: c4c8279a1d4638a1c5d889b53e2d9e89e458cc37
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39117618"
---
# <a name="unified-alerts-in-azure-monitor"></a>Enhetlig aviseringar i Azure Monitor

## <a name="overview"></a>Översikt

> [!NOTE]
>  En ny enhetlig avisering miljö där du kan hantera varningar från flera prenumerationer och introducerar alert tillstånd och smart grupper är för närvarande tillgängligt som offentlig förhandsversion. Se det sista avsnittet i den här artikeln finns en beskrivning av det här förbättrad upplevelse och processen för att aktivera den.


Den här artikeln beskriver enhetlig avisering upplevelse i Azure Monitor. Den [tidigare avisering erfarenhet](monitoring-overview-alerts.md) är tillgänglig från den **aviseringar (klassisk)** alternativet Azure Monitor-menyn. 

## <a name="features-of-the-unified-alert-experience"></a>Funktioner för enhetlig avisering upplevelse

Enhetlig upplevelse har följande fördelar över klassiska upplevelse:

-   **Bättre meddelandesystem**: [åtgärdsgrupper](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) är namngivna grupper av meddelanden och åtgärder som kan återanvändas i flera aviseringar. 
- **Enhetlig skaparupplevelse**: aviseringar och Varningsregler för mått, loggar och aktivitetsloggar i Azure Monitor, Log Analytics och Application Insights kan hanteras på samma ställe. 
- **Visa utlösta Log Analytics-aviseringar i Azure-portalen**: Log Analytics-aviseringar kan nu visas med aviseringar från andra källor i Azure-portalen. Tidigare var aviseringar från andra källor i en separat portal.
- **Separation av aktiverade aviseringar och Varningsregler**: Varningsregler nu skiljer sig från aviseringar. En aviseringsregel är definitionen av ett villkor som utlöser en avisering. En avisering är en instans av en aviseringsregel den utlösts.
- **Bättre arbetsflöde**: enhetlig aviseringen skaparupplevelse vägleder dig genom processen med att konfigurera en aviseringsregel.
 
Måttaviseringar har följande förbättringar över klassiska måttaviseringar:

-   **Förbättrad latens**: måttaviseringar kan köras så ofta som en gång i minuten. Klassiska måttaviseringar körs alltid en frekvens på 15: e minut. Loggaviseringar fortfarande ha en fördröjning som är längre än en minut på grund av den tid det tar att mata in loggarna. 
-   **Stöd för flerdimensionella mått**: du kan meddela på endimensionella mått, vilket innebär att du kan övervaka en specifik instans av måttet.
-   **Mer kontroll över mått villkor**: du kan definiera större Varningsregler som stöd för övervakning av högsta, lägsta, genomsnittlig och total värdena för mått.
-   **Kombineras övervakning av flera mått**: du kan övervaka upp till två mått med en enda regel. En avisering utlöses om båda mått bryta mot deras respektive tröskelvärden för den angivna tidsperioden.
-   **Mått från loggar** (begränsad offentlig förhandsversion): vissa log data som skickas till Log Analytics kan nu extraheras och omvandlas till Azure Monitor-mått och sedan ett meddelande om precis som andra mått. 


## <a name="alert-rules"></a>Varningsregler
Enhetlig aviseringarna använder följande begrepp för att separera Varningsregler från aviseringar när enhetlig redigeringen av alla olika aviseringstyper.

| Objekt | Definition |
|:---|:---|
| Varningsregel | Definition av villkoret för att skapa en avisering. En varningsregel består av en _målresurs_, _signal_, _kriterier_, och _logic_. En aviseringsregel är endast aktiv om den tillhör en _aktiverat_ tillstånd.
| Målresurs | Definierar den specifika resurser och signalerar som är tillgängliga för aviseringar. Ett mål kan vara en Azure-resurs.<br><br>Exempel: virtuell dator, storage-konto, virtual machine scale Sets, Log Analytics-arbetsyta, Application Insights-resurs |
| Signal | Källan för data som skickas från målresursen. Stöds signaltyperna är *mått*, *aktivitetsloggen*, *Programinsikter*, och *Log*. |
| Kriterie | Kombinationen av _signal_ och _logic_ tillämpas på en målresurs.<br><br>Exempel: Procent CPU > 70%, serversvarstid > 4 ms, antal resultat av en logg fråga > 100 och så vidare |
| Logik | Användardefinierade logik för att kontrollera att signalen inom förväntades i intervallvärden. |
| Åtgärd | Åtgärd som ska utföras när aviseringen utlöses. Flera åtgärder kan inträffa när en avisering utlöses. Dessa aviseringar stöder åtgärdsgrupper.<br><br>Exempel: skicka e-post för att e-postadress, anropa en webhook-URL |
| Övervakningsvillkor | Anger om det villkor som skapade en metrisk varning har lösts. Måttvarningsregler som exempel på ett visst mått med jämna mellanrum. Om villkoret i regeln uppfylls, skapas en ny avisering med ett villkor för ”utlösta”.  Sedan när måttet samplas igen, om villkoret uppfylls, händer ingenting.  Om villkoren inte uppfylls, sedan ändras villkoret för aviseringen till ”matcha”. Vid nästa tillfälle som villkoret uppfylls, skapas en ny avisering med ett villkor för ”utlösta”. |


## <a name="alert-pages"></a>Aviseringen sidor
Enhetlig aviseringar ger kan du visa och hantera alla dina Azure-aviseringar. I följande avsnitt beskrivs funktionerna i varje enskild sida för enhetlig upplevelse.

### <a name="alerts-overview-page"></a>Aviseringsöversikt
Den **aviseringar** översiktssida visar en sammansatt sammanfattning av alla aktiverade aviseringar och det totala antalet aktiverade aviseringsregler. Ändra prenumerationer eller filterparametrar uppdaterar aggregeringar och aviseringar utlöstes lista.

 ![Översikt över aviseringar](./media/monitoring-overview-unified-alerts/alerts-preview-overview2.png) 

### <a name="alert-rules-management"></a>Varningsregler management
**Regler** är en enskild sida för att hantera alla aviseringsregler för alla Azure-prenumerationer. Den visar en lista över alla aviseringsregler och kan sorteras utifrån target-resurser, resursgrupper, regelnamn eller status. Varningsregler kan även redigeras, aktiveras eller inaktiveras från den här sidan.

 ![regler för aviseringar](./media/monitoring-overview-unified-alerts/alerts-preview-rules.png)


## <a name="create-an-alert-rule"></a>Skapa en varningsregel
Aviseringar kan skrivas i ett konsekvent sätt oavsett övervakningstjänsten eller skicka en signal typen. Alla utlösta aviseringar och relaterade detaljer är tillgängliga i en sida.
 
Du kan skapa en ny aviseringsregel med följande tre steg:
1. Välj den _target_ för aviseringen.
1. Välj den _signal_ från de tillgängliga signalerna för målet.
1. Ange den _logic_ som ska tillämpas på data från signalen.
 
Förenklad redigering processen kräver inte längre att du enkelt se övervakningen käll- eller signaler som stöds innan du väljer en Azure-resurs. Listan över tillgängliga signaler filtreras automatiskt baserat på vilken resurs som du väljer och det hjälper dig att definiera logiken i regeln.

Du kan lära dig mer om hur du skapar aviseringsregler i [skapa, visa och hantera aviseringar med hjälp av Azure Monitor](monitor-alerts-unified-usage.md).

Aviseringar är tillgängliga i flera Azure-övervakningstjänster. Information om hur och när du ska använda var och en av dessa tjänster finns i [övervakning av Azure-program och resurser](./monitoring-overview.md). I följande tabell innehåller en lista över typerna av Varningsregler som är tillgängliga i Azure. Den visar också vad stöds för närvarande av enhetlig avisering upplevelse.

| **Övervaka källan** | **Signaltyp**  | **Beskrivning** | 
|-------------|----------------|-------------|
| Azure Monitor | Mått  | Kallas även [måttaviseringar i nära realtid](monitoring-near-real-time-metric-alerts.md), de stöd för utvärdering av mått villkor så ofta som en gång i minuten och tillåter flera mått och flerdimensionella mått regler. En lista över resurstyper som stöds finns i [nyare måttaviseringar för Azure-tjänster i Azure-portalen](monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported).<br>[Klassiska måttaviseringar](monitoring-overview-alerts.md) stöds inte i det nya aviseringsgränssnittet. Du hittar dem under aviseringar (klassisk) i Azure-portalen. Klassiska aviseringar stöd för vissa typer av mått som ännu inte har flyttats till nyare aviseringar. En fullständig lista finns i [stöds mått](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-supported-metrics). |
| Log Analytics | Logs  | Ta emot meddelanden eller kör automatiserade åtgärder när en logga sökfråga uppfyller vissa villkor. Aviseringar i Log Analytics är [kopieras till den nya upplevelsen](monitoring-alerts-extend.md). En [förhandsgranskning av *Log Analytics loggar som mått* ](monitoring-alerts-extend-tool.md) är tillgänglig. Förhandsversionen kan du vidta vissa typer av loggar och konvertera dem till mått, där du kan sedan meddela dem med hjälp av den nya upplevelsen med aviseringar. Förhandsgranskningen är användbart om du har icke-Azure-loggar som du vill hämta tillsammans med inbyggda Azure Monitor-mått. |
| Aktivitetsloggar | Aktivitetslogg | Innehåller poster för alla skapa, uppdatera och ta bort åtgärder som har skapats av det valda målet. |
| Service Health | Aktivitetslogg  | Stöds inte i enhetlig aviseringar. Se [skapa aviseringar för aktivitetsloggen för tjänstmeddelanden](monitoring-activity-log-alerts-on-service-notifications.md).  |
| Application Insights | Logs  | Innehåller loggar med prestandainformation om programmets. Med analytics-fråga kan definiera du villkor för åtgärder som ska utföras baserat på programdata. |
| Application Insights | Mått | Stöds inte i enhetlig aviseringar. Se [måttaviseringar](../application-insights/app-insights-alerts.md). |
| Application Insights | Webbtester för tillgänglighet | Stöds inte i enhetlig aviseringar.  Se [webbtestaviseringar](../application-insights/app-insights-monitor-web-app-availability.md). Tillgänglig för alla webbplatser som är utrustade för att skicka data till Application Insights. Få ett meddelande när tillgänglighet och svarstider på en webbplats som är lägre än förväntningar. |

## <a name="enhanced-unified-alerts-public-preview"></a>Förbättrad enhetlig aviseringar (förhandsversion)

En förbättrad enhetlig aviseringsgränssnittet gavs ut i offentlig förhandsversion för Azure Monitor den 1 juni 2018. Den här upplevelsen bygger på fördelarna med [unified aviseringar](#overview)som släpptes i mars 2018 och som ger möjlighet att hantera och aggregera enskilda aviseringar och ändra aviseringstillståndet. Det här avsnittet beskriver de nya funktionerna och hur du navigerar de nya aviseringarna sidorna i Azure-portalen.

### <a name="enhanced-unified-alerts"></a>Förbättrad enhetlig aviseringar

Den nya guiden innehåller följande funktioner som inte är tillgängliga i den klassiska enhetlig upplevelsen:

- **Visa aviseringar för prenumerationer**: du kan nu visa och hantera enskilda instanser av aviseringar över flera prenumerationer i en enda vy.
- **Hantera tillståndet för aviseringar**: aviseringar har nu ett tillstånd som anger om de har godkänts som stängda.
- **Organisera aviseringar med smarta grupper**: Smart grupper automatiskt gruppera relaterade aviseringar så att du kan hantera dem som en grupp i stället för enskilt.

### <a name="enable-enhanced-unified-alerts"></a>Aktivera förbättrad enhetlig aviseringar
Aktivera den nya upplevelsen för enhetlig aviseringar genom att markera banderollen högst upp på aviseringssidan. Den här processen skapar en avisering butik som innehåller de senaste 30 dagarna av aktiverade aviseringar över tjänster som stöds. När den nya upplevelsen har aktiverats kan växla du fram och tillbaka mellan nya och gamla upplevelsen genom att välja banderollen.

> [!NOTE]
>  Det kan ta några minuter innan den nya upplevelsen aktiveras först.

![Banderoll](media/monitoring-overview-unified-alerts/opt-in-banner.png)

Alla prenumerationer som du har åtkomst till registreras när du aktiverar den nya upplevelsen. Även om hela prenumerationen aktiveras kan endast användare som väljer den nya upplevelsen visa den. Andra användare med åtkomst till prenumerationen måste separat aktivera upplevelsen.

Att aktivera den nya upplevelsen för aviseringen påverkar inte konfigurationen av åtgärdsgrupper eller meddelanden i dina aviseringsregler. Detta ändrar bara hur du visa och hantera aktiverade instanser av aviseringar i Azure-portalen.

### <a name="smart-groups"></a>Smart grupper
Smart grupper minska bruset genom att låta dig hantera relaterade aviseringar som en enhet i stället för enskilda aviseringar. Du kan visa information om smart grupper och ställa in tillståndet på samma sätt till hur du kan med aviseringar. Varje avisering är medlem i en smart gruppen.

Smart grupper skapas automatiskt med hjälp av maskininlärning att kombinera relaterade aviseringar som representerar ett enda problem. När en avisering skapas, läggs algoritmen det till en ny smart grupp eller en befintlig smart grupp baserat på information som historiska mönster, liknande egenskaper och liknande struktur. 

För närvarande algoritmen som endast tar hänsyn till aviseringar från samma Övervakare tjänst inom en prenumeration. Smart grupper kan minska onödig avisering via denna konsolidering med upp till 99%. Du kan visa orsaken till att aviseringar ingick i en grupp i sidan med smarta grupp.

Namnet på en smart grupp är namnet på den första aviseringen. Du kan inte skapa eller byta namn på en smart grupp.


### <a name="alert-states"></a>Aviseringstillstånd
Förbättrad enhetlig aviseringar begreppet aviseringstillståndet. Du kan ange tillståndet för en avisering för att ange var den är i lösningsprocessen. När en avisering skapas, har den statusen *New*. Du kan ändra status när du har godkänt en avisering och när du stänger den. Alla tillståndsändringar lagras i historiken för aviseringen.

Dessa aviseringstillstånd stöds.

| Status | Beskrivning |
|:---|:---|
| Ny | Problemet bara har identifierats och har inte har granskat. |
| Bekräftad | En administratör har granskat aviseringen och mellanrum. |
| Stängd | Problemet har lösts. När en avisering har stängts kan öppna du det genom att ändra det till ett annat tillstånd. |

Tillståndet för en avisering är annorlunda än övervakarens villkor. Måttaviseringsregler kan ställa in en avisering till ett villkor för _löst_ när felet är inte längre uppfyllt. Tillstånd för avisering anges av användaren och är oberoende av övervakarens villkor. Även om systemet kan ställa in övervakarens villkor att ”matcha”, ändras aviseringstillståndet inte förrän användaren ändrar den.

#### <a name="change-the-state-of-an-alert-or-smart-group"></a>Ändra tillståndet för en avisering eller smart-grupp
Du kan ändra tillståndet för en enskild varning eller hantera flera aviseringar tillsammans genom att ställa in tillståndet för en smart grupp.

Ändra tillståndet för en avisering genom att välja **ändra aviseringtillståndet** i en detaljerad vy för aviseringen. Eller ändra tillståndet för en smart grupp genom att välja **ändra smart grupptillstånd** i dess detaljvy. Ändra tillståndet för flera objekt i taget genom att först välja dem i en listvy och sedan välja **ändra tillstånd** överst på sidan. 

I båda fallen väljer du ett nytt tillstånd från den nedrullningsbara menyn. Ange en valfri kommentar. Om du ändrar ett enskilt objekt, har du också ett alternativ för att tillämpa samma ändringar till alla aviseringar i gruppen smart.

![Ändra tillstånd](media/monitoring-overview-unified-alerts/change-tate.png)

### <a name="alerts-page"></a>Sidan varningar
Aviseringar standardsidan ger en översikt över aviseringar som har skapats inom ett visst tidsintervall. Den visar totalt antal aviseringar för varje allvarlighetsgrad med kolumner som identifierar det totala antalet aviseringar i varje status för varje allvarlighetsgrad. Välj någon av allvarlighetsgrader att öppna den [alla aviseringar](#all-alerts-page) sidan filtreras efter den allvarlighetsgraden.

![Sidan varningar](media/monitoring-overview-unified-alerts/alerts-page.png)

Du kan filtrera den här vyn genom att välja värden i listrutorna överst på sidan.

| Kolumn | Beskrivning |
|:---|:---|
| Prenumeration | Välj upp till fem Azure-prenumerationer. Endast varningar i de valda prenumerationerna som ingår i vyn. |
| Resursgrupp | Välj en enskild resursgrupp. Endast aviseringar med mål i den valda resursgruppen ingår i vyn. |
| Tidsintervall | Bara de aviseringar som utlösts inom det valda tidsfönstret ingår i vyn. Värden som stöds är den senaste timmen, de senaste 24 timmarna, de senaste 7 dagarna och de senaste 30 dagarna. |

Välj följande värden överst på sidan aviseringar för att öppna en annan sida.

| Värde | Beskrivning |
|:---|:---|
| Totalt antal aviseringar | Det totala antalet aviseringar som matchar de valda villkoren. Välj det här värdet för att öppna vyn alla aviseringar med inget filter. |
| Smart grupper | Det totala antalet smart grupper som har skapats från de aviseringar som matchar de valda villkoren. Välj det här värdet för att öppna listan över smart i vyn alla aviseringar.
| Totalt antal aviseringsregler | Det totala antalet Varningsregler i den valda prenumerationen och resursgruppen. Välj det här värdet för att öppna vyn regler filtreras på den valda prenumerationen och resursgruppen.


### <a name="all-alerts-page"></a>Alla aviseringssidan 
Genom att använda sidan alla aviseringar kan visa du en lista över aviseringar som har skapats i den valda tidsperioden. Du kan visa en lista över enskilda aviseringar eller en lista över de smarta grupper som innehåller aviseringarna. Välj banderoll överst på sidan för att växla mellan vyer.

![Alla aviseringssidan](media/monitoring-overview-unified-alerts/all-alerts-page.png)

Du kan filtrera vyn genom att välja följande värden i listrutorna överst på sidan.

| Kolumn | Beskrivning |
|:---|:---|
| Prenumeration | Välj upp till fem Azure-prenumerationer. Endast varningar i de valda prenumerationerna som ingår i vyn. |
| Resursgrupp | Välj en enskild resursgrupp. Endast aviseringar med mål i den valda resursgruppen ingår i vyn. |
| Resurstyp | Välj en eller flera resurstyper. Endast aviseringar med mål för det valda ingår i vyn. Den här kolumnen är endast tillgänglig när en resursgrupp har angetts. |
| Resurs | Välj en resurs. Endast aviseringar med den här resursen som ett mål som ingår i vyn. Den här kolumnen är endast tillgänglig när du har angett en resurstyp. |
| Severity | Välj en allvarlighetsgrad för avisering, eller välj *alla* att inkludera aviseringar för alla allvarlighetsgrader. |
| Övervakningsvillkor | Välj ett övervakningsvillkor eller välj *alla* att inkludera aviseringar av villkor. |
| Tillstånd för avisering | Väljer du en aviseringstillståndet eller *alla* att inkludera aviseringar av tillstånd. |
| Övervaka tjänsten | Välj en tjänst, eller välj *alla* att inkludera alla tjänster. Det ingår bara aviseringar som har skapats av regler som använder tjänsten som mål. |
| Tidsintervall | Bara de aviseringar som utlösts inom det valda tidsfönstret ingår i vyn. Värden som stöds är den senaste timmen, de senaste 24 timmarna, de senaste 7 dagarna och de senaste 30 dagarna. |

Välj **kolumner** överst på sidan för att välja vilka kolumner som ska visas. 

### <a name="alert-detail-page"></a>Aviseringsinformation sidan
Sidan aviseringsinformation visas när du väljer en avisering. Det ger information om aviseringen och du kan ändra tillståndet.

![Aviseringsinformation](media/monitoring-overview-unified-alerts/alert-detail.png)

Sidan aviseringsinformation innehåller följande avsnitt.

| Section | Beskrivning |
|:---|:---|
| Essentials | Visar egenskaperna och andra viktiga information om aviseringen. |
| Historik | Visar en lista över varje åtgärd som aviseringen och ändringar som görs till aviseringen. Detta är för närvarande begränsad till tillståndsändringar. |
| Smart grupp | Information om gruppen smart aviseringen ingår i. Den *antal aviseringar* avser antalet aviseringar som ingår i gruppen smart. Detta inkluderar de andra aviseringarna i samma smart grupp som har skapats under de senaste 30 dagarna.  Det här är oavsett tidsfiltret i sidan med listan över aviseringar. Välj en avisering för att visa dess information. |
| Mer detaljer | Visar ytterligare kontextinformation för aviseringen, som vanligtvis gäller för typ av datakälla som skapade aviseringen. |


### <a name="smart-group-detail-page"></a>Smart sidan med information
Sidan med smarta gruppen visas när du väljer en smart grupp. Den innehåller information om gruppen smart, inklusive resonemang som användes för att skapa gruppen och du kan ändra tillståndet.
 
![Smart grupp information](media/monitoring-overview-unified-alerts/smart-group-detail.png)


Sidan med smarta grupp innehåller följande avsnitt.

| Section | Beskrivning |
|:---|:---|
| Aviseringar | Visar en lista över de enskilda aviseringar som ingår i gruppen smart. Välj en avisering för att öppna appens sida aviseringsinformation. |
| Historik | Visar en lista över varje åtgärd som gruppen smart och eventuella ändringar som görs till den. Det här är för närvarande begränsad till tillståndsändringar och avisering Medlemskapsändringar. |

## <a name="next-steps"></a>Nästa steg
- [Lär dig hur du använder det nya aviseringsgränssnittet för att skapa, visa och hantera aviseringar](monitor-alerts-unified-usage.md)
- [Lär dig mer om aviseringar i aviseringarna](monitor-alerts-unified-log.md)
- [Lär dig mer om måttaviseringar i aviseringarna](monitoring-near-real-time-metric-alerts.md)
- [Lär dig mer om aviseringar för aktivitetsloggar i aviseringarna](monitoring-activity-log-alerts-new-experience.md)
