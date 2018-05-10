---
title: Logga varningar i Azure-Monitor - aviseringar | Microsoft Docs
description: 'Aktivera e-postmeddelanden, meddelanden, anropa webbplatser URL: er (webhooks) eller automation när de analytiska frågan du anger villkor för Azure-aviseringar.'
author: msvijayn
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: f7457655-ced6-4102-a9dd-7ddf2265c0e2
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: vinagara
ms.openlocfilehash: 3a4277d2106078136cee09dfe6aefc87a73c4e08
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
---
# <a name="log-alerts-in-azure-monitor---alerts"></a>Loggen aviseringar i Azure-Monitor - aviseringar 
Den här artikeln innehåller information om loggen aviseringar är en av typerna av aviseringar som stöds i den nya [Azure aviseringar](monitoring-overview-unified-alerts.md) och användarna kan använda Azures analytics platform som bas för aviseringar... Mer information om måttet aviseringar via loggar avser [nära realtid mått aviseringar](monitoring-near-real-time-metric-alerts.md)


Loggen aviseringen består av loggen Sök regler som har skapats för [Azure logganalys](../log-analytics/log-analytics-tutorial-viewdata.md) eller [Application Insights](../application-insights/app-insights-cloudservices.md#view-azure-diagnostic-events)


## <a name="log-search-alert-rule---definition-and-types"></a>Loggen Sök varningsregeln - definition och typer

Loggen Sök regler skapas med Azure aviseringar för att köra frågor för angivna loggen automatiskt med jämna mellanrum.  Om resultatet av logg-fråga matchar särskilda villkor, skapas en avisering post. Regeln kan sedan automatiskt köra en eller flera åtgärder med hjälp av [åtgärdsgrupper](monitoring-action-groups.md). 

Loggen Sök regler definieras av följande information:
- **Loggar frågan**.  Den fråga som körs varje gång regeln utlöses.  Poster som returneras av den här frågan används för att avgöra om en avisering skapas. *Azure Application Insights* fråga kan även inkludera [mellan program anropar](https://dev.applicationinsights.io/ai/documentation/2-Using-the-API/CrossResourceQuery), förutsatt att användaren har behörighet till externa program. 

    > [!IMPORTANT]
    > Relationstypen av [mellan program frågan för Application Insights](https://dev.applicationinsights.io/ai/documentation/2-Using-the-API/CrossResourceQuery) har Förhandsgranska - funktionerna och användarupplevelse kan ändras. Användning av [mellan arbetsytan frågan](https://dev.loganalytics.io/oms/documentation/3-Using-the-API/CrossResourceQuery) och [cross-resurs-fråga för Log Analytics](../log-analytics/log-analytics-cross-workspace-search.md) är för närvarande **stöds inte** i Azure-aviseringar.

- **Tidsperiod**.  Anger tidsintervallet för frågan. Frågan returnerar bara de poster som har skapats i det här intervallet för den aktuella tiden. Tidsperiod som begränsar vilka data som hämtats för logg-fråga att förhindra missbruk och kringgår kommandon tid (som sedan) användas i loggen fråga. <br>*Om frågan körs klockan 13:15 tidsperioden som har angetts till 60 minuter, returneras endast de poster som skapats mellan 12:15:00 och 1:15 i Återställningsmappen exempelvis för att köra frågan för loggen. Nu om loggen frågan använder tiden som kommandot som sedan 7d, log-frågan skulle köras endast för data mellan 12:15:00 och 1:15 PM - som om det finns data för bara de senaste 60 minuterna. Och inte för data som anges i loggen frågan sju dagar.*
- **Frekvensen**.  Anger hur ofta frågan ska köras. Kan vara ett värde mellan 5 minuter och 24 timmar. Måste vara lika med eller mindre än tidsperioden.  Om värdet är större än tidsperioden, riskerar du poster som saknas.<br>*Anta exempelvis att en tidsperiod på 30 minuter och en frekvens som 60 minuter.  Om frågan körs 1:00, returnerar poster mellan 12:30 och 1:00.  Nästa gång frågan körs är 2:00 när återgår den poster mellan 1:30 och 2:00.  Alla poster som skapats mellan 01:00 och 1:30 skulle aldrig utvärderas.*
- **Tröskelvärde för**.  Resultaten av loggen sökningen utvärderas för att avgöra om en avisering ska skapas.  Tröskelvärdet är olika för olika typer av loggen Sök Varningsregler.

Loggen Sök regler vara den för [Azure logganalys](../log-analytics/log-analytics-tutorial-viewdata.md) eller [Programinsikter](../application-insights/app-insights-cloudservices.md#view-azure-diagnostic-events), kan vara av två typer. De olika typerna beskrivs i detalj i avsnitten som följer.

- **[Antalet resultat](#number-of-results-alert-rules)**. Avisering skapas när antalet poster som returneras av loggen sökningen överskrider ett angivet tal.
- **[Mått mätning](#metric-measurement-alert-rules)**.  Avisering som skapas för varje objekt i resultaten av loggen sökning med värden som överskrider angiven tröskel.

Skillnaderna mellan varningsregeln typer är som följer.

- *Antalet resultat* Varningsregler skapas alltid en enda avisering, medan *mått mätning* varningsregeln skapar en avisering för varje objekt som överstiger tröskelvärdet.
- *Antalet resultat* Varningsregler skapar en avisering när tröskelvärdet överskrids en gång. *Mått mätning* Varningsregler kan skapa en avisering när tröskelvärdet överskrids ett visst antal gånger under ett visst tidsintervall.

### <a name="number-of-results-alert-rules"></a>Antalet resultat Varningsregler
**Antalet resultat** Varningsregler skapar en avisering när antalet poster som returneras av frågan överskrider det angivna tröskelvärdet. Den här typen av regel för varning är idealiskt för att arbeta med händelser, t.ex Windows-händelseloggar, Syslog, WebApp svar och anpassade loggar.  Du kanske vill skapa en avisering när en viss felhändelse skapas eller när flera felhändelser skapas inom en viss tidsperiod.

**Tröskelvärde för**: tröskelvärdet för ett antal resultat Varningsregler är större eller mindre än ett visst värde.  Om antalet poster som returneras av loggen sökningen matchar det här villkoret, skapas en avisering.

Ange antalet resultat till större än 0 för att Avisera om en enskild händelse och Sök efter en enskild händelse som har skapats sedan den senaste gången frågan kördes. Vissa program får logga in ett tillfälligt fel som inte nödvändigtvis rera en avisering.  Programmet kan till exempel gör processen som skapade felhändelsen och lyckas nästa gång.  I det här fallet kan du inte vill skapa en avisering om flera händelser skapas inom en viss tidsperiod.  

I vissa fall kanske du vill skapa en avisering om en händelse.  En process kan till exempel logga regelbundna händelser som indikerar att den fungerar korrekt.  Om det inte att logga en av dessa händelser inom en viss tidsperiod, ska en avisering skapas.  I det här fallet kan du ange ett tröskelvärde **mindre än 1**.

#### <a name="example"></a>Exempel
Överväg ett scenario där du vill veta när en webbaserad App ger ett svar för användare med kod 500 (det vill säga) internt serverfel. Du kan skapa en aviseringsregel med följande information:  
- **Fråga:** begäranden | där resultCode == ”500”<br>
- **Tidsperiod:** 30 minuter<br>
- **Varna frekvens:** fem minuter<br>
- **Tröskelvärde:** bra än 0<br>

Sedan körs aviseringen frågan var femte minut med 30 minuter data - att leta efter en post där Resultatkod var 500. Om även en post hittas utlöses aviseringen och utlöser den åtgärd som konfigurerats.

### <a name="metric-measurement-alert-rules"></a>Mått mätning Varningsregler

- **Mått mätning** Varningsregler skapar en avisering för varje objekt i en fråga med ett värde som överskrider ett angivet tröskelvärde.  De har olika följande skillnader från **antalet resultat** Varna regler.
- **Mängdfunktion**: Anger beräkningen som utförs och kan vara ett numeriskt fält ska aggregeras.  Till exempel **count()** returnerar antalet poster i frågan, **avg(CounterValue)** Returnerar medelvärdet för fältet CounterValue under period. Mängdfunktion i fråga måste vara namnet/kallas: AggregatedValue och ange ett numeriskt värde. 
- **Gruppera fältet**: en post med ett insamlat värde skapas för varje instans av det här fältet och en avisering genereras för varje.  Till exempel om du vill generera en avisering för varje dator du vill använda **per dator** 

    > [!NOTE]
    > Du kan ange fältet för att gruppera data för mått mätning Varningsregler som baseras på Application Insights. Det gör du genom att använda den **sammanställd på** alternativet i Regeldefinitionen.   
    
- **Intervallet**: definierar det tidsintervall under vilken data sammanställs.  Till exempel om du har angett **fem minuter**, skapas en post för varje instans av fältet samman med 5 minuters intervall under den tidsperiod som angetts för aviseringen.

    > [!NOTE]
    > Bin-funktionen måste användas i frågan anger intervallet. Eftersom bin() kan leda till olika tidsintervall - konverteras avisering automatiskt bin kommandot bin_at kommandot med rätt tid vid körning, kontrollera resultatet med en fast punkt
    
- **Tröskelvärde för**: tröskelvärdet för mått mätning Varningsregler definieras av ett samlat värde och ett antal intrång.  Om varje datapunkt i loggen sökningen överskrider detta värde, anses det har ett intrång.  Om antalet överträdelser i för alla objekt i resultaten överskrider det angivna värdet, skapas en avisering för objektet.

#### <a name="example"></a>Exempel
Föreställ dig ett scenario där du vill lägga till en avisering om alla datorer överskrids processoranvändning 90% tre gånger under 30 minuter.  Du kan skapa en aviseringsregel med följande information:  

- **Fråga:** Perf | där ObjectName == ”-Processor” och CounterName == ”% processortid” | sammanfatta AggregatedValue = avg(CounterValue) av bin (TimeGenerated, 5 m), datorn<br>
- **Tidsperiod:** 30 minuter<br>
- **Varna frekvens:** fem minuter<br>
- **Aggregera värde:** bra än 90<br>
- **Utlösaren avisering baserat på:** totalt överträdelser som är större än 5<br>

Frågan skulle skapa ett genomsnittligt värde för varje dator med 5 minuters mellanrum.  Den här frågan skulle köras var femte minut för data som samlas in under de föregående 30 minuterna.  Exempeldata visas nedan för tre datorer.

![Exempel frågeresultat](./media/monitor-alerts-unified/metrics-measurement-sample-graph.png)

I det här exemplet skulle separata aviseringar skapas för srv02 och srv03 eftersom de uppfylls tröskelvärdet 90% 3 gånger under tidsperioden.  Om den **utlösaren avisering baserat på:** ändrades till **sidordning** sedan en avisering skapas endast för srv03 eftersom den utsatts för intrång tröskelvärdet för tre på varandra följande prover.


## <a name="log-search-alert-rule---creation-and-modification"></a>Loggen Sök varningsregeln - skapandet och ändringar

Loggen avisering samt dess consisting loggen Sök varningsregel kan visas, skapas eller ändras från:
- Azure Portal
- REST API: er (inklusive via PowerShell)
- Azure Resource Manager-mallar

### <a name="azure-portal"></a>Azure Portal
Sedan introduktionen av den [nya Azure aviseringar](monitoring-overview-unified-alerts.md), nu användare kan hantera alla typer av aviseringar i Azure-portalen från en enda plats och liknande steg. Lär dig mer om [med nya Azure aviseringar](monitor-alerts-unified-usage.md).

Användare kan också perfekt deras frågor i Analytics platform valt i Azure och sedan *importera dem för användning i aviseringar genom att spara frågan*. Steg att följa:
- *För Application Insights*: Gå till Analytics-portalen, verifiera frågan och dess resultat. Spara med unika namn i *delade frågor*.
- *För Log Analytics*: Gå till Log-sökning, verifiera frågan och dess resultat. Använder sedan spara med det unika namnet i en kategori.

Sedan när [skapar en avisering om loggen i aviseringar ](monitor-alerts-unified-usage.md), visas den sparade fråga som signaltypen **logg (sparad fråga)**; som visas i exemplet nedan: ![sparad fråga importeras till aviseringar](./media/monitor-alerts-unified/AlertsPreviewResourceSelectionLog-new.png)

> [!NOTE]
> Med hjälp av **logg (sparad fråga)** resulterar i en import till aviseringar. Alla ändringar som gjorts efter i Analytics kommer därför inte att reflekterande i loggen Sök Varningsregler och vice versa.

### <a name="rest-apis"></a>REST API:er
API: er som angavs för Log aviseringar är RESTful och kan nås via Azure Resource Manager REST API. Därför kan nås via PowerShell, samt andra alternativ för att utnyttja de API: er.

För information samt exempel med hjälp av REST-API, se:
- [Logga Analytics avisering REST API](../log-analytics/log-analytics-api-alerts.md) – om du vill skapa och hantera loggen Sök Varningsregler för Azure logganalys
- [Azure övervakaren schemalagda frågan regler REST API](https://docs.microsoft.com/en-us/rest/api/monitorr/scheduledqueryrules/) – om du vill skapa och hantera loggen Sök Varningsregler för Azure Application Insights

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall
Användarna kan också använda flexibiliteten genom [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) att skapa och uppdatera resurser – för att skapa eller uppdatera Log aviseringar.

För information samt exempel på hur du använder Resource Manager-mallar, se:
- [Sparade sökningen och aviseringar](monitor-alerts-unified-log-template.md#managing-log-alert-on-log-analytics) för Logga varningar baserat på Azure logganalys
- [Schemalagda Frågeregeln](monitor-alerts-unified-log-template.md#managing-log-alert-on-application-insights) för Logga varningar baserat på Azure Application Insights
 

## <a name="next-steps"></a>Nästa steg
* Förstå [Logga varningar i Azure](monitor-alerts-unified-log-webhook.md).
* Lär dig mer om den nya [Azure aviseringar](monitoring-overview-unified-alerts.md).
* Lär dig mer om [Programinsikter](../application-insights/app-insights-analytics.md).
* Lär dig mer om [logganalys](../log-analytics/log-analytics-overview.md).    
