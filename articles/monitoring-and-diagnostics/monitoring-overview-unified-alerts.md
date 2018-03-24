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
ms.date: 02/02/2018
ms.author: mamit
ms.custom: ''
ms.openlocfilehash: bc9d788367ab14751f9f9158ac88149dc420368a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="explore-the-alerts-experience-in-azure-monitor"></a>Utforska aviseringar upplevelse i Azure-Monitor

## <a name="overview"></a>Översikt
 Aviseringar upplevelse i Azure har en ny ser ut och uppdaterade funktionerna. Den nya upplevelsen är tillgänglig från den **aviseringar** under Azure-Monitor på fliken. Följande är några av fördelarna med att använda den nya aviseringar upplevelsen jämfört med aviseringar (klassisk)-upplevelse:

 - **Uppdelning av Fired aviseringar och avisering regler** – i den nya aviseringar upplevelsen avisering regler (definition av villkor som utlöser en avisering) och utlöses aviseringar (en instans av varningsregeln att) särskiljs därför operativa och konfiguration av vyer är åtskilda.
 - **En enhetlig redigering miljö för måttet, aktivitetsloggen och logga varningar** - nya aviseringar redigering upplevelse guider användaren längs processen att konfigurera en aviseringsregel, vilket gör det enklare att identifiera rätt saker att få ett meddelande om.
 - **Visa utlöses logganalys aviseringar i Azure-portalen** -upplevelse i aviseringar, nu kan du också se utlöses logganalys aviseringar i din prenumeration.  
 

I följande avsnitt beskrivs, i detalj, hur den nya upplevelsen fungerar.

## <a name="taxonomy"></a>Taxonomy
Aviseringar upplevelsen använder följande begrepp för att separera Aviseringsregel och utlöste aviseringen objekt när enhetlig redigering användarupplevelse av alla olika aviseringstyper.

- **Rikta resurs** -ett mål kan vara Azure-resurser. Målresurs definierar omfång och signaler som är tillgängliga för aviseringar. Exempel mål: en virtuell dator, ett lagringskonto, en skaluppsättning för virtuell dator, en logganalys-arbetsytan eller Application Insights-resurs.

- **Kriterier** - kriteriet är en kombination av Signal och logik tillämpas på en målresurs. Exempel: Procent CPU > 70%, serversvarstid > 4 ms, antal resultat av en logg fråga > 100 osv. 

- **Signal** – signalerar sänds av målresurs och kan vara av flera typer. Den här förhandsversionen stöder **mått**, **aktivitetsloggen**, **Programinsikter** och **loggen** som signaltyper.

- **Logik** -användardefinierade logik för att kontrollera om signalen förväntade intervall/värden.  
 
- **Åtgärden** -ett specifikt anrop som skickas till en mottagare av ett meddelande (till exempel e-posta en adress eller bokföring till en Webhooksadressen). Meddelanden kan vanligtvis utlösa flera åtgärder. Aviseringstyper som stöds i den här förhandsgranskningen supportgrupper för åtgärden.  
 
- **Varningsregeln** -definitionen av ett villkor som ska utlösa aviseringen. I den här förhandsgranskningen avisering regeln samlar in mål- och villkor för aviseringar. Varningsregeln kan vara i ett aktiverat eller inaktiverat tillstånd.
 
    > [!NOTE]
    > Detta skiljer sig från aviseringar (klassisk)-upplevelse där aviseringen representerar både regeln och Eldad aviseringen och därför kan det vara i något av varning, aktiv eller inaktiverad tillstånd.
    >

## <a name="single-place-to-view-and-manage-alerts"></a>Enda plats att visa och hantera aviseringar
Målet av aviseringar (förhandsversion) är att vara den enda platsen att visa och hantera alla dina Azure-aviseringar. Följande avsnitt beskriver funktionerna i varje enskild skärmen i den nya upplevelsen.

### <a name="alerts-preview-overview-page"></a>Aviseringsöversikt (förhandsgranskning)
**Övervakaren - aviseringar (förhandsgranskning)** översiktssidan visar aggregerade sammanfattning av alla Eldad aviseringar och totalt konfigurerats aktiveras Varningsregler. Den visar även en lista över alla Eldad aviseringar. Ändra prenumerationer eller filterparametrarna uppdaterar aggregat och aviseringarna utlöses lista.

> [!NOTE]
> Eldad aviseringar som visas i aviseringar som är begränsade till stöds mått och activitylog aviseringar; Översikt över Azure övervakaren visar antalet brand aviseringar, inklusive de på äldre Azure aviseringar

 ![Översikt över aviseringar](./media/monitoring-overview-unified/alerts-preview-overview.png) 

### <a name="alert-rules-management"></a>Varningsregler management
**Övervakaren - aviseringar (förhandsgranskning) > regler** är en enstaka sida för att hantera alla Varningsregler över dina Azure-prenumerationer. Visar alla Varningsregler (aktiverat eller inaktiverat) och kan sorteras utifrån target-resurser, resursgrupper, regelnamn eller status. Varningsregler kan även aktiveras eller inaktiveras eller redigeras från den här sidan.  

 ![regler för aviseringar](./media/monitoring-overview-unified/alerts-preview-rules.png)


## <a name="one-alert-authoring-experience-across-all-monitoring-sources"></a>En avisering redigering erfarenheter från alla källor för övervakning
Aviseringar kan skrivas i ett konsekvent sätt oavsett övervakningstjänsten eller signalerar till typen i den nya aviseringar upplevelsen. Alla aviseringar utlöses och relaterad information finns i en sida.  
 
Redigera en avisering är en aktivitet i tre steg där användaren först hämtar ett mål för aviseringen, följt av att välja rätt signalen och sedan ange logiken som ska tillämpas på signalen som en del av regeln. Förenklad redigering processen kräver inte längre att användaren vet övervakning käll- eller signaler som stöds innan du väljer en Azure-resurs. Vanliga redigeringsfunktionerna automatiskt filtrerar listan över tillgängliga signalerar baserat på valda målresurs och hjälper att skapa aviseringen logik

Du kan lära dig mer om hur du skapar följande aviseringstyper [här](monitor-alerts-unified-usage.md).
- Mått aviseringar (kallas nära realtid mått aviseringar i aktuella experience)
- Loggen aviseringar (logganalys)
- Loggen aviseringar (aktivitetsloggar)
- Loggen aviseringar (Application Insights)

 

## <a name="alert-types-supported-in-this-preview"></a>Aviseringstyper som stöds i den här förhandsgranskningen


| **Typen av signal** | **Övervakare för källa** | **Beskrivning** | 
|-------------|----------------|-------------|
| Mått | Övervakare för Azure | Kallas [ **nära realtid mått aviseringar**](monitoring-near-real-time-metric-alerts.md), aviseringarna mått utvärderar mått villkor så ofta som 1 min. och för flera mått regler. En lista över resurstyper som stöds är tillgänglig [här](monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported). Äldre mått aviseringar enligt [här](monitoring-overview-alerts.md#alerts-in-different-azure-services) stöds inte i den nya aviseringar upplevelse. Du hittar dem under aviseringar (klassisk)|
| Logs  | Log Analytics | Ta emot meddelanden eller köra automatiserade åtgärder när en logg sökfråga över mått och/eller händelse data uppfyller vissa villkor.|
| Aktivitetslogg | Aktivitetsloggar | Den här kategorin innehåller poster för alla skapa, uppdatera, och ta bort åtgärder som utförs via det valda målet (resursen/resursgruppen grupp eller prenumeration). |
| Logs  | Tjänsten hälsa loggar | Stöds inte i aviseringar upplevelse.   |
| Logs  | Application Insights | Den här kategorin innehåller loggar med prestandainformation om programmets. Du kan definiera villkoren för åtgärderna som vidtagits - baserat på programdata med analytics-fråga. |
| Mått | Application Insights | Stöds inte i aviseringar upplevelse. Du hittar dem under aviseringar (klassisk) |
| Tillgänglighetstester | Application Insights | Stöds inte i aviseringar upplevelse. Du hittar dem under aviseringar (klassisk) |


## <a name="next-steps"></a>Nästa steg
- [Lär dig hur du använder den nya upplevelsen aviseringar för att skapa, visa och hantera aviseringar](monitor-alerts-unified-usage.md)
- [Lär dig mer om loggen aviseringar i aviseringar upplevelsen](monitor-alerts-unified-log.md)
- [Lär dig mer om mått aviseringar i aviseringar upplevelsen](monitoring-near-real-time-metric-alerts.md)
- [Lär dig mer om aktiviteten loggen aviseringar i aviseringar upplevelsen](monitoring-activity-log-alerts-new-experience.md)
