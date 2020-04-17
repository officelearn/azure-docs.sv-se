---
title: Migrera från Orchestrator till Azure Automation
description: Beskriver hur du migrerar runbooks och integrationspaket från System Center Orchestrator till Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: c7df6e31cd021fc61129131f9bd02acc7b96e2ad
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457560"
---
# <a name="migrating-from-orchestrator-to-azure-automation-beta"></a>Migrera från Orchestrator till Azure Automation (Beta)

Runbooks i [System Center Orchestrator](https://technet.microsoft.com/library/hh237242.aspx) baseras på aktiviteter från integrationspaket som är skrivna specifikt för Orchestrator medan runbooks i Azure Automation baseras på Windows PowerShell.  [Grafiska runbooks](automation-runbook-types.md#graphical-runbooks) i Azure Automation har ett liknande utseende som Orchestrator runbooks med sina aktiviteter som representerar PowerShell-cmdlets, underordnade runbooks och tillgångar.

[System Center Orchestrator Migration Toolkit](https://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) innehåller verktyg som hjälper dig att konvertera runbooks från Orchestrator till Azure Automation.  Förutom att konvertera själva runbooks måste du konvertera integrationspaketen med de aktiviteter som runbooks använder för att integrera moduler med Windows PowerShell-cmdlets.  

Följande är den grundläggande processen för att konvertera Orchestrator runbooks till Azure Automation.  Vart och ett av dessa steg beskrivs i detalj i avsnitten nedan.

1. Hämta [System Center Orchestrator Migration Toolkit](https://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) som innehåller de verktyg och moduler som beskrivs i den här artikeln.
2. Importera [standardaktivitetermodul](#standard-activities-module) till Azure Automation.  Detta inkluderar konverterade versioner av standard Orchestrator-aktiviteter som kan användas av konverterade runbooks.
3. Importera [System Center Orchestrator Integration Modules](#system-center-orchestrator-integration-modules) till Azure Automation för de integrationspaket som används av dina runbooks som får åtkomst till System Center.
4. Konvertera anpassade och tredjepartsintegrationspaket med [integrationspaketkonverteraren](#integration-pack-converter) och importera till Azure Automation.
5. Konvertera Orchestrator-runbooks med [Runbook Converter](#runbook-converter) och installera i Azure Automation.
6. Skapa obligatoriska Orchestrator-resurser manuellt i Azure Automation eftersom Runbook Converter inte konverterar dessa resurser.
7. Konfigurera en [Hybrid Runbook Worker](#hybrid-runbook-worker) i det lokala datacentret för att köra konverterade runbooks som kommer åt lokala resurser.

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installationsinstruktioner för Az-modul på hybridkörningsarbetaren finns [i Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med [så här uppdaterar du Azure PowerShell-moduler i Azure Automation](automation-update-azure-modules.md).

## <a name="service-management-automation"></a>Service Management Automation

[Service Management Automation](https://technet.microsoft.com/library/dn469260.aspx) (SMA) lagrar och kör runbooks i ditt lokala datacenter som Orchestrator, och använder samma integrationsmoduler som Azure Automation. [Runbook Converter](#runbook-converter) konverterar Orchestrator runbooks till grafiska runbooks men som inte stöds i SMA.  Du kan fortfarande installera [standardmodulen för aktiviteter](#standard-activities-module) och [systemcenter orchestratorintegrationsmoduler](#system-center-orchestrator-integration-modules) i SMA, men du måste [skriva om dina runbooks](https://technet.microsoft.com/library/dn469262.aspx)manuellt .

## <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker

Runbooks i Orchestrator lagras på en databasserver och körs på runbook-servrar, både i ditt lokala datacenter.  Runbooks i Azure Automation lagras i Azure-molnet och kan köras i ditt lokala datacenter med hjälp av en [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md).  Det är så här du vanligtvis kör runbooks konverterade från Orchestrator eftersom de är utformade för att köras på lokala servrar.

## <a name="integration-pack-converter"></a>Konverterare för integrationspaket

Integration Pack Converter konverterar integrationspaket som har skapats med hjälp av [Orchestrator Integration Toolkit (OIT)](https://technet.microsoft.com/library/hh855853.aspx) till integrationsmoduler baserade på Windows PowerShell som kan importeras till Azure Automation eller Service Management Automation.  

När du kör Integration Pack Converter visas en guide som gör att du kan välja en integrationspaketfil (.oip).  Guiden visar sedan de aktiviteter som ingår i det integrationspaketet och låter dig välja vilka som ska migreras.  När du är klar med guiden skapas en integrationsmodul som innehåller en motsvarande cmdlet för var och en av aktiviteterna i det ursprungliga integrationspaketet.

### <a name="parameters"></a>Parametrar

Alla egenskaper för en aktivitet i integrationspaketet konverteras till parametrar för motsvarande cmdlet i integrationsmodulen.  Windows PowerShell-cmdlets har en uppsättning [vanliga parametrar](https://technet.microsoft.com/library/hh847884.aspx) som kan användas med alla cmdlets. Parametern -Verbose gör till exempel att en cmdlet matar ut detaljerad information om dess funktion.  Ingen cmdlet kan ha en parameter med samma namn som en vanlig parameter. Om en aktivitet har en egenskap med samma namn som en vanlig parameter uppmanas du att ange ett annat namn för parametern.

### <a name="monitor-activities"></a>Övervaka aktiviteter

Övervaka runbooks i Orchestrator börjar med en [övervakningsaktivitet](https://technet.microsoft.com/library/hh403827.aspx) och körs kontinuerligt väntar på att anropas av en viss händelse.  Azure Automation stöder inte övervaka runbooks, så alla övervakningsaktiviteter i integrationspaketet konverteras inte.  I stället skapas en platshållar cmdlet i integrationsmodulen för övervakningsaktiviteten.  Den här cmdleten har ingen funktion, men den tillåter alla konverterade runbook som använder den för att installeras.  Den här runbooken kommer inte att kunna köras i Azure Automation, men den kan installeras så att du kan ändra den.

### <a name="integration-packs-that-cannot-be-converted"></a>Integrationspaket som inte kan konverteras

Integrationspaket som inte har skapats med OIT kan inte konverteras med Integration Pack Converter. Det finns också vissa integrationspaket som tillhandahålls av Microsoft och som för närvarande inte kan konverteras med det här verktyget.  Konverterade versioner av dessa integrationspaket har [tillhandahållits för hämtning](#system-center-orchestrator-integration-modules) så att de kan installeras i Azure Automation eller Service Management Automation.

## <a name="standard-activities-module"></a>Module för standardaktiviteter

Orchestrator innehåller en uppsättning [standardaktiviteter](https://technet.microsoft.com/library/hh403832.aspx) som inte ingår i ett integrationspaket men som används av många runbooks.  Modulen Standardaktiviteter är en integrationsmodul som innehåller en cmdletekvivalent för var och en av dessa aktiviteter.  Du måste installera den här integrationsmodulen i Azure Automation innan du importerar konverterade runbooks som använder en standardaktivitet.

Förutom att stödja konverterade runbooks kan cmdlets i standardaktivitetsmodulen användas av någon som är bekant med Orchestrator för att skapa nya runbooks i Azure Automation.  Även om funktionaliteten hos alla standardaktiviteter kan utföras med cmdlets, kan de fungera annorlunda.  Cmdlets i den konverterade standardaktiviteter modulen kommer att fungera på samma sätt som deras motsvarande aktiviteter och använda samma parametrar.  Detta kan hjälpa den befintliga Orchestrator runbook-författaren i övergången till Azure Automation-runbooks.

## <a name="system-center-orchestrator-integration-modules"></a>Integrationsmoduler för systemcenter orchestrator

Microsoft tillhandahåller [integrationspaket](https://technet.microsoft.com/library/hh295851.aspx) för att skapa runbooks för att automatisera System Center-komponenter och andra produkter.  Vissa av dessa integrationspaket baseras för närvarande på OIT men kan för närvarande inte konverteras till integrationsmoduler på grund av kända problem.  [System Center Orchestrator Integration Modules](https://www.microsoft.com/download/details.aspx?id=49555) innehåller konverterade versioner av dessa integrationspaket som kan importeras till Azure Automation och Service Management Automation.  

Genom RTM-versionen av det här verktyget publiceras uppdaterade versioner av integrationspaketen baserat på OIT som kan konverteras med Integration Pack Converter.  Vägledning kommer också att ges för att hjälpa dig att konvertera runbooks med hjälp av aktiviteter från integrationspaket som inte baseras på OIT.

## <a name="runbook-converter"></a>Konverterare för Runbook

Runbook Converter konverterar Orchestrator-runbooks till [grafiska runbooks](automation-runbook-types.md#graphical-runbooks) som kan importeras till Azure Automation.  

Runbook Converter implementeras som en PowerShell-modul `ConvertFrom-SCORunbook` med en cmdlet som kallas som utför konverteringen.  När du installerar verktyget skapas en genväg till en PowerShell-session som läser in cmdleten.   

Följande är den grundläggande processen för att konvertera en Orchestrator runbook och importera den till Azure Automation.  Följande avsnitt innehåller ytterligare information om hur du använder verktyget och arbetar med konverterade runbooks.

1. Exportera en eller flera runbooks från Orchestrator.
2. Hämta integrationsmoduler för alla aktiviteter i runbooken.
3. Konvertera Orchestrator-runbooks i den exporterade filen.
4. Granska information i loggar för att validera konverteringen och för att fastställa eventuella nödvändiga manuella uppgifter.
5. Importera konverterade runbooks till Azure Automation.
6. Skapa alla nödvändiga resurser i Azure Automation.
7. Redigera runbooken i Azure Automation för att ändra nödvändiga aktiviteter.

### <a name="using-runbook-converter"></a>Använda Runbook Converter

Syntaxen för **ConvertFrom-SCORunbook** är följande:

```powershell
ConvertFrom-SCORunbook -RunbookPath <string> -Module <string[]> -OutputFolder <string>
```

* RunbookPath - Sökväg till exportfilen som innehåller de runbooks som ska konverteras.
* Modul - Kommaavgränsad lista över integrationsmoduler som innehåller aktiviteter i runbooks.
* OutputFolder - Sökväg till mappen för att skapa konverterade grafiska runbooks.

Följande exempelkommando konverterar runbooks i en exportfil som heter **MyRunbooks.ois_export**.  Dessa runbooks använder Active Directory och Data Protection Manager integrationspaket.

```powershell
ConvertFrom-SCORunbook -RunbookPath "c:\runbooks\MyRunbooks.ois_export" -Module c:\ip\SystemCenter_IntegrationModule_ActiveDirectory.zip,c:\ip\SystemCenter_IntegrationModule_DPM.zip -OutputFolder "c:\runbooks"
```

### <a name="log-files"></a>Loggfiler

Runbook Converter skapar följande loggfiler på samma plats som den konverterade runbooken.  Om filerna redan finns skrivs de över med information från den senaste konverteringen.

| Fil | Innehåll |
|:--- |:--- |
| Runbook Converter - Progress.log |Detaljerade steg i konverteringen, inklusive information för varje aktivitet som har konverterats och varnat för varje aktivitet som inte konverterats. |
| Runbook Converter - Summary.log |Sammanfattning av den senaste konverteringen inklusive eventuella varningar och uppföljningsuppgifter som du behöver utföra, till exempel skapa en variabel som krävs för den konverterade runbooken. |

### <a name="exporting-runbooks-from-orchestrator"></a>Exportera runbooks från Orchestrator

Runbook Converter fungerar med en exportfil från Orchestrator som innehåller en eller flera runbooks.  Det kommer att skapa en motsvarande Azure Automation runbook för varje Orchestrator runbook i exportfilen.  

Om du vill exportera en runbook från Orchestrator högerklickar du på namnet på runbooken i Runbook Designer och väljer **Exportera**.  Om du vill exportera alla runbooks i en mapp högerklickar du på mappens namn och väljer **Exportera**.

### <a name="runbook-activities"></a>Runbook-aktiviteter

Runbook Converter konverterar varje aktivitet i Orchestrator-runbooken till en motsvarande aktivitet i Azure Automation.  För de aktiviteter som inte kan konverteras skapas en platshållaraktivitet i runbooken med varningstext.  När du har importerat den konverterade runbooken till Azure Automation måste du ersätta någon av dessa aktiviteter med giltiga aktiviteter som utför de funktioner som krävs.

Alla Orchestrator-aktiviteter i [modulen Standardaktiviteter](#standard-activities-module) konverteras.  Det finns vissa vanliga Orchestrator aktiviteter som inte finns i den här modulen dock och inte konverteras.  Har till `Send Platform Event` exempel ingen Azure Automation-motsvarighet eftersom händelsen är specifik för Orchestrator.

[Övervakningsaktiviteter](https://technet.microsoft.com/library/hh403827.aspx) konverteras inte eftersom det inte finns någon motsvarighet till dem i Azure Automation.  Undantaget är övervakningsaktiviteter i [konverterade integrationspaket](#integration-pack-converter) som konverteras till platshållaraktiviteten.

Alla aktiviteter från ett [konverterat integrationspaket](#integration-pack-converter) konverteras om du anger `modules` sökvägen till integrationsmodulen med parametern. För System Center Integration Packs kan du använda [System Center Orchestrator Integration Modules](#system-center-orchestrator-integration-modules).

### <a name="orchestrator-resources"></a>Orchestrator-resurser

Runbook Converter konverterar bara runbooks, inte andra Orchestrator-resurser, till exempel räknare, variabler eller anslutningar.  Räknare stöds inte i Azure Automation.  Variabler och anslutningar stöds, men du måste skapa dem manuellt.  Loggfilerna informerar dig om runbook kräver sådana resurser och ange motsvarande resurser som du behöver skapa i Azure Automation för att den konverterade runbook ska fungera korrekt.

En runbook kan till exempel använda en variabel för att fylla i ett visst värde i en aktivitet.  Den konverterade runbooken konverterar aktiviteten och anger en variabel tillgång i Azure Automation med samma namn som Orchestrator-variabeln.  Detta kommer att noteras i **filen Runbook Converter - Summary.log** som skapas efter konverteringen.  Du måste skapa den här variabel tillgången manuellt i Azure Automation innan du använder runbooken.

### <a name="input-parameters"></a>Indataparametrar

Runbooks i Orchestrator accepterar `Initialize Data` indataparametrar med aktiviteten.  Om runbooken som konverteras innehåller den här aktiviteten skapas en [indataparameter](automation-graphical-authoring-intro.md#runbook-input-and-output) i Azure Automation-runbooken för varje parameter i aktiviteten.  En [arbetsflödesskriptkontrollaktivitet](automation-graphical-authoring-intro.md#activities) skapas i den konverterade runbook som hämtar och returnerar varje parameter.  Alla aktiviteter i runbooken som använder en indataparameter refererar till utdata från den här aktiviteten.

Anledningen till att den här strategin används är att bäst spegla funktionerna i Orchestrator-runbooken.  Aktiviteter i nya grafiska runbooks bör referera direkt till indataparametrar med hjälp av en Runbook-indatakälla.

### <a name="invoke-runbook-activity"></a>Anropa Runbook-aktivitet

Runbooks i Orchestrator starta andra `Invoke Runbook` runbooks med aktiviteten. Om runbooken som konverteras innehåller `Wait for completion` den här aktiviteten och alternativet är inställt, skapas en runbook-aktivitet för den i den konverterade runbooken.  Om `Wait for completion` alternativet inte är inställt skapas en arbetsflödesskriptaktivitet som använder [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) för att starta runbooken. När du har importerat den konverterade runbooken till Azure Automation måste du ändra den här aktiviteten med den information som anges i aktiviteten.

## <a name="related-articles"></a>Relaterade artiklar

* [System Center 2012 - Orchestrator](https://technet.microsoft.com/library/hh237242.aspx)
* [Service Management Automation](https://technet.microsoft.com/library/dn469260.aspx)
* [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)
* [Orchestrator Standard Aktiviteter](https://technet.microsoft.com/library/hh403832.aspx)
* [Ladda ner System Center Orchestrator Migration Toolkit](https://www.microsoft.com/en-us/download/details.aspx?id=47323)

