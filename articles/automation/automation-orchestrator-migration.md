---
title: "Migrera från Orchestrator till Azure Automation | Microsoft Docs"
description: "Beskriver hur du migrerar runbooks och integration packs från System Center Orchestrator till Azure Automation."
services: automation
documentationcenter: 
author: bwren
manager: stevenka
editor: tysonn
ms.assetid: 1a7da58c-7a98-49b5-9d9d-001a9f6e631a
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2016
ms.author: bwren
ms.openlocfilehash: 457888b4d38875b912ad87d44e96ab727e3ee3ee
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="migrating-from-orchestrator-to-azure-automation-beta"></a>Migrera från Orchestrator till Azure Automation (Beta)
Runbooks i [System Center Orchestrator](http://technet.microsoft.com/library/hh237242.aspx) baseras på aktiviteter från integrationspaketen som är skrivna specifikt för Orchestrator medan runbooks i Azure Automation baseras på Windows PowerShell.  [Grafiska runbook-flöden](automation-runbook-types.md#graphical-runbooks) i Azure Automation liknande utseende för Orchestrator-runbooks har med deras aktiviteter som representerar PowerShell cmdlets, underordnade runbooks och tillgångar.

Den [System Center Orchestrator Migration Toolkit](http://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) innehåller verktyg som hjälper dig att konvertera runbooks från Orchestrator till Azure Automation.  Förutom konverterar runbooks själva, måste du konvertera integreringspaket med aktiviteter som använder runbooks till integreringsmoduler med Windows PowerShell-cmdlets.  

Följande är den grundläggande processen för konvertering av Orchestrator-runbooks till Azure Automation.  Var och en av dessa steg beskrivs i detalj i avsnitten nedan.

1. Hämta den [System Center Orchestrator Migration Toolkit](http://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) som innehåller de verktyg och moduler som beskrivs i den här artikeln.
2. Importera [standardaktiviteter modulen](#standard-activities-module) i Azure Automation.  Detta inkluderar konverterade versioner av standard Orchestrator-aktiviteter som kan användas av konverterade runbooks.
3. Importera [integreringsmoduler för System Center Orchestrator](#system-center-orchestrator-integration-modules) i Azure Automation för dessa integreringspaket som används av runbooks som har åtkomst till System Center.
4. Konvertera anpassade och integreringspaket för tredje part med hjälp av den [Integration Pack konverteraren](#integration-pack-converter) och importera till Azure Automation.
5. Konvertera Orchestrator runbooks med den [Runbook konverteraren](#runbook-converter) och installera i Azure Automation.
6. Manuellt skapa nödvändiga Orchestrator tillgångar i Azure Automation eftersom Runbook-konverteraren inte konverterar dessa resurser.
7. Konfigurera en [Hybrid Runbook Worker](#hybrid-runbook-worker) i ditt lokala Datacenter att köra konverterade runbooks som får åtkomst till lokala resurser.

## <a name="service-management-automation"></a>Service Management Automation
[Service Management Automation](http://technet.microsoft.com/library/dn469260.aspx) (SMA) lagrar och kör runbooks i ditt lokala datacenter som Orchestrator och används samma integreringsmoduler som Azure Automation. Den [Runbook konverteraren](#runbook-converter) konverterar Orchestrator runbooks till grafiska runbook-flöden men som inte stöds i SMA.  Du kan fortfarande installera den [Standard aktiviteter modulen](#standard-activities-module) och [integreringsmoduler för System Center Orchestrator](#system-center-orchestrator-integration-modules) till SMA, men du måste manuellt [Skriv om dina runbooks](http://technet.microsoft.com/library/dn469262.aspx).

## <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker
Runbooks i Orchestrator lagras på en databasserver och körs på runbook-servrar, både i ditt lokala datacenter.  Runbooks i Azure Automation lagras i Azure-molnet och kan köras i din lokala data center genom att använda en [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md).  Detta är hur kommer du vanligtvis köra runbooks konverteras från Orchestrator eftersom de har utformats för att köra på lokala servrar.

## <a name="integration-pack-converter"></a>Integration Pack konverterare
Integration Pack Konverteraren konverterar integreringspaket som har skapats med den [Orchestrator Integration Toolkit (OIT)](http://technet.microsoft.com/library/hh855853.aspx) till integreringsmoduler baserat på Windows PowerShell som kan importeras till Azure Automation eller Service Management Automation.  

När du kör Integration Pack konverteraren visas med en guide som gör att du kan välja en integrationspaketfilen (.oip).  Guiden visar en lista över aktiviteter som ingår i paketet sedan och kan du välja som ska migreras.  När du har slutfört guiden skapar en modul för integrering med en motsvarande cmdlet för alla aktiviteter i det ursprungliga integrationspaketet.

### <a name="parameters"></a>Parametrar
Alla egenskaper för en aktivitet i integreringspaketet konverteras till parametrarna för motsvarande cmdlet i modulen integrering.  Windows PowerShell-cmdlets har en uppsättning [gemensamma parametrar](http://technet.microsoft.com/library/hh847884.aspx) som kan användas med alla cmdletar.  Exempelvis - Verbose-parametern orsakar en cmdlet till utdata detaljerad information om driften.  Inga cmdlet kan ha en parameter med samma namn som en gemensam parameter.  Om en aktivitet har en egenskap med samma namn som en gemensam parametern, uppmanas du att ange ett annat namn för parametern.

### <a name="monitor-activities"></a>Övervakaraktiviteter
Övervaka runbooks i Orchestrator starta med en [övervaka aktiviteten](http://technet.microsoft.com/library/hh403827.aspx) och kör kontinuerligt väntar på att anropas av en viss händelse.  Azure Automation stöder inte övervaka runbooks, så alla övervakaraktiviteter i integreringspaketet inte konverteras.  I stället skapas en platshållare cmdlet i modulen integration för övervakaraktiviteten.  Denna cmdlet har inga funktioner, men det gör att alla konverterade runbook som används för att installeras.  Denna runbook inte kan köras i Azure Automation, men den kan installeras så att du kan ändra den.

### <a name="integration-packs-that-cannot-be-converted"></a>Integreringspaket som inte kan konverteras
Integreringspaket som inte har skapats med OIT kan inte konverteras med Integration Pack konverterare. Det finns även vissa integreringspaket som tillhandahålls av Microsoft som för närvarande inte kan konverteras med det här verktyget.  Konverterade versioner av integreringspaketen har [som finns att hämta](#system-center-orchestrator-integration-modules) så att de kan installeras i Azure Automation eller Service Management Automation.

## <a name="standard-activities-module"></a>Standardaktiviteter modul
Orchestrator innehåller en uppsättning [standardaktiviteter](http://technet.microsoft.com/library/hh403832.aspx) som inte ingår i ett integreringspaket men används av många runbooks.  Standardaktiviteter modul är en modul för integrering med en motsvarande cmdlet för var och en av dessa aktiviteter.  Innan du importerar alla konverterade runbooks som använder en aktivitet som standard måste du installera den här integrationsmodul i Azure Automation.

Förutom att konverterade runbooks, kan cmdletar i modulen standardaktiviteter användas av någon som är bekant med Orchestrator för att skapa nya runbooks i Azure Automation.  När funktionen för alla aktiviteter som standard kan utföras med cmdlet: ar, fungerar de på olika sätt.  Cmdlets i modulen konverterade standardaktiviteter fungerar samma som deras motsvarande aktiviteter och använda samma parametrar.  Detta kan hjälpa befintliga Orchestrator runbook-redigerare i sina övergången till Azure Automation-runbooks.

## <a name="system-center-orchestrator-integration-modules"></a>System Center Orchestrator integreringsmoduler
Microsoft tillhandahåller [integreringspaket](http://technet.microsoft.com/library/hh295851.aspx) för att skapa runbooks för att automatisera System Center-komponenter och andra produkter.  Vissa av dessa integreringspaket för närvarande är baserade på OIT men för närvarande inte kan konverteras till integreringsmoduler på grund av kända problem.  [System Center Orchestrator integreringsmoduler](https://www.microsoft.com/download/details.aspx?id=49555) innehåller konverterade versioner av dessa integreringspaket som kan importeras till Azure Automation och Service Management Automation.  

Uppdaterade versioner av integreringspaket baserat på OIT som kan konverteras med Integration Pack konverterare av RTM-versionen av det här verktyget kommer att publiceras.  Vägledning ges också för att hjälpa dig att konvertera runbooks med hjälp av aktiviteter från integrationspaketen som inte baseras på OIT.

## <a name="runbook-converter"></a>Runbook-konverteraren
Runbook-Konverteraren konverterar Orchestrator runbooks till [grafiska runbook-flöden](automation-runbook-types.md#graphical-runbooks) som kan importeras till Azure Automation.  

Runbook-konverteraren implementeras som en PowerShell-modul med en cmdlet som kallas **ConvertFrom SCORunbook** som utför konverteringen.  När du installerar verktyget, skapas en genväg till en PowerShell-session som läser in cmdlet.   

Följande är den grundläggande processen konvertera en Orchestrator-runbook och importera den till Azure Automation.  Följande avsnitt innehåller ytterligare information om verktyget och arbetar med konverterade runbooks.

1. Exportera en eller flera runbooks från Orchestrator.
2. Hämta integreringsmoduler för alla aktiviteter i runbook.
3. Konvertera Orchestrator-runbooks i den exporterade filen.
4. Granska informationen i loggarna att validera konverteringen och för att fastställa alla manuella uppgifter som krävs.
5. Importera konverterade runbooks i Azure Automation.
6. Skapa alla nödvändiga resurser i Azure Automation.
7. Redigera runbook i Azure Automation för att ändra alla aktiviteter som krävs.

### <a name="using-runbook-converter"></a>Med Runbook-konverteraren
Syntaxen för **ConvertFrom SCORunbook** är följande:

    ConvertFrom-SCORunbook -RunbookPath <string> -Module <string[]> -OutputFolder <string>

* RunbookPath - sökvägen till exportfilen som innehåller runbook för att konvertera.
* Modul - kommaavgränsad lista över integreringsmoduler som innehåller aktiviteter i runbooks.
* OutputFolder - sökvägen till mappen för att skapa konverteras grafiska runbook-flöden.

Följande exempelkommando konverterar runbooks i en export-fil som kallas **MyRunbooks.ois_export**.  Dessa runbooks använder integreringspaket Active Directory och Data Protection Manager.

    ConvertFrom-SCORunbook -RunbookPath "c:\runbooks\MyRunbooks.ois_export" -Module c:\ip\SystemCenter_IntegrationModule_ActiveDirectory.zip,c:\ip\SystemCenter_IntegrationModule_DPM.zip -OutputFolder "c:\runbooks"


### <a name="log-files"></a>Loggfiler
Följande loggfiler skapas på samma plats som den konverterade runbooken Runbook-konverteraren.  Om filerna redan finns, skrivs sedan de över med informationen från den senaste konverteringen.

| Fil | Innehåll |
|:--- |:--- |
| Runbook-konverteraren - Progress.log |Detaljerade anvisningar för konverteringen inklusive information om varje aktivitet har konverterats och varning för varje aktivitet som inte konverteras. |
| Runbook-konverteraren - Summary.log |Sammanfattning av senaste konverteringen inklusive alla varningar och följa upp uppgifter som du behöver utföra, till exempel skapa en variabel som krävs för den konverterade runbooken. |

### <a name="exporting-runbooks-from-orchestrator"></a>Exportera runbooks från Orchestrator
Runbook-konverteraren fungerar med en exportfil från Orchestrator som innehåller en eller flera runbooks.  Den skapar en motsvarande Azure Automation-runbook för varje runbook i Orchestrator i exportfilen.  

Om du vill exportera en runbook från Orchestrator, högerklicka på namnet på runbook i Runbook Designer och välj **exportera**.  Om du vill exportera alla runbooks i en mapp, högerklickar du på namnet på mappen och välj **exportera**.

### <a name="runbook-activities"></a>Runbook-aktiviteter
Runbook-Konverteraren konverterar varje aktivitet i Orchestrator-runbook till en motsvarande aktivitet i Azure Automation.  För de aktiviteter som inte kan konverteras, skapas en platshållare för aktivitet i runbooken med varningstext.  När du har importerat konverterade runbook till Azure Automation måste du ersätta någon av dessa aktiviteter med giltig aktiviteter som utför de nödvändiga funktionerna.

Alla Orchestrator-aktiviteter i den [Standard aktiviteter modulen](#standard-activities-module) ska konverteras.  Det finns vissa standard Orchestrator-aktiviteter som inte ingår i den här modulen men och konverteras inte.  Till exempel **skicka Plattformshändelsen** inte har något Azure Automation-motsvarande eftersom händelsen är specifika för Orchestrator.

[Övervaka aktiviteter](https://technet.microsoft.com/library/hh403827.aspx) konverteras inte eftersom det finns ingen motsvarighet till dem i Azure Automation.  Undantaget är övervakaren aktiviteter i [konverteras integreringspaket](#integration-pack-converter) som kommer att konverteras till aktiviteten platshållare.

Alla aktiviteter från en [konverteras integreringspaketet](#integration-pack-converter) konverteras om du anger sökvägen till modul för integrering med den **moduler** parameter.  För System Center-integreringspaket, kan du använda den [integreringsmoduler för System Center Orchestrator](#system-center-orchestrator-integration-modules).

### <a name="orchestrator-resources"></a>Orchestrator-resurser
Runbook-Konverteraren konverterar enbart runbooks, inte andra informationskällor för Orchestrator, till exempel räknare, variabler eller anslutningar.  Räknare stöds inte i Azure Automation.  Variabler och anslutningar stöds, men du måste skapa dem manuellt.  Loggfilerna ska får du information om runbook kräver sådana resurser och ange motsvarande resurser som du behöver skapa i Azure Automation för konverterade runbook ska fungera korrekt.

En runbook kan till exempel använda en variabel för att fylla i ett visst värde i en aktivitet.  Konverterade runbook konverterar aktiviteten och ange en variabel tillgång i Azure Automation med samma namn som Orchestrator-variabeln.  Detta kommer att märka i den **Runbook konverteraren - Summary.log** filen som skapas efter konverteringen.  Du måste skapa den här variabeltillgång manuellt i Azure Automation innan du använder runbook.

### <a name="input-parameters"></a>Indataparametrar
Runbooks i Orchestrator godkänna indataparametrar med den **initiera Data** aktivitet.  Om runbook omvandlas innehåller den här aktiviteten och sedan en [Indataparametern](automation-graphical-authoring-intro.md#runbook-input-and-output) i Azure Automation runbook skapas för varje parameter i aktiviteten.  En [Arbetsflödesskriptet kontrollen](automation-graphical-authoring-intro.md#activities) aktivitet skapas i den konverterade runbook som hämtar och returnerar varje parameter.  Aktiviteter i runbook som använder en indataparameter referera till utdata från den här aktiviteten.

Orsaken till att den här strategin används är för bäst spegling funktioner i Orchestrator-runbook.  Aktiviteter i nya grafiska runbook-flöden bör finns direkt indataparametrar med hjälp av en Runbook indata källa.

### <a name="invoke-runbook-activity"></a>Aktiviteten anropa Runbook
Runbooks i Orchestrator starta andra runbooks med den **anropa Runbook** aktivitet. Om runbook omvandlas innehåller den här aktiviteten och **vänta på slutförande** alternativet anges kommer en runbook-aktivitet skapas för den konverterade runbook.  Om den **vänta på slutförande** alternativet inte anges, och sedan en Arbetsflödesskriptet aktivitet skapas som använder **Start AzureAutomationRunbook** att starta runbook.  När du har importerat konverterade runbook till Azure Automation måste du ändra den här aktiviteten med den information som anges i aktiviteten.

## <a name="related-articles"></a>Relaterade artiklar
* [System Center 2012 – Orchestrator](http://technet.microsoft.com/library/hh237242.aspx)
* [Service Management Automation](https://technet.microsoft.com/library/dn469260.aspx)
* [Runbook Worker-hybrid](automation-hybrid-runbook-worker.md)
* [Standardaktiviteter för orchestrator](http://technet.microsoft.com/library/hh403832.aspx)
* [Hämta System Center Orchestrator Migration Toolkit](https://www.microsoft.com/en-us/download/details.aspx?id=47323)
