---
title: Skapa aviseringar i OMS Log Analytics | Microsoft Docs
description: Aviseringar i Log Analytics kan identifiera viktig information i OMS-databasen och proaktivt meddelar dig om problem eller anropa åtgärder om du vill försöka åtgärda.  Den här artikeln beskriver hur du skapar en aviseringsregel och information om olika åtgärder som de kan ta.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 6cfd2a46-b6a2-4f79-a67b-08ce488f9a91
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/13/2018
ms.author: bwren
ms.openlocfilehash: b692822660ab12f89b274cea75727fb808d673f8
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="working-with-alert-rules-in-log-analytics"></a>Arbeta med Varningsregler i logganalys

> [!NOTE]
> Den här artikeln beskriver klassiska logganalys-aviseringar som hanteras i OMS-portalen.  Aviseringar i logganalys [förlängs till Azure](../monitoring-and-diagnostics/monitoring-alerts-extend.md).  När det är klart sedan du skapar och redigerar Varningsregler i Azure-portalen och Observera använda procedurerna i den här artikeln.


Aviseringar skapas från varningsregler som automatiskt kör regelbundna loggsökningar.  De skapar en avisering post om resultaten uppfyller specifika villkor.  Regeln kan sedan automatiskt köra en eller flera åtgärder för att proaktivt meddela dig om aviseringen eller anropa en annan process.   

Den här artikeln beskriver processer för att skapa och redigera Varningsregler med hjälp av OMS-portalen.  Mer information om de olika inställningarna och hur du implementerar logik som krävs finns [förstå aviseringar i logganalys](log-analytics-alerts.md).


## <a name="create-an-alert-rule"></a>Skapa en varningsregel

Om du vill skapa en aviseringsregel med hjälp av OMS-portalen, börja med att skapa en logg-sökning för de poster som ska anropa aviseringen.  Den **avisering** knappen är tillgängliga så att du kan skapa och konfigurera varningsregeln.

>[!NOTE]
> Högst 250 Varningsregler kan för närvarande skapas i logganalys-arbetsytan. 

1. Översikt över OMS-sidan klickar du på **loggen Sök**.
2. Skapa en ny logg sökfråga eller välj en sparad logg-sökning. 
3. Klicka på **Avisering** längst upp på sidan för att öppna skärmen **Lägg till varningsregel**.
4. Konfigurera varningsregeln med hjälp av informationen i [information om Varningsregler](#details-of-alert-rules) nedan.
6. Klicka på **Spara** för att slutföra varningsregeln.  Den ska börja köras omedelbart.


## <a name="edit-an-alert-rule"></a>Redigera en varningsregel
Du kan hämta en lista över alla Varningsregler i den **aviseringar** -menyn i logganalys **inställningar**.  

![Hantera aviseringar](./media/log-analytics-alerts/configure.png)

1. I OMS-konsolen väljer du den **inställningar** panelen.
2. Välj **aviseringar**.

Du kan utföra flera åtgärder från den här vyn.

* Inaktivera en regel genom att välja **av** bredvid den.
* Redigera en aviseringsregel genom att klicka på pennikonen bredvid den.
* Ta bort en varningsregel genom att klicka på den **X** ikonen bredvid den. 

## <a name="details-of-alert-rules"></a>Information om Varningsregler
När du skapar eller redigerar en aviseringsregel i OMS-portalen kan du arbeta med den **Lägg till regel varning** eller **redigera Aviseringsregel** sidan.  Följande tabeller beskriver fälten i den här skärmen.

![Varningsregel](media/log-analytics-alerts/add-alert-rule.png)

### <a name="alert-information"></a>Aviseringsinformation
Dessa är grundläggande inställningar för varningsregeln och aviseringar som skapas.

| Egenskap | Beskrivning |
|:--- |:---|
| Namn | Unikt namn som identifierar varningsregeln. Det här namnet ingår i alla varningar som skapas av regeln.  |
| Beskrivning | Valfri beskrivning av regeln. |
| Allvarsgrad |Allvarlighetsgrad för alla aviseringar som skapats av den här regeln. |

### <a name="search-query-and-time-window"></a>Fråge- och sökfönstret
Fråge- och sökfönstret som returnerar posterna som utvärderas för att avgöra om alla aviseringar som ska skapas.

| Egenskap | Beskrivning |
|:--- |:---|
| Sökfråga | Det här är den fråga som ska köras.  Poster som returneras av den här frågan används för att avgöra om en avisering skapas.<br><br>Välj **Använd aktuell sökfråga** att använda den aktuella frågan eller välj en befintlig sparad sökning från listan.  Frågesyntaxen tillhandahålls i textrutan där du kan ändra det om det behövs. |
| Tidsfönster |Anger tidsintervallet för frågan.  Frågan returnerar bara de poster som har skapats i det här intervallet för den aktuella tiden.  Detta kan vara ett värde mellan 5 minuter och 24 timmar.  Det bör vara större än eller lika med aviseringsfrekvensen.  <br><br> Till exempel om frågan körs klockan 13:15 tidsfönstret har angetts till 60 minuter, ska endast de poster som skapats mellan 12:15:00 och 1:15 i Återställningsmappen returneras. |

När du anger tidsramen för varningsregeln visas antalet befintliga poster som matchar sökkriterierna för den tidsperioden.  Detta kan hjälpa dig att bestämma frekvensen som ger antalet resultat som du förväntar dig.

### <a name="schedule"></a>Schema
Definierar hur ofta frågan ska köras.

| Egenskap | Beskrivning |
|:--- |:---|
| Aviseringsfrekvensen | Anger hur ofta frågan ska köras. Kan vara ett värde mellan 5 minuter och 24 timmar. Måste vara lika med eller mindre än tidsperioden.  Om värdet är större än tidsfönstret riskerar du poster som saknas.<br><br>Tänk dig ett tidsfönster på 30 minuter och en frekvens som 60 minuter.  Om frågan körs 1:00, returnerar poster mellan 12:30 och 1:00.  Nästa gång frågan körs är 2:00 när återgår den poster mellan 1:30 och 2:00.  Alla poster som skapats mellan 01:00 och 1:30 skulle aldrig utvärderas. |


### <a name="generate-alert-based-on"></a>Generera avisering baserat på
Definierar de kriterier som ska utvärderas mot resultatet av frågan för att avgöra om en avisering ska skapas.  Detaljerna är olika beroende på vilken typ av regel för varning som du väljer.  Du kan hämta information för olika varningsregeln typer från [förstå aviseringar i logganalys](log-analytics-alerts.md).

| Egenskap | Beskrivning |
|:--- |:---|
| Ignorera aviseringar | När du aktiverar Undertryckning för varningsregeln är åtgärder för regeln inaktiverad för en angiven tidsperiod när du har skapat en ny avisering. Regeln körs och skapar avisering innehåller information om följande villkor är uppfyllda. Detta är att låta dig tid för att rätta till problemet utan att köra dubbla åtgärder. |

#### <a name="number-of-results-alert-rules"></a>Antalet resultat Varningsregler

| Egenskap | Beskrivning |
|:--- |:---|
| Antal resultat |En avisering skapas om antalet poster som returneras av frågan är antingen **större än** eller **mindre än** värdet du anger.  |

#### <a name="metric-measurement-alert-rules"></a>Mått mätning Varningsregler

| Egenskap | Beskrivning |
|:--- |:---|
| Samlingsvärde | Tröskelvärde som varje samlat värde i resultatet får överstiga ska anses vara ett intrång. |
| Utlös avisering baserat på | Antal överträdelser för en varning ska skapas.  Du kan ange **Totalt antal överträdelser** för valfri kombination av överträdelser över resultaten eller **på varandra följande överträdelser** att kräva att överträdelserna måste ske i beräkningar. |

### <a name="actions"></a>Åtgärder
Varningsregler skapas alltid en [Varna post](#alert-records) när tröskelvärdet är uppfyllt.  Du kan också definiera en eller flera svar som ska köras som skickar ett e-postmeddelande eller starta en runbook.



#### <a name="email-actions"></a>E-post-åtgärder
E-post-åtgärder skicka ett e-postmeddelande med information om aviseringen till en eller flera mottagare.

| Egenskap | Beskrivning |
|:--- |:---|
| E-postavisering |Ange **Ja** om du vill att ett e-postmeddelande ska skickas när en avisering utlöses. |
| Ämne |Ämne för e-postmeddelandet.  Du kan inte ändra innehållet i e-postmeddelandet. |
| Mottagare |Tar med alla e-postmottagare.  Om du anger mer än en adress kan du avgränsa dem med semikolon (;). |

#### <a name="webhook-actions"></a>Webhook-åtgärder
Webhook-åtgärder kan du anropa en extern process via en enkel HTTP POST-begäran.

| Egenskap | Beskrivning |
|:--- |:---|
| Webhook |Ange **Ja** om du vill anropa en webhook när en avisering utlöses. |
| Webhooksadressen |URL till webhooken. |
| Inkludera anpassad JSON-nyttolast |Välj det här alternativet om du vill ersätta standard nyttolast med en anpassad nyttolast. |
| Ange anpassad JSON-nyttolast |Anpassad nyttolast för webhooken.  Se föregående avsnitt för mer information. |

#### <a name="runbook-actions"></a>Runbook-åtgärder
Runbook-åtgärder startar en runbook i Azure Automation. 

>[!NOTE]
> Du måste ha installerats i din arbetsyta för den här åtgärden aktiveras Automation-lösningen. 


| Egenskap | Beskrivning |
|:--- |:---|
| Runbook | Ange **Ja** om du vill starta en Azure Automation-runbook när en avisering utlöses.  |
| Automation-konto | Anger Automation-kontot som väljs från runbooks.  Detta är det åtgärdskonto som är länkad till arbetsytan. |
| Välj en runbook | Välj runbook som du vill starta när en avisering skapas. |
| Kör på | Välj **Azure** att köra runbook i molnet.  Välj **Hybrid worker** att köra runbook på en agent med [Hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md ) installerad.  |




## <a name="next-steps"></a>Nästa steg
* Installera den [avisering hanteringslösning](log-analytics-solution-alert-management.md) att analysera aviseringar som skapats i logganalys tillsammans med aviseringar som samlas in från System Center Operations Manager (SCOM).
* Läs mer om [logga sökningar](log-analytics-log-searches.md) som kan generera aviseringar.
* Slutföra en genomgång för [konfigurerar en webook](log-analytics-alerts-webhooks.md) med en aviseringsregel.  
* Lär dig hur du skriver [runbooks i Azure Automation](https://azure.microsoft.com/documentation/services/automation) att åtgärda problem som identifieras av aviseringar.

