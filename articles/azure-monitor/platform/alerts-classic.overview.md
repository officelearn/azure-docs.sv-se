---
title: Översikt över klassiska aviseringar i Microsoft Azure och Azure Monitor
description: Klassiska aviseringar blir inaktuella. Aviseringar kan du övervaka Azure Resursmått, evenemang eller loggar och meddelas när ett angivet villkor uppfylls.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/29/2018
ms.author: robb
ms.openlocfilehash: 0d91e12de075ee6efebe39fd5ab582d4998046f9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60776649"
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>Vad är klassiska aviseringar i Microsoft Azure?

> [!NOTE]
> Den här artikeln beskriver hur du skapar äldre klassiska måttaviseringar. Azure Monitor har nu stöd [nyare måttaviseringar för nästan i realtid och nya aviseringsgränssnittet](../../azure-monitor/platform/alerts-overview.md). Klassiska aviseringar är [schemalagda att dras](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement).  
>

Aviseringar kan du konfigurera villkor för över data och bli informerad när villkoren matchas senaste övervakningsdata.

## <a name="old-and-new-alerting-capabilities"></a>Gamla och nya aviseringsfunktioner

Tidigare Azure Monitor, Application Insights, logganalys och Service Health hade separat aviseringsfunktioner. Övertidskostnad, Azure förbättrad och kombineras både användargränssnitt och olika metoder för aviseringar. Konsolideringen pågår fortfarande. Aviseringar

Du kan visa klassiska aviseringar endast på skärmen klassiska aviseringar om användaren i Azure Portal. Du får den här skärmen från den **Visa klassiska aviseringar** knappen på skärmen aviseringar. 

 ![Aviseringen val i Azure-portalen](media/alerts-classic.overview/monitor-alert-screen2.png)

Nya aviseringar användarupplevelsen har följande fördelar över klassiska aviseringarna:
-   **Bättre meddelandesystem** -alla nyare aviseringar använda åtgärdsgrupper som är namngivna grupper av meddelanden och åtgärder som kan återanvändas i flera aviseringar. Använd inte åtgärdsgrupper klassiska måttaviseringar och äldre Log Analytics-aviseringar.
-   **En enhetlig redigeringen** – alla avisering skapas för mått, loggar och aktivitet logga via Azure Monitor kan Log Analytics och Application Insights är på samma ställe.
-   **Visa utlösta Log Analytics-aviseringar i Azure-portalen** – du kan också se utlösta Log Analytics-aviseringar i din prenumeration. Tidigare var dessa i en separat portal.
-   **Separation av aktiverade aviseringar och Varningsregler** – aviseringsregler (definition av villkor som utlöser en avisering) och utlösta aviseringar (en instans av varningsregel detonation) differentierade, så vyerna drift och konfiguration är åtskilda.
-   **Bättre arbetsflöde** – nya aviseringar och redigering experience-guider användaren längs processen med att konfigurera en varningsregel som gör det enklare att identifiera rätt saker att få ett meddelande om.
-   **Konsolidering av aviseringar för smart** och **inställningen aviseringstillståndet** -nyare aviseringar innehåller automatisk grupperingsfunktion som visar liknande aviseringar tillsammans för att minska överlagring i användargränssnittet. 

Nyare måttaviseringar har följande fördelar över klassiska måttaviseringar:
-   **Förbättrad latens**: Nyare måttaviseringar kan köras så ofta som var en minut. Äldre måttaviseringar körs alltid med en frekvens på 5 minuter. Nyare aviseringar har ökar mindre fördröjning från förekomsten av problemet till meddelande eller åtgärd (3 till 5 minuter). Äldre aviseringar är 5 till 15 minuter beroende på typen.  Loggaviseringar har vanligtvis 10 till 15 minuters fördröjning på grund av den tid det tar att mata in loggarna, men senare bearbetningsmetoder sänker den tiden. 
-   **Stöd för flerdimensionella mått**: Du kan meddela på endimensionella mått som gör att du kan övervaka ett intressant segment av mått.
-   **Mer kontroll över mått villkor**: Du kan definiera bättre aviseringsregler. Nyare aviseringar stöd för övervakning av högsta, lägsta, genomsnittlig och total värdena för mått.
-   **Kombineras övervakning av flera mått**: Du kan övervaka flera mått (får för närvarande upp till två mått) med en enda regel. En avisering utlöses om båda mått bryta mot deras respektive tröskelvärden för den angivna tidsperioden.
-   **Bättre meddelandesystem**: Alla nyare aviseringar använda [åtgärdsgrupper](../../azure-monitor/platform/action-groups.md), vilket är namngivna grupper av meddelanden och åtgärder som kan återanvändas i flera aviseringar.  Använd inte åtgärdsgrupper klassiska måttaviseringar och äldre Log Analytics-aviseringar. 
-   **Mått från loggar** (offentlig förhandsversion): Loggdata som går in i Log Analytics nu kan extraheras och konverteras till Azure Monitor-mått och sedan ett meddelande om precis som andra mått. Se [aviseringar (klassisk)](alerts-classic.overview.md) av termer som är specifika för klassiska aviseringar. 


## <a name="classic-alerts-on-azure-monitor-data"></a>Klassiska aviseringar i Azure Monitor-data
Det finns två typer av klassiska aviseringar tillgängliga - aviseringar för mått och aviseringar för aktivitetsloggar.

* **Klassiska måttaviseringar** -den här aviseringen utlöses när värdet för ett visst mått överskrider ett tröskelvärde som du tilldelar. Aviseringen genererar en avisering när gränsen skärs och avisering villkoret är uppfyllt. I det här läget anses aviseringen vara ”aktiverad”. Den genererar ett nytt meddelande när det är ”löst” – det vill säga när tröskelvärdet skärs igen och villkoret uppfylls inte längre.

* **Klassiska aktivitetsloggaviseringar** – en strömmande log-avisering som utlöses vid en händelsepost för aktivitetsloggen som matchar dina filterkriterier. Aviseringarna har endast en status ”aktiverad”. Aviseringen motorn gäller bara filtervillkoren som en ny händelse. Den söker inte för att hitta dessa poster. Dessa aviseringar kan meddela dig när en ny Service Health-incident inträffar eller när en användare eller program utför en åtgärd i din prenumeration, till exempel ”ta bort virtuell dator”.

Dirigera data till Log Analytics (tidigare OMS) för diagnostiklogg data är tillgängliga i Azure Monitor, och Använd en avisering i Log Analytics-fråga. Logga Analytics nu använder den [nya aviseringar metod](../../azure-monitor/platform/alerts-overview.md) 

Följande diagram sammanfattar datakällor i Azure Monitor och, begreppsmässigt, hur du kan meddela från dessa data.

![Aviseringar som förklaras](media/alerts-classic.overview/Alerts_Overview_Resource_v5.png)

## <a name="taxonomy-of-alerts-classic"></a>Taxonomi aviseringar (klassisk)
Azure använder följande termer för att beskriva klassiska aviseringar och deras funktioner:
* **Aviseringen** – en definition av kriterier (en eller flera regler eller villkor) som blir aktiveras när uppfylls.
* **Aktiva** -tillståndet när en avisering om klassiska villkoren uppfylls.
* **Löst** -tillståndet när en avisering om klassiska villkoren uppfylls inte längre när du tidigare har uppfyllts.
* **Meddelande** – de åtgärder som vidtagits baserat på en klassisk avisering blir aktiv.
* **Åtgärden** – ett specifikt anrop som skickas till en mottagare av ett meddelande (till exempel e-posta en adress eller publicera till en Webbadress för webhook). Meddelanden kan vanligtvis utlösa flera åtgärder.

## <a name="how-do-i-receive-a-notification-from-an-azure-monitor-classic-alert"></a>Hur får jag ett meddelande från en klassisk Azure Monitor-avisering
Historiskt sett används Azure-aviseringar från olika tjänster för sina egna inbyggda aviserings-metoder. 

Azure Monitor skapade ett återanvändbara meddelande gruppering kallas *åtgärdsgrupper*. Åtgärdsgrupper ange en uppsättning mottagare för ett meddelande. Helst en avisering aktiveras som refererar till åtgärdsgruppen, får alla mottagare som meddelanden. Åtgärdsgrupper låter dig återanvända en gruppering av mottagare (till exempel på anrop engineer listan) för många aviseringar objekt. Åtgärdsgrupper använda meddelanden genom att publicera till en webhook-URL förutom e-postadresser, SMS-nummer och ett antal andra åtgärder.  Mer information finns i [åtgärdsgrupper](../../azure-monitor/platform/action-groups.md). 

Äldre klassiska aktivitetsloggsaviseringar använda åtgärdsgrupper.

Men Använd inte äldre måttaviseringar åtgärdsgrupper. I stället kan du konfigurera följande åtgärder: 
- Skicka e-postmeddelanden till tjänstadministratör, till medadministratörer eller till ytterligare e-postadresser som du anger.
- Anropa en webhook, där du kan starta ytterligare automation-åtgärder.

Webhooks gör automation och åtgärder, till exempel med hjälp av:
- Azure Automation – Runbook
- Azure-funktion
- Azure Logic App
- En tjänst från tredje part

## <a name="next-steps"></a>Nästa steg
Hämta information om Varningsregler och konfigurera dem med hjälp av:

* Läs mer om [mått](data-platform.md)
* Konfigurera [klassiska mått aviseringar via Azure-portalen](alerts-classic-portal.md)
* Konfigurera [klassisk mått aviseringar PowerShell](alerts-classic-portal.md)
* Konfigurera [klassiska mått aviseringar kommandoradsgränssnittet (CLI)](alerts-classic-portal.md)
* Konfigurera [klassiska mått aviseringar Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)
* Läs mer om [aktivitetsloggen](activity-logs-overview.md)
* Konfigurera [Aktivitetsloggaviseringar via Azure-portalen](activity-log-alerts.md)
* Konfigurera [Aktivitetsloggaviseringar via Resource Manager](alerts-activity-log.md)
* Granska den [avisering webhook för aktivitetslogg](activity-log-alerts-webhook.md)
* Läs mer om [åtgärdsgrupper](action-groups.md)
* Konfigurera [nyare aviseringar](alerts-metric.md)
