---
title: "Vanliga frågor och svar om log Analytics ny logg sökning | Microsoft Docs"
description: "Den här artikeln innehåller vanliga frågor och svar om uppgraderingen av logganalys till det nya språket i fråga."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: bwren
ms.openlocfilehash: bf48cbc52a1ed96ed1bb49b1879d5cd7aece945c
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2017
---
# <a name="log-analytics-new-log-search-faq-and-known-issues"></a>Nya logganalys logga Sök vanliga frågor och kända problem

Den här artikeln innehåller vanliga frågor och kända problem om uppgraderingen av [logganalys till nya frågespråket](log-analytics-log-search-upgrade.md).  Du bör läsa igenom hela artikeln innan du fattar beslut om att uppgradera din arbetsyta.


## <a name="alerts"></a>Aviseringar

### <a name="question-i-have-a-lot-of-alert-rules-do-i-need-to-create-them-again-in-the-new-language-after-i-upgrade"></a>Fråga: Jag har en stor mängd Varningsregler. Behöver jag skapa dem igen på det nya språket efter uppgraderingen?  
Nej, din Varningsregler konverteras automatiskt till det nya språket Sök under uppgraderingen.  

### <a name="question-i-have-alert-rules-with-webhook-and-runbook-actions-will-these-continue-to-work-when-i-upgrade"></a>Fråga: Jag har Varningsregler webhook och runbook-åtgärder. Kommer dessa att fortsätta arbeta när jag uppgraderar?

Nej, det finns vissa ändringar i webhook och runbook-åtgärder som kan kräva att du kan göra ändringar på hur du bearbetar nyttolasten. Vi har gjort dessa ändringar att standardisera de olika utdataformat och minska storleken på nyttolasten. Mer information om dessa format finns i [lägga till åtgärder i Varningsregler i logganalys](log-analytics-alerts-actions.md).


## <a name="computer-groups"></a>Datorgrupper

### <a name="question-im-getting-errors-when-trying-to-use-computer-groups--has-their-syntax-changed"></a>Fråga: Jag får felmeddelanden när du försöker använda datorgrupper.  Har ändrats för sina syntax
Ja, grupperas syntaxen för datorn ändringar när ditt arbetsområde har uppgraderats.  Se [datorgrupper i logganalys logga sökningar](log-analytics-computer-groups.md) mer information.

### <a name="known-issue-groups-imported-from-active-directory"></a>Kända problem: grupper som importeras från Active Directory
För närvarande kan du skapa en fråga som använder en datorgrupp som importeras från Active Directory.  Skapa en ny datorgrupp med importerade Active Directory-gruppen och sedan använda den nya gruppen i frågan som en tillfällig lösning förrän problemet har åtgärdats.

En exempelfråga för att skapa en ny datorgrupp som innehåller en importerade Active Directory-grupp är följande:

    ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "AD Group Name" and TimeGenerated >= ago(24h) | distinct Computer


## <a name="dashboards"></a>Instrumentpaneler

### <a name="question-can-i-still-use-dashboards-in-an-upgraded-workspace"></a>Fråga: Kan jag fortsätta att använda instrumentpaneler i en uppgraderad arbetsytan?
Med uppgraderingen, vi börjar depracating **min instrumentpanel**.  Du kan fortsätta att använda alla paneler som du lagt till instrumentpanelen innan ditt arbetsområde har uppgraderats, men du kan inte redigera dessa paneler eller lägga till nya.  Du kan fortsätta att skapa och redigera vyer med [Vydesigner](log-analytics-view-designer.md), som har en större funktion, och även skapa instrumentpaneler i Azure-portalen.


## <a name="log-searches"></a>Log-sökningar

### <a name="question-i-have-saved-searches-outside-of-my-upgraded-workspace-can-i-convert-them-to-the-new-search-language-automatically"></a>Fråga: Jag har sparade sökningar utanför Min uppgraderade arbetsyta. Kan jag konvertera dem till det nya språket Sök automatiskt?
Du kan använda konverteringsverktyget språk på sidan Logga sökning för att konvertera var och en.  Det finns ingen metod för att automatiskt konvertera flera sökningar utan att uppgradera arbetsytan.

### <a name="question-why-are-my-query-results-not-sorted"></a>Fråga: Varför min frågeresultaten sorteras inte?
Resultaten sorteras inte som standard i det nya språket i fråga.  Använd den [sort-operator](https://go.microsoft.com/fwlink/?linkid=856079) att sortera resultaten av en eller flera egenskaper.

### <a name="question-where-did-the-metrics-view-go-after-i-upgraded"></a>Fråga: Var vyn mått? när jag uppgraderar
Vyn mått gav en grafisk representation av prestandadata från en logg-sökning.  Den här vyn är inte längre tillgängliga efter uppgraderingen.  Du kan använda den [återge operatorn](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/render-operator) att formatera utdata från en fråga i en timechart.

### <a name="question-where-did-minify-go-after-i-upgraded"></a>Fråga: Där minify gå när jag uppgraderar?
Minify är en funktion som ger en sammanfattande vy över sökresultat.  När du har uppgraderat visas Minify alternativet inte längre i loggen Sök-portalen.  Du kan få liknande funktionalitet med den nya Sök språk [minska](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/reduce-operator) eller [autocluster_v2](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/evaluate-operator/autocluster). 

    Event
    | where TimeGenerated > ago(10h)
    | reduce by RenderedDescription

    Event
    | where TimeGenerated > ago(10h)
    | evaluate autocluster_v2()


### <a name="known-issue-search-results-in-a-list-may-include-properties-with-no-data"></a>Kända problem: sökresultat i en lista kan innehålla egenskaper utan data
Loggen sökresultat i en lista kan visa egenskaper utan data.  Före uppgraderingen måste ingå dessa egenskaper inte.  Det här problemet korrigeras så att tomma egenskaper inte visas.

### <a name="known-issue-selecting-a-value-in-a-chart-doesnt-display-detailed-results"></a>Kända problem: du väljer ett värde i ett diagram visas inte detaljerade resultat
När du har markerat ett värde i ett diagram före uppgraderingen, skulle den returnera en detaljerad lista över poster som matchar det markerade värdet.  Efter uppgraderingen returneras endast den sammanfattade raden.  Det här problemet undersöks för närvarande.

## <a name="log-search-api"></a>Loggsöknings-API

### <a name="question-does-the-log-search-api-get-updated-after-i-upgrade"></a>Fråga: Sök-API loggen uppdateras efter uppgraderingen?
Äldre [loggen Sök API](log-analytics-log-search-api.md) inte längre att fungera när du har uppgraderat din arbetsyta.  Se [Azure Log Analytics REST API](https://dev.loganalytics.io/) information om nya API: et.


## <a name="portals"></a>Portaler

### <a name="question-should-i-use-the-new-advanced-analytics-portal-or-keep-using-the-log-search-portal"></a>Fråga: Ska jag använda den nya Advanced Analytics-portalen eller behålla med hjälp av loggen Sök portal?
Du kan se en jämförelse mellan två portaler på [portaler för att skapa och redigera loggen frågor i Azure Log Analytics](log-analytics-log-search-portals.md).  Varje har fördelar så att du kan välja som passar dina behov bäst.  Det är vanligt att skriva frågor i Advanced Analytics-portalen och klistra in i andra platser, till exempel View Designer.  Du bör du läsa om [problem att överväga](log-analytics-log-search-portals.md#advanced-analytics-portal) när gör.


### <a name="question--after-upgrade-i-get-an-error-trying-to-run-queries-and-am-also-seeing-errors-in-my-views"></a>Fråga: Efter uppgradering, jag får ett fel uppstod vid försök att köra frågor och får också fel i Mina vyer.

Din webbläsare kräver åtkomst till följande adresser till logganalys frågor efter uppgraderingen.  Om din webbläsare har åtkomst till Azure-portalen via en brandvägg, måste du aktivera åtkomst till dessa adresser.

| URI: N | IP-adress | Portar |
|:---|:---|:---|
| Portal.loganalytics.IO | Dynamisk | 80,443 |
| API.loganalytics.IO    | Dynamisk | 80,443 |
| docs.loganalytics.IO   | Dynamisk | 80,443 |



## <a name="power-bi"></a>Power BI

### <a name="question-does-anything-change-with-powerbi-integration"></a>Fråga: Ändras något med PowerBI-integration?
Ja.  När ditt arbetsområde har uppgraderats fungerar inte längre processen för att exportera logganalys data till Power BI.  Alla befintliga scheman som du skapade innan du uppgraderar inaktiveras.  Efter uppgraderingen, Azure Log Analytics använder samma plattform som Application Insights och du använder samma process för att exportera logganalys frågor till Power BI som [processen för att exportera Application Insights frågor till Power BI](../application-insights/app-insights-export-power-bi.md#export-analytics-queries).

### <a name="known-issue-power-bi-request-size-limit"></a>Kända problem: storleksgränsen för Powerbi-begäran
Det finns en maximal storlek på 8 MB för en Log Analytics-fråga som kan exporteras till Power BI.  Den här gränsen ska ökas snart.


## <a name="powershell-cmdlets"></a>PowerShell-cmdletar

### <a name="question-does-the-log-search-powershell-cmdlet-get-updated-after-i-upgrade"></a>Fråga: Loggen Sök PowerShell-cmdleten uppdateras efter uppgraderingen?
Den [Get-AzureRmOperationalInsightsSearchResults](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/Get-AzureRmOperationalInsightsSearchResults) att bli inaktuell när uppgraderingen av alla arbetsytor har slutförts.  Använd den [Invoke-LogAnalyticsQuery cmdlet](https://dev.loganalytics.io/documentation/Tools/PowerShell-Cmdlets) loggen sökningar i uppgraderade arbetsytor.




## <a name="resource-manager-templates"></a>Mallar för Resurshanteraren

### <a name="question-can-i-create-an-upgraded-workspace-with-a-resource-manager-template"></a>Fråga: Kan jag skapa en uppgraderad arbetsyta med en Resource Manager-mall?
Ja.  Du måste använda en API-version av 2017-03-15-preview och inkluderar en **funktioner** avsnitt i mallen som i följande exempel.

    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "apiVersion": "2017-03-15-preview",
            "name": "[parameters('workspaceName')]",
            "location": "[parameters('workspaceRegion')]",
            "properties": {
                "sku": {
                    "name": "Free"
                },
                "features": {
                    "legacy": 0,
                    "searchVersion": 1
                }
            }
        }
    ],



## <a name="solutions"></a>Lösningar

### <a name="question-will-my-solutions-continue-to-work"></a>Fråga: Min lösningar fungerar fortfarande?
Alla lösningar fortsätter att fungera i en uppgraderad arbetsyta, även om deras prestanda förbättras om de konverteras till det nya språket i fråga.  Det finns kända problem med vissa befintliga lösningar som beskrivs i det här avsnittet.

### <a name="known-issue-capacity-and-performance-solution"></a>Kända problem: Kapacitets- och lösning
Vissa delar i den [kapacitet och prestanda](log-analytics-capacity.md) vyn kan vara tom.  En lösning på problemet kommer snart att vara tillgänglig.

### <a name="known-issue-application-insights-connector"></a>Kända problem: Application Insights connector
Perspektiv i [Application Insights Connector lösning](log-analytics-app-insights-connector.md) stöds inte för närvarande i en uppgraderad arbetsyta.  En lösning på problemet ligger under analys.

### <a name="known-issue-backup-solution"></a>Kända problem: säkerhetskopieringslösning
Lösningen för säkerhetskopiering inte samlar in data i en uppgraderad arbetsyta. En ny säkerhetskopieringslösning som fungerar med den uppgraderade arbetsytan kommer att utannonseras inom kort.

## <a name="upgrade-process"></a>Uppgraderingsprocessen

### <a name="question-i-have-several-workspaces-can-i-upgrade-all-workspaces-at-the-same-time"></a>Fråga: Jag har flera arbetsytor. Kan jag uppgradera alla arbetsytor på samma gång?  
Nej.  Uppgraderingen gäller en enda arbetsyta varje gång. Det finns för närvarande inget sätt att samtidigt uppgradera många arbetsytor. Observera att andra användare i arbetsytan uppgraderade kommer att påverkas också.  

### <a name="question-will-existing-log-data-collected-in-my-workspace-be-modified-if-i-upgrade"></a>Fråga: Befintliga loggdata som samlas in i Min arbetsyta ändras om jag uppgraderar?  
Nej. Loggdata som är tillgängliga för din arbetsyta sökningar påverkas inte under uppgraderingen. Sparade sökningar, kommer aviseringar, vyer och att konverteras till det nya språket Sök automatiskt.  

### <a name="question-what-happens-if-i-dont-upgrade-my-workspace"></a>Fråga: Vad händer om jag inte uppgradera Min arbetsyta?  
Äldre log-sökningen att bli inaktuell under de kommande månaderna. Arbetsytor som inte uppgraderas av den tiden kommer att uppgraderas automatiskt.

### <a name="question-can-i-revert-back-after-i-upgrade"></a>Fråga: Kan jag återgå tillbaka efter uppgraderingen?
Innan allmän tillgänglighet kan du återställa din arbetsyta efter uppgraderingen.  Nu när det nya språket har nått allmän tillgänglighet, har tagits bort den här funktionen när vi börjar att ta bort den äldra plattformen.



## <a name="views"></a>Vyer

### <a name="question-how-do-i-create-a-new-view-with-view-designer"></a>Fråga: Hur skapar jag en ny vy med Vydesigner?
Före uppgraderingen måste kan du skapa en ny vy med Vydesigner från en panel på huvudinstrumentpanelen.  När din arbetsyta uppgraderas tas den här panelen bort.  Du kan skapa en ny vy med Vydesigner i OMS-portalen genom att klicka på den gröna + knappen i den vänstra menyn.

### <a name="known-issue-see-all-option-for-line-charts-in-views-doesnt-result-in-a-line-chart"></a>Kända problem: se alla alternativ för linjediagram i vyer inte resulterar i ett linjediagram
Om du klickar på den *se alla* alternativet längst ned i en rad diagramdel i en vy visas en tabell.  Före uppgraderingen måste skulle du visas ett linjediagram.  Det här problemet analyseras för eventuella ändringar.


## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [uppgradera din arbetsyta till den nya Log Analytics-frågespråket](log-analytics-log-search-upgrade.md).
