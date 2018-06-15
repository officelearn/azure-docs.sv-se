---
title: Nyheter i Azure Log Analytics? | Microsoft Docs
description: Den här artikeln innehåller vanliga frågor och svar om uppgraderingen av logganalys till det nya språket i fråga.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: bwren
ms.openlocfilehash: 017a1da233827f19489a99b234ee9009fd9f6fe3
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2017
ms.locfileid: "26039867"
---
# <a name="whats-changed-in-azure-log-analytics"></a>Nyheter i Azure Log Analytics?
Förutom frågespråket själva finns flera förbättringar och ändringar som du bör vara medveten om när logganalys-arbetsytan är [uppgraderas till det nya frågespråket](log-analytics-log-search-new.md).  Den här artikeln beskrivs kortfattat ändringar mellan en äldre och uppgraderade arbetsyta med länkar till detaljerad innehåll. 

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Whats-changed-in-Azure-Log-Analytics/player]

Se [logganalys nya loggar Sök vanliga frågor och kända problem](log-analytics-log-search-faq.md) en beskrivning av några kända problem med uppgradering och svar på vanliga frågor.  

## <a name="query-language"></a>Frågespråk
Den primära förändringen i logganalys-uppgraderingen är nya frågespråk som har betydande förbättringar över tidigare språk.  

Du kan få en direkt jämförelse av vanliga åtgärder mellan det äldre språket och det nya språket på [övergå till nya Azure Log Analytics-frågespråket](log-analytics-log-search-transition.md).  Komplett dokumentation om det nya språket är tillgängligt på [Azure Log Analytics-frågespråket](https://docs.loganalytics.io).


## <a name="computer-groups"></a>Datorgrupper
Metod för att skapa en datorgrupp har inte ändrats, även om du nu måste ange ett unikt alias för var och en.  Datorgrupper baserat på loggen sökningar måste använda syntaxen för det nya språket.

Det finns en ny syntax för att använda datorgrupper i en sökning i loggen.  I stället för att använda funktionen $ComputerGroups är datorgrupper nu varje implementeras som en funktion med ett unikt alias.  Du kan använda detta alias i loggen sökning som någon annan funktion.  

Information finns på [datorgrupper i logganalys logga sökningar](log-analytics-computer-groups.md).


## <a name="log-search-portals"></a>Loggsökningsportaler
Förutom loggen Sök-portal att skapa och köra loggen söker använda du nu Advanced Analytics-portalen som ger avsevärt förbättrade funktioner för redigering.

En kort beskrivning av två portaler finns på [portaler för att skapa och redigera loggen frågor i Azure Log Analytics](log-analytics-log-search-portals.md).  Du kan gå igenom en självstudiekurs på den nya portalen på [komma igång med Analytics-portalen](https://docs.loganalytics.io/docs/Learn/Getting-Started/Getting-started-with-the-Analytics-portal).

## <a name="alerts"></a>Aviseringar
Aviseringar fungerar på samma sätt i uppgraderade arbetsytor, men den fråga som de körs måste skrivas i det nya språket.  Alla befintliga Varningsregler som du hade före uppgraderingen konverteras automatiskt till det nya språket.  Du kan få mer information i [förstå aviseringar i logganalys](log-analytics-alerts.md).

Formatet på nyttolasten för runbooks och webhooks som skickas från aviseringar har ändrats.  Hämta information för det nya nyttolastformatet i [lägga till åtgärder i Varningsregler i logganalys](log-analytics-alerts-actions.md).

## <a name="dashboards"></a>Instrumentpaneler
[Instrumentpaneler](log-analytics-dashboards.md) håller på att inaktuell.  Du kan fortsätta att använda alla paneler som du lagt till instrumentpanelen innan ditt arbetsområde har uppgraderats, men du kan inte redigera dessa paneler eller lägga till nya.  Använd View Designer för att skapa anpassade vyer som har en mer omfattande funktioner än instrumentpaneler.

Information om Vydesigner finns på [Använd View Designer för att skapa anpassade vyer i logganalys](log-analytics-view-designer.md).

## <a name="power-bi"></a>Power BI
Processen för att exportera logganalys data till Power BI har ändrats för uppgraderade arbetsytor och inaktiveras alla befintliga scheman som du skapade innan du uppgraderar.  

Information finns på [logganalys exportera data till Power BI](log-analytics-powerbi.md).

## <a name="powershell"></a>PowerShell
Get-AzureRmOperationalInsightsSearchResults för att köra loggen sökningar från PowerShell fungerar inte med en uppgraderad arbetsyta.  Använd Invoke-LogAnalyticsQuery för den här funktionen med en uppgraderad arbetsyta.

Information finns på [Azure Log Analytics REST API - PowerShell-Cmdlets](https://dev.loganalytics.io/documentation/Tools/PowerShell-Cmdlets).

## <a name="log-search-api"></a>Logga Sök-API
Log-sökningen REST API har ändrats och lösningar som använder den äldra versionen måste uppdateras för att använda den nya versionen av API: et.   

Information om den nya versionen av API: et finns på [Azure Log Analytics REST API](https://dev.loganalytics.io/).

## <a name="next-steps"></a>Nästa steg

- Se [logganalys nya loggar Sök vanliga frågor och kända problem](log-analytics-log-search-faq.md) en beskrivning av några kända problem med uppgradering och svar på vanliga frågor.