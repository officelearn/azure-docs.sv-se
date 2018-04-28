---
title: Utforska den nya upplevelsen för aviseringar i Azure-Monitor | Microsoft Docs
description: Förstå hur nya aviseringar för enkel och skalbar upplevelse i Azure gör redigering, visa och hantera aviseringar enklare
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
ms.date: 03/23/2018
ms.author: mamit
ms.custom: ''
ms.openlocfilehash: c3622b4699ef532f204231c76aa3436be3676763
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="the-new-alerts-experience-in-azure-monitor"></a>Nya aviseringar uppstår i Azure-Monitor

## <a name="overview"></a>Översikt

> [!NOTE]
> Den här artikeln beskriver nya aviseringar. Äldre klassiska Azure-Monitor aviseringar beskrivs i [översikt över aviseringar i klassiskt](monitoring-overview-alerts.md). 
>
>

Aviseringar har nya upplevelsen. Äldre aviseringar upplevelse är nu under fliken aviseringar (klassisk). Den nya aviseringar miljön har följande fördelar över aviseringar (klassisk)-upplevelse:

-   **Bättre meddelandesystem**: alla nya aviseringar använder [åtgärdsgrupper]( https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups), som är namngiven grupper av meddelanden och åtgärder som kan återanvändas i flera aviseringar.  Använd inte åtgärdsgrupper klassiska mått aviseringar och äldre logganalys-aviseringar. 
- **En enhetlig redigering miljö** – alla Varna skapande för statistik, loggar och aktiviteten logga via Azure-Monitor, Log Analytics och Application Insights finns på en plats. 
- **Visa utlöses logganalys aviseringar i Azure-portalen** -du kan nu också se utlöses logganalys aviseringar i din prenumeration. Tidigare var dessa i en separat portal. 
- **Uppdelning av Fired aviseringar och avisering regler** - avisering regler (definition av villkor som utlöser en avisering) och utlöses aviseringar (en instans av varningsregeln att) särskiljs så vyer för drift och konfiguration är åtskilda.
- **Bättre arbetsflöde** - nya aviseringar redigering upplevelse guider användaren längs processen att konfigurera en aviseringsregel, vilket gör det enklare att identifiera rätt saker att få ett meddelande om.
 
Nyare mått aviseringar har särskilt följande förbättringar:
-   **Förbättrad latens**: nyare mått aviseringar kan köras så ofta som var en minut. Äldre mått aviseringar körs alltid en frekvens på 5 minuter. Loggen aviseringar fortfarande har en längre tid än en minut fördröjningen på grund av tiden är tar att mata in loggarna. 
-   **Stöd för flerdimensionella mått**: du kan meddela om dimensionell mått så att du kan övervaka en intressant segmentet i måttet.
-   **Mer kontroll över mått villkor**: du kan definiera bättre Varningsregler. Nyare aviseringar stöd för övervakning maximala, minsta, genomsnittlig och totala värden för mått.
-   **Kombinerade övervakning av flera mått**: du kan övervaka flera mått (för närvarande högst två mått) med en enskild regel. En varning aktiveras om båda mått bryta mot sina respektive tröskelvärden för den angivna tidsperioden.
-   **Mått från loggar** (begränsad förhandsversion): vissa logga data hamnar i logganalys nu kan extraheras och konverteras till Azure-Monitor mått och sedan visas på samma sätt som andra mått. 



I följande avsnitt beskrivs, i detalj, hur den nya upplevelsen fungerar.

## <a name="alert-rules-terminology"></a>Varningsregler terminologi
Den nya aviseringar upplevelsen använder följande begrepp för att separera Aviseringsregel och utlöste aviseringen objekt när enhetlig redigering användarupplevelse av alla olika aviseringstyper.

- **Rikta resurs** -ett mål kan vara Azure-resurser. Målresurs definierar omfång och signaler som är tillgängliga för aviseringar. Exempel mål: en virtuell dator, ett lagringskonto, en skaluppsättning för virtuell dator, en logganalys-arbetsytan eller Application Insights-resurs.

- **Kriterier** - kriteriet är en kombination av Signal och logik tillämpas på en målresurs. Exempel: Procent CPU > 70%, serversvarstid > 4 ms, antal resultat av en logg fråga > 100 osv. 

- **Signal** – signalerar sänds av målresurs och kan vara av flera typer. **Måttet**, **aktivitetsloggen**, **Programinsikter**, och **loggen** är signaltyper som stöds.

- **Logik** -användardefinierade logik för att kontrollera om signalen förväntade intervall/värden.  
 
- **Åtgärden** – en specifik åtgärd när aviseringen utlöses. Till exempel e-posta en e-postadress eller anropa en Webhooksadressen. Flera åtgärder kan uppstå när en avisering utlöses. Dessa aviseringar stöd för åtgärdsgrupper.  
 
- **Varningsregeln** -villkoret som ska utlösa aviseringen. Regeln samlar in mål- och villkor för aviseringar. Varningsregeln kan vara i ett aktiverat eller inaktiverat tillstånd.
 
    > [!NOTE]
    > Detta skiljer sig från aviseringar (klassisk)-upplevelse där aviseringen representerar både regeln och Eldad aviseringen och därför kan det vara i något av varning, aktiv eller inaktiverad tillstånd.
    >

## <a name="single-place-to-view-and-manage-alerts"></a>Enda plats att visa och hantera aviseringar
Målet aviseringar erfarenhet är att vara den enda platsen att visa och hantera alla dina Azure-aviseringar. Följande avsnitt beskriver funktionerna i varje enskild skärmen i den nya upplevelsen.

### <a name="alerts-overview-page"></a>Aviseringsöversikt
**Övervakaren - aviseringar** översiktssidan visar aggregerade sammanfattning av alla Eldad aviseringar och totalt konfigurerats aktiveras Varningsregler. Den visar även en lista över alla Eldad aviseringar. Ändra prenumerationer eller filterparametrarna uppdaterar aggregat och aviseringarna utlöses lista.

> [!NOTE]
> Eldad aviseringar som visas i aviseringar som är begränsade till stöds mått och aktivitet Logga varningar; Översikt över Azure övervakaren visar antalet Eldad aviseringar, inklusive de på äldre Azure aviseringar

 ![Översikt över aviseringar](./media/monitoring-overview-unified-alerts/alerts-preview-overview2.png) 

### <a name="alert-rules-management"></a>Varningsregler management
**Övervakaren - aviseringar > regler** är en enstaka sida för att hantera alla Varningsregler över dina Azure-prenumerationer. Visar alla Varningsregler (aktiverat eller inaktiverat) och kan sorteras utifrån target-resurser, resursgrupper, regelnamn eller status. Varningsregler kan även aktiveras eller inaktiveras eller redigeras från den här sidan.  

 ![regler för aviseringar](./media/monitoring-overview-unified-alerts/alerts-preview-rules.png)


## <a name="one-alert-authoring-experience-across-all-monitoring-sources"></a>En avisering redigering erfarenheter från alla källor för övervakning
Aviseringar kan skrivas i ett konsekvent sätt oavsett övervakningstjänsten eller signalerar till typen i den nya aviseringar upplevelsen. Alla aviseringar utlöses och relaterad information finns i en sida.  
 
Redigera en avisering är en aktivitet i tre steg där användaren först hämtar ett mål för aviseringen, följt av att välja rätt signalen och sedan ange logiken som ska tillämpas på signalen som en del av regeln. Förenklad redigering processen kräver inte längre att användaren vet övervakning käll- eller signaler som stöds innan du väljer en Azure-resurs. Vanliga redigeringsfunktionerna automatiskt filtrerar listan över tillgängliga signalerar baserat på valda målresurs och hjälper att skapa aviseringen logik

Du kan lära dig mer om hur du skapar följande aviseringstyper [här](monitor-alerts-unified-usage.md).
- Måttaviseringar
- Loggen aviseringar (logganalys)
- Loggen aviseringar (aktivitetsloggar)
- Loggen aviseringar (Application Insights)

 
## <a name="alerts-supported-in-new-experience"></a>Aviseringar som stöds i nya upplevelsen
Aviseringar är tillgänglig över flera Azure övervaka tjänster. Information om hur och när du använder dessa tjänster [finns i den här artikeln](./monitoring-overview.md). Här är en uppdelning av aviseringstyperna över Azure och vad är för närvarande stöds av den nya upplevelsen för aviseringar. 


| **Typen av signal** | **Övervakare för källa** | **Beskrivning** | 
|-------------|----------------|-------------|
| Mått | Övervakare för Azure | Kallas även [nära realtid mått aviseringar](monitoring-near-real-time-metric-alerts.md), de stöder utvärderar mått villkor så ofta som en minut och tillåter flera mått och flerdimensionellt mått regler. En lista över resurstyper som stöds är tillgänglig [här](monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported). |
| Mått | Övervakare för Azure | [Äldre klassiska mått aviseringar](monitoring-overview-alerts.md) stöds inte i den nya upplevelsen för aviseringar. Du hittar dem under aviseringar (klassisk) i Azure-portalen. Klassiska aviseringar stöd för vissa typer av mått som ännu inte har flyttats till de nya aviseringarna. En fullständig lista finns [mått som stöds](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-supported-metrics)
| Logs  | Log Analytics | Ta emot meddelanden eller köra automatiserade åtgärder när en logg sökfråga över mått och/eller händelse data uppfyller vissa villkor. Äldre logganalys aviseringar finns kvar, men är [kopieras till den nya upplevelsen](monitoring-alerts-extend.md). Dessutom kan en [förhandsgranskning av *logganalys loggar som mått* ](monitoring-alerts-extend-tool.md) är tillgänglig. Förhandsversionen kan du vidta vissa typer av loggar och omvandla dem till statistik, där du kan sedan meddela på dem med hjälp av den nya aviseringsdata upplevelsen. Förhandsgranskningen är användbart om du har Azure-loggar som du vill hämta tillsammans med intern Azure-Monitor mått. |
| Aktivitetslogg | Aktivitetsloggar (Allmänt) | Innehåller poster för alla skapa, uppdatera och ta bort åtgärder som utförs via det valda målet (resursen/resursgruppen grupp eller prenumeration). |
| Aktivitetslogg  | Service Health | Stöds inte i den nya aviseringar upplevelsen. Se [Skapa aktivitet loggen aviseringar på tjänstmeddelanden](monitoring-activity-log-alerts-on-service-notifications.md).  |
| Logs  | Application Insights | Innehåller loggar med prestandainformation om programmets. Du kan definiera villkoren för åtgärderna som vidtagits - baserat på programdata med analytics-fråga. |
| Mått | Application Insights | Stöds inte i den nya aviseringar upplevelsen. Se [mått aviseringar](../application-insights/app-insights-alerts.md) |
| Tillgänglighetstester för webbprogram | Application Insights | Stöds inte i aviseringar upplevelse.  Se [Web test varningar](../application-insights/app-insights-monitor-web-app-availability.md). Tillgänglig för alla webbplatser som instrumenterats ska skicka data till Application Insights. Få ett meddelande när tillgänglighet och svarstider på en webbplats som är lägre än förväntningar. |




## <a name="next-steps"></a>Nästa steg
- [Lär dig hur du använder den nya upplevelsen aviseringar för att skapa, visa och hantera aviseringar](monitor-alerts-unified-usage.md)
- [Lär dig mer om loggen aviseringar i aviseringar upplevelsen](monitor-alerts-unified-log.md)
- [Lär dig mer om mått aviseringar i aviseringar upplevelsen](monitoring-near-real-time-metric-alerts.md)
- [Lär dig mer om aktiviteten loggen aviseringar i aviseringar upplevelsen](monitoring-activity-log-alerts-new-experience.md)
