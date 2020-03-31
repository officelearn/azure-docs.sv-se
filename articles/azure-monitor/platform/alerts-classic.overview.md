---
title: Översikt över klassiska aviseringar i Microsoft Azure och Azure Monitor
description: Klassiska aviseringar är inaktuella. Aviseringar kan du övervaka Azure-resursmått, händelser eller loggar och meddelas när ett villkor som du anger uppfylls.
ms.topic: conceptual
ms.date: 05/19/2018
ms.subservice: alerts
ms.openlocfilehash: 098efd3075c6b099bdfc925cb4f09163f83532a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77668274"
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>Vad är klassiska aviseringar i Microsoft Azure?

> [!NOTE]
> I den här artikeln beskrivs hur du skapar äldre klassiska måttaviseringar. Azure Monitor stöder nu [nyare måttaviseringar i nära realtid och en ny aviseringsupplevelse](../../azure-monitor/platform/alerts-overview.md). Klassiska aviseringar dras [tillbaka](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement), men fortfarande i begränsad användning för resurser som ännu inte stöder de nya aviseringarna. 
>

Aviseringar gör att du kan konfigurera villkor över data och bli meddelad när villkoren matchar de senaste övervakningsdata.

## <a name="old-and-new-alerting-capabilities"></a>Gamla och nya varningsfunktioner

Tidigare Azure Monitor, Application Insights, Log Analytics och Service Health hade separata aviseringar funktioner. Övertid, Azure förbättrade och kombinerade både användargränssnittet och olika metoder för aviseringar. Konsolideringen pågår fortfarande.

Du kan bara visa klassiska aviseringar på användarskärmen för klassiska aviseringar i Azure Portal. Du får den här skärmen från knappen **Visa klassiska varningar** på skärmen varningar. 

 ![Varningsalternativ i Azure-portalen](media/alerts-classic.overview/monitor-alert-screen2.png)

Den nya användarupplevelsen för aviseringar har följande fördelar jämfört med den klassiska aviseringarupplevelsen:
-   **Bättre meddelandesystem** - Alla nyare aviseringar använder åtgärdsgrupper, som heter grupper av meddelanden och åtgärder som kan återanvändas i flera aviseringar. Klassiska måttaviseringar och äldre Log Analytics-aviseringar använder inte åtgärdsgrupper.
-   **En enhetlig redigeringsupplevelse** – Alla aviseringar för mått, loggar och aktivitetslogg över Azure Monitor, Log Analytics och Application Insights finns på ett ställe.
-   **Visa aviseringar för sparkenlogganalys i Azure-portalen** – Nu kan du även se aviseringar om sparken i din prenumeration. Tidigare var dessa i en separat portal.
-   **Separation av avfyrade aviseringar och varningsregler** - Varningsregler (definitionen av villkor som utlöser en avisering) och avfyrade aviseringar (en instans av aviseringsregeln bränning) differentieras, så drift- och konfigurationsvyerna separeras.
-   **Bättre arbetsflöde** - De nya aviseringar som skapar erfarenhet vägleder användaren längs processen att konfigurera en varningsregel, vilket gör det enklare att upptäcka rätt saker att få aviseringar på.
-   **Konsolidering och** **inställning** av aviseringar för smarta aviseringar – Nyare aviseringar inkluderar automatisk grupperingsfunktion som visar liknande aviseringar tillsammans för att minska överbelastningen i användargränssnittet. 

De nyare måttaviseringarna har följande fördelar jämfört med de klassiska måttaviseringarna:
-   **Förbättrad svarstid:** Nyare måttaviseringar kan köras lika ofta som var en minut. Äldre måttaviseringar körs alltid med en frekvens på 5 minuter. Nyare aviseringar har ökat mindre fördröjning från utfärdande förekomst till anmälan eller åtgärd (3 till 5 minuter). Äldre varningar är 5 till 15 minuter beroende på typ.  Loggaviseringar har vanligtvis 10 till 15 minuters fördröjning på grund av den tid det tar att inta loggarna, men nyare bearbetningsmetoder minskar den tiden. 
-   **Stöd för flerdimensionella mått:** Du kan varna för dimensionella mått så att du kan övervaka ett intressant segment av måttet.
-   **Mer kontroll över metriska villkor**: Du kan definiera rikare varningsregler. De nyare aviseringarna stöder övervakning av de högsta, lägsta, genomsnittliga och totala värdena för mått.
-   **Kombinerad övervakning av flera mått**: Du kan övervaka flera mått (för närvarande upp till två mått) med en enda regel. En avisering utlöses om båda måtten bryter mot sina respektive tröskelvärden för den angivna tidsperioden.
-   **Bättre meddelandesystem:** Alla nyare aviseringar använder [åtgärdsgrupper](../../azure-monitor/platform/action-groups.md), som heter grupper av meddelanden och åtgärder som kan återanvändas i flera aviseringar.  Klassiska måttaviseringar och äldre Log Analytics-aviseringar använder inte åtgärdsgrupper. 
-   **Mått från loggar** (offentlig förhandsversion): Loggdata som går in i Log Analytics kan nu extraheras och konverteras till Azure Monitor-mått och sedan aviseras på precis som andra mått. Se [Aviseringar (klassisk)](alerts-classic.overview.md) för den terminologi som är specifik för klassiska aviseringar. 


## <a name="classic-alerts-on-azure-monitor-data"></a>Klassiska aviseringar på Azure Monitor-data
Det finns två typer av klassiska aviseringar tillgängliga - måttaviseringar och aktivitetsloggaviseringar.

* **Klassiska måttaviseringar** - Den här aviseringen utlöser när värdet för ett angivet mått överskrider ett tröskelvärde som du tilldelar. Aviseringen genererar ett meddelande när tröskelvärdet överskrids och varningsvillkoret uppfylls. Då anses aviseringen vara "aktiverad". Det genererar ett annat meddelande när det är "Löst" - det vill, när tröskelvärdet överskrids igen och villkoret inte längre uppfylls.

* **Klassiska aktivitetsloggaviseringar** – En direktuppspelningsloggavisering som utlöser en aktivitetslogghändelsepost som matchar dina filtervillkor. Dessa aviseringar har bara ett tillstånd, "Aktiverad". Varningsmotorn tillämpar helt enkelt filterkriterierna på alla nya händelser. Den söker inte för att hitta äldre poster. Dessa aviseringar kan meddela dig när en ny tjänsthälsa incident inträffar eller när en användare eller ett program utför en åtgärd i din prenumeration, till exempel "Ta bort virtuell dator."

För resursloggdata som är tillgängliga via Azure Monitor dirigerar du data till Log Analytics och använder en loggfrågavarning. Log Analytics använder nu den [nya aviseringsmetoden](../../azure-monitor/platform/alerts-overview.md) 

I följande diagram sammanfattas datakällor i Azure Monitor och, begreppsmässigt, hur du kan avisera data.

![Varningar förklaras](media/alerts-classic.overview/Alerts_Overview_Resource_v5.png)

## <a name="taxonomy-of-alerts-classic"></a>Taxonomi av varningar (klassisk)
Azure använder följande termer för att beskriva klassiska aviseringar och deras funktioner:
* **Varning** - en definition av kriterier (en eller flera regler eller villkor) som aktiveras när de uppfylls.
* **Aktiv** - det tillstånd då de kriterier som definieras av en klassisk avisering uppfylls.
* **Löst** - det tillstånd där de kriterier som definieras av en klassisk avisering inte längre uppfylls efter att tidigare ha uppfyllts.
* **Meddelande** - åtgärden som vidtas baserat på en klassisk avisering blir aktiv.
* **Åtgärd** - ett specifikt samtal som skickas till en mottagare av ett meddelande (till exempel att skicka e-post till en adress eller publicera en webhook-URL). Meddelanden kan vanligtvis utlösa flera åtgärder.

## <a name="how-do-i-receive-a-notification-from-an-azure-monitor-classic-alert"></a>Hur får jag ett meddelande från en klassikeravisering för Azure Monitor?
Historiskt sett har Azure-aviseringar från olika tjänster använt sina egna inbyggda meddelandemetoder. 

Azure Monitor skapade en återanvändningsbar meddelandegruppering som kallas *åtgärdsgrupper*. Åtgärdsgrupper anger en uppsättning mottagare för ett meddelande. Varje gång en avisering aktiveras som refererar till åtgärdsgruppen får alla mottagare det meddelandet. Med åtgärdsgrupper kan du återanvända en gruppering av mottagare (till exempel din jourteknikerlista) över många varningsobjekt. Åtgärdsgrupper stöder meddelanden genom att publicera en webhook-URL utöver e-postadresser, SMS-nummer och ett antal andra åtgärder.  Mer information finns i [åtgärdsgrupper](../../azure-monitor/platform/action-groups.md). 

Äldre klassiska aktivitetsloggaviseringar använder åtgärdsgrupper.

De äldre måttaviseringarna använder dock inte åtgärdsgrupper. I stället kan du konfigurera följande åtgärder: 
- Skicka e-postmeddelanden till tjänstadministratören, till coadministratorer eller till ytterligare e-postadresser som du anger.
- Anropa en webhook, som gör att du kan starta ytterligare automatiseringsåtgärder.

Webhooks möjliggör automatisering och reparation, till exempel med hjälp av:
- Azure Automation – Runbook
- Azure-funktion
- Azure Logic-app
- en tjänst från tredje part

## <a name="next-steps"></a>Nästa steg
Få information om varningsregler och konfigurera dem med hjälp av:

* Läs mer om [mått](data-platform.md)
* Konfigurera [klassiska måttaviseringar via Azure Portal](alerts-classic-portal.md)
* Konfigurera [klassiska måttaviseringar PowerShell](alerts-classic-portal.md)
* Konfigurera [det klassiska metriskvarningar kommandoradsgränssnittet (CLI)](alerts-classic-portal.md)
* Konfigurera [klassiska måttaviseringar Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)
* Läs mer om [aktivitetslogg](platform-logs-overview.md)
* Konfigurera [aktivitetsloggaviseringar via Azure-portal](activity-log-alerts.md)
* Konfigurera [aktivitetsloggvarningar via Resurshanteraren](alerts-activity-log.md)
* Granska [schemat för webbkrok för aktivitetsloggen](activity-log-alerts-webhook.md)
* Läs mer om [åtgärdsgrupper](action-groups.md)
* Konfigurera [nyare aviseringar](alerts-metric.md)
