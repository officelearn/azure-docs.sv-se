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
ms.date: 11/02/2017
ms.author: bwren
ms.openlocfilehash: 1ec815a12cea98228dd4b7ac7361fe5e3554b5d3
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2017
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
Ja.  När ditt arbetsområde har uppgraderats fungerar inte längre processen för att exportera logganalys data till Power BI.  Alla befintliga scheman som du skapade innan du uppgraderar inaktiveras.  

Efter uppgraderingen, Azure Log Analytics använder samma plattform som Application Insights och du använder samma process för att exportera logganalys frågor till Power BI som [processen för att exportera Application Insights frågor till Power BI](../application-insights/app-insights-export-power-bi.md#export-analytics-queries).  Export till Power BI anropar nu direkt API-slutpunkt. Detta gör att du kan få upp till 500 000 rader eller 64,000,000 byte data, exportera långa frågor och anpassa tidsgränsen för frågan (standardvärdet är 3 minuter och högsta timeout-värdet är 10 minuter).

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

### <a name="known-issue-perspectives-in-application-insights-connector"></a>Kända problem: perspektiv i Application Insights connector
Perspektiv i [Application Insights Connector lösning](log-analytics-app-insights-connector.md) stöds inte längre i Application Insights connector lösningen.  Du kan använda View Designer för att skapa anpassade vyer med Application Insights-data.

### <a name="known-issue-backup-solution"></a>Kända problem: säkerhetskopieringslösning
Lösningen för säkerhetskopiering kan inte samla in data om har installerats innan du uppgraderar en arbetsyta. Avinstallera lösningen och installera den senaste versionen.  Den nya versionen av lösningen har inte stöd för klassiska säkerhetskopieringsvalv, så måste du också uppgradera till Recovery Services-valv att använda lösningen.

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


## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [uppgradera din arbetsyta till den nya Log Analytics-frågespråket](log-analytics-log-search-upgrade.md).
