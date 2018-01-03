---
title: "Utforska den nya aviseringar (förhandsgranskning)-miljön i Azure-Monitor | Microsoft Docs"
description: "Förstå hur nya aviseringar för enkel och skalbar upplevelse i Azure gör redigering, visa och hantera aviseringar enklare"
author: manishsm-msft
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: mamit
ms.custom: 
ms.openlocfilehash: 5ded43548d9aea106c6e083476df4e735b8c00a6
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2017
---
# <a name="explore-the-new-alerts-preview-experience-in-azure-monitor"></a>Utforska den nya aviseringar (förhandsgranskning)-miljön i Azure-Monitor

## <a name="overview"></a>Översikt
 Aviseringar upplevelse i Azure har en ny ser ut och uppdaterade funktionerna. Den nya upplevelsen är tillgänglig från den **aviseringar (förhandsgranskning)** under Azure-Monitor på fliken. Följande är några av fördelarna med att använda den nya aviseringar (förhandsgranskning)-miljön:

 - **Uppdelning av Fired aviseringar och avisering regler** – i Alerts(Preview)-upplevelsen avisering regler (definition av villkor som utlöser en avisering) och utlöses aviseringar (en instans av varningsregeln att) särskiljs därför operativa och konfiguration av vyer är åtskilda. 
 - **En enhetlig redigering miljö för mått och logga varningar** - nya aviseringar (förhandsgranskning) Redigering upplevelse guider användaren längs processen att konfigurera en aviseringsregel, vilket gör det enklare att identifiera rätt saker att få ett meddelande om. 
 - **Visa utlöses logganalys aviseringar i Azure-portalen** – i det aviseringar (förhandsgranskning)-upplevelse nu kan du också se utlöses logganalys aviseringar i din prenumeration.  

I följande avsnitt beskrivs, i detalj, hur den nya upplevelsen fungerar. 

## <a name="taxonomy"></a>Taxonomi
Alerts(preview) upplevelsen använder följande begrepp för att separera Aviseringsregel och utlöste aviseringen objekt när enhetlig redigering användarupplevelse av alla olika aviseringstyper.

- **Rikta resurs** -ett mål kan vara Azure-resurser. Målresurs definierar omfång och signaler som är tillgängliga för aviseringar. Exempel mål: en virtuell dator, ett lagringskonto, en skaluppsättning för virtuell dator, en logganalys-arbetsytan eller en lösning. 

- **Kriterier** - kriteriet är en kombination av Signal och logik tillämpas på en målresurs. Exempel: Procent CPU > 70%, serversvarstid > 4 ms, antal resultat av en logg fråga > 100 osv. 

- **Signal** – signalerar sänds av målresurs och kan vara av flera typer. Den här förhandsversionen stöder mått och logga signaltyper.

- **Logik** -användardefinierade logik för att kontrollera om signalen förväntade intervall/värden.  
 
- **Åtgärden** -ett specifikt anrop som skickas till en mottagare av ett meddelande (till exempel e-posta en adress eller bokföring till en Webhooksadressen). Meddelanden kan vanligtvis utlösa flera åtgärder. Aviseringstyper som stöds i den här förhandsgranskningen supportgrupper för åtgärden.  
 
- **Varningsregeln** -definitionen av ett villkor som ska utlösa aviseringen. I den här förhandsgranskningen avisering regeln samlar in mål- och villkor för aviseringar. Varningsregeln kan vara i ett aktiverat eller inaktiverat tillstånd. 
 
- **Aviseringen utlöses** -skapas när ett aktiverat varningsregeln utlöses. Eldad avisering objekt kan vara i Fired eller tillstånd.

    > [!NOTE]
    > Detta skiljer sig från den aktuella aviseringar erfarenhet där aviseringen representerar både regeln och Eldad aviseringen och därför kan det vara i något av varning, aktiv eller inaktiverad tillstånd.
    >

## <a name="single-place-to-view-and-manage-alerts"></a>Enda plats att visa och hantera aviseringar
Målet av aviseringar (förhandsversion) är att vara den enda platsen att visa och hantera alla dina Azure-aviseringar. Följande avsnitt beskriver funktionerna i varje enskild skärmen i den nya upplevelsen.

### <a name="alerts-preview-overview-page"></a>Aviseringsöversikt (förhandsgranskning)
**Övervakaren - aviseringar (förhandsgranskning)** översiktssidan visar aggregerade sammanfattning av alla Eldad aviseringar och totalt konfigurerats aktiveras Varningsregler. Den visar även en lista över alla Eldad aviseringar. Ändra prenumerationer eller filterparametrarna uppdaterar aggregat och aviseringarna utlöses lista.

> [!NOTE]
> Eldad aviseringar visas i aviseringar (förhandsversion) är begränsade till stöds mått och logga varningar; Azure Övervakare visar antalet brand aviseringar, inklusive de på äldre Azure aviseringar

 ![Förhandsgranskning-översikt över aviseringar](./media/monitoring-overview-unified/alerts-preview-overview.png) 

### <a name="alert-rules-management"></a>Varningsregler management
**Övervakaren - aviseringar (förhandsgranskning) > regler** är en enstaka sida för att hantera alla Varningsregler över dina Azure-prenumerationer. Visar alla Varningsregler (aktiverat eller inaktiverat) och kan sorteras utifrån target-resurser, resursgrupper, regelnamn eller status. Varningsregler kan även aktiveras eller inaktiveras eller redigeras från den här sidan.  

 ![regler för aviseringar preview](./media/monitoring-overview-unified/alerts-preview-rules.png)


## <a name="one-alert-authoring-experience-across-all-monitoring-sources"></a>En avisering redigering erfarenheter från alla källor för övervakning
I aviseringar (förhandsgranskning) uppstår, aviseringar kan skrivas i ett konsekvent sätt oavsett övervakningstjänsten eller skicka en signal typen. Alla aviseringar utlöses och relaterad information finns i en sida.  
 
Redigera en avisering är en aktivitet i tre steg där användaren först hämtar ett mål för aviseringen, följt av att välja rätt signalen och sedan ange logiken som ska tillämpas på signalen som en del av regeln. Förenklad redigering processen kräver inte längre att användaren vet övervakning käll- eller signaler som stöds innan du väljer en Azure-resurs. Vanliga redigeringsfunktionerna automatiskt filtrerar listan över tillgängliga signalerar baserat på valda målresurs och hjälper att skapa aviseringen logik

Du kan lära dig mer om hur du skapar följande aviseringstyper [här](monitor-alerts-unified-usage.md). 
- Mått aviseringar (kallas nära realtid mått aviseringar i aktuella experience)
- Loggen aviseringar (logganalys)
 

## <a name="alert-types-supported-in-this-preview"></a>Aviseringstyper som stöds i den här förhandsgranskningen


| **Typen av signal** | **Övervakare för källa** | **Beskrivning** | 
|-------------|----------------|-------------|
| Mått | Övervakare för Azure | Kallas [ **nära realtid mått aviseringar** ](monitoring-near-real-time-metric-alerts.md) i aktuella upplevelsen aviseringarna mått utvärderar mått villkor så ofta som 1 min. och för flera mått regler. En lista över resurstyper som stöds är tillgänglig [här](monitoring-near-real-time-metric-alerts.md#what-resources-can-i-create-near-real-time-metric-alerts-for). Andra mått aviseringar enligt [här](monitoring-overview-alerts.md#alerts-in-different-azure-services) stöds inte i aviseringar (förhandsgranskning)-upplevelse.|
| Logs  | Log Analytics | Ta emot meddelanden eller köra automatiserade åtgärder när en logg sökfråga över mått och/eller händelse data uppfyller vissa villkor.|
| Logs  | Aktivitetsloggar | Stöds inte i aviseringar (förhandsgranskning)-upplevelse. |
| Logs  | Application Insights | Stöds inte i aviseringar (förhandsgranskning)-upplevelse. |
| Mått | Application Insights | Stöds inte i aviseringar (förhandsgranskning)-upplevelse. |
| Tillgänglighetstester | Application Insights | Stöds inte i aviseringar (förhandsgranskning)-upplevelse. |


## <a name="next-steps"></a>Nästa steg
- [Lär dig hur du använder den nya upplevelsen aviseringar (förhandsversion) för att skapa, visa och hantera aviseringar](monitor-alerts-unified-usage.md)
- [Lär dig mer om loggen aviseringar i aviseringar (förhandsgranskning)-upplevelse](monitor-alerts-unified-log.md)
- [Lär dig mer om mått aviseringar i aviseringar (förhandsgranskning)-upplevelse](monitoring-near-real-time-metric-alerts.md)

