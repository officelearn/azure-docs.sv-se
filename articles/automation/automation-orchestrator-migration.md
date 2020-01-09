---
title: Migrera från Orchestrator till Azure Automation
description: Beskriver hur du migrerar Runbooks och integrerings paket från System Center Orchestrator till Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 528b961ca07ec86ad502ee1b589772e354564a3d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75421685"
---
# <a name="migrating-from-orchestrator-to-azure-automation-beta"></a>Migrera från Orchestrator till Azure Automation (beta)
Runbooks i [System Center Orchestrator](https://technet.microsoft.com/library/hh237242.aspx) baseras på aktiviteter från integrations paket som är skrivna specifikt för Orchestrator medan runbooks i Azure Automation baseras på Windows PowerShell.  [Grafiska runbooks](automation-runbook-types.md#graphical-runbooks) i Azure Automation har ett liknande utseende för Orchestrator-Runbooks med sina aktiviteter som representerar PowerShell-cmdletar, underordnade Runbooks och till gångar.

[System Center Orchestrator Migration Toolkit](https://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) innehåller verktyg som hjälper dig att konvertera Runbooks från Orchestrator till Azure Automation.  Förutom att konvertera Runbooks själva måste du konvertera integrerings paketen till de aktiviteter som Runbooks använder för att integrera moduler med Windows PowerShell-cmdlets.  

Följande är den grundläggande processen för att konvertera Orchestrator-Runbooks till Azure Automation.  Vart och ett av dessa steg beskrivs i detalj i avsnittet nedan.

1. Hämta [System Center Orchestrator Migration Toolkit](https://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) som innehåller de verktyg och moduler som beskrivs i den här artikeln.
2. Importera [modulen standard aktiviteter](#standard-activities-module) till Azure Automation.  Detta inkluderar konverterade versioner av vanliga Orchestrator-aktiviteter som kan användas av konverterade Runbooks.
3. Importera [System Center Orchestrator integration-moduler](#system-center-orchestrator-integration-modules) till Azure Automation för de integrerings paket som används av dina runbooks som ansluter till System Center.
4. Konvertera anpassade och tredje parts integrerings paket med hjälp av [integrerings paket konverteraren](#integration-pack-converter) och importera till Azure Automation.
5. Konvertera Orchestrator-Runbooks med [Runbook-konverteraren](#runbook-converter) och installera i Azure Automation.
6. Skapa nödvändiga Orchestrator-tillgångar manuellt i Azure Automation eftersom Runbook-konverteraren inte konverterar dessa resurser.
7. Konfigurera en [hybrid Runbook Worker](#hybrid-runbook-worker) i ditt lokala data Center för att köra konverterade Runbooks som kommer åt lokala resurser.

## <a name="service-management-automation"></a>Service Management Automation
[Service Management Automation](https://technet.microsoft.com/library/dn469260.aspx) (SMA) lagrar och Kör Runbooks i ditt lokala data Center som Orchestrator, och använder samma integrerings moduler som Azure Automation. [Runbook-konverteraren](#runbook-converter) konverterar Orchestrator-Runbooks till grafiska runbooks, men stöds inte i sma.  Du kan fortfarande installera [modulen standard aktiviteter](#standard-activities-module) och [System Center Orchestrator-integrering](#system-center-orchestrator-integration-modules) i SMA, men du måste skriva om [dina runbooks](https://technet.microsoft.com/library/dn469262.aspx)manuellt.

## <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker
Runbooks i Orchestrator lagras på en databas server och körs på Runbook-servrar, både i det lokala data centret.  Runbooks i Azure Automation lagras i Azure-molnet och kan köras i ditt lokala data Center med hjälp av en [hybrid Runbook Worker](automation-hybrid-runbook-worker.md).  Så här kommer du vanligt vis att köra Runbooks som konverterats från Orchestrator eftersom de är utformade för att köras på lokala servrar.

## <a name="integration-pack-converter"></a>Konverterare för integrerings paket
Konverterings paket konverteraren konverterar integrerings paket som har skapats med hjälp av [Orchestrator Integration Toolkit (OIT)](https://technet.microsoft.com/library/hh855853.aspx) till integrations moduler baserade på Windows PowerShell som kan importeras till Azure Automation eller Service Management Automation.  

När du kör integrerings paketets konverterare visas en guide som gör att du kan välja en integrations paket fil (. OIP).  I guiden visas sedan de aktiviteter som ingår i integrerings paketet och du kan välja vilka som ska migreras.  När du slutför guiden skapas en integrerings modul som innehåller en motsvarande cmdlet för var och en av aktiviteterna i det ursprungliga integrerings paketet.

### <a name="parameters"></a>Parametrar
Alla egenskaper för en aktivitet i integrerings paketet konverteras till parametrar för motsvarande cmdlet i integrerings modulen.  Windows PowerShell-cmdletar har en uppsättning [gemensamma parametrar](https://technet.microsoft.com/library/hh847884.aspx) som kan användas med alla cmdletar.  Till exempel ger parametern-verbose en cmdlet för att mata ut detaljerad information om dess funktion.  Ingen cmdlet kan ha en parameter med samma namn som en gemensam parameter.  Om en aktivitet har en egenskap med samma namn som en gemensam parameter uppmanas du att ange ett annat namn för parametern i guiden.

### <a name="monitor-activities"></a>Övervaka aktiviteter
Övervaka Runbooks i Orchestrator startar med en [Övervaka aktivitet](https://technet.microsoft.com/library/hh403827.aspx) och körs kontinuerligt och väntar på att anropas av en viss händelse.  Azure Automation stöder inte övervaknings-Runbooks, så alla övervaknings aktiviteter i integrerings paketet kommer inte att konverteras.  I stället skapas en placeholder-cmdlet i integrations-modulen för aktiviteten övervaka.  Denna cmdlet har inga funktioner, men den tillåter alla konverterade Runbook-funktioner som använder den för att installeras.  Denna Runbook kommer inte att kunna köras i Azure Automation, men den kan installeras så att du kan ändra den.

### <a name="integration-packs-that-cannot-be-converted"></a>Integrerings paket som inte kan konverteras
Integrerings paket som inte har skapats med OIT kan inte konverteras med integrations paket konverteraren. Det finns även vissa integrerings paket från Microsoft som för närvarande inte kan konverteras med det här verktyget.  Konverterade versioner av dessa integrerings paket har [angetts för nedladdning](#system-center-orchestrator-integration-modules) så att de kan installeras i Azure Automation eller Service Management Automation.

## <a name="standard-activities-module"></a>Modul för standard aktiviteter
Orchestrator innehåller en uppsättning [standard aktiviteter](https://technet.microsoft.com/library/hh403832.aspx) som inte ingår i ett integrations paket men som används av många Runbooks.  Modulen standard aktiviteter är en integrerings modul som innehåller en motsvarande cmdlet för var och en av dessa aktiviteter.  Du måste installera den här integrerings modulen i Azure Automation innan du importerar några konverterade Runbooks som använder en standard aktivitet.

Förutom stöd för konverterade Runbooks kan cmdletarna i modulen standard aktiviteter användas av någon bekant med Orchestrator för att bygga nya Runbooks i Azure Automation.  Även om funktionerna för alla standard aktiviteter kan utföras med cmdletar kan de fungera annorlunda.  Cmdletarna i den konverterade modulen standard aktiviteter fungerar på samma sätt som motsvarande aktiviteter och använder samma parametrar.  Detta kan hjälpa den befintliga Orchestrator Runbook-författaren i sin över gång att Azure Automation runbooks.

## <a name="system-center-orchestrator-integration-modules"></a>Moduler för System Center Orchestrator-integrering
Microsoft tillhandahåller [integrations paket](https://technet.microsoft.com/library/hh295851.aspx) för att skapa Runbooks för att automatisera System Center-komponenter och andra produkter.  Vissa av integrerings paketen är för närvarande baserade på OIT, men kan för närvarande inte konverteras till integrations moduler på grund av kända problem.  [System Center Orchestrator integration-moduler](https://www.microsoft.com/download/details.aspx?id=49555) innehåller konverterade versioner av dessa integrerings paket som kan importeras till Azure Automation och Service Management Automation.  

I RTM-versionen av det här verktyget publiceras uppdaterade versioner av integrerings paketen baserat på OIT som kan konverteras med konverteraren för integrerings paketet.  Vägledning ges även för att hjälpa dig att konvertera Runbooks med hjälp av aktiviteter från integrations paketen som inte baseras på OIT.

## <a name="runbook-converter"></a>Runbook-konverterare
Runbook-konverteraren konverterar Orchestrator-Runbooks till [grafiska runbooks](automation-runbook-types.md#graphical-runbooks) som kan importeras till Azure Automation.  

Runbook-konverteraren implementeras som en PowerShell-modul med en cmdlet som heter **ConvertFrom-SCORunbook** som utför konverteringen.  När du installerar verktyget kommer det att skapa en genväg till en PowerShell-session som läser in cmdleten.   

Följande är den grundläggande processen för att konvertera en Orchestrator-Runbook och importera den till Azure Automation.  I följande avsnitt finns mer information om hur du använder verktyget och hur du arbetar med konverterade Runbooks.

1. Exportera en eller flera Runbooks från Orchestrator.
2. Hämta integrerings moduler för alla aktiviteter i runbooken.
3. Konvertera Orchestrator-Runbooks i den exporterade filen.
4. Granska informationen i loggarna för att validera konverteringen och för att fastställa eventuella manuella uppgifter som krävs.
5. Importera konverterade Runbooks till Azure Automation.
6. Skapa alla nödvändiga till gångar i Azure Automation.
7. Redigera runbooken i Azure Automation för att ändra eventuella obligatoriska aktiviteter.

### <a name="using-runbook-converter"></a>Använda Runbook-konverterare
Syntaxen för **ConvertFrom-SCORunbook** är följande:

```powershell
ConvertFrom-SCORunbook -RunbookPath <string> -Module <string[]> -OutputFolder <string>
```

* RunbookPath – sökvägen till export filen som innehåller de Runbooks som ska konverteras.
* Modul-kommaavgränsad lista över integrerings moduler som innehåller aktiviteter i Runbooks.
* OutputFolder – sökvägen till mappen för att skapa konverterade grafiska runbooks.

I följande exempel kommando konverterar Runbooks i en export fil med namnet **MyRunbooks. ois_export**.  Dessa Runbooks använder Active Directory och Data Protection Manager integrerings paket.

```powershell
ConvertFrom-SCORunbook -RunbookPath "c:\runbooks\MyRunbooks.ois_export" -Module c:\ip\SystemCenter_IntegrationModule_ActiveDirectory.zip,c:\ip\SystemCenter_IntegrationModule_DPM.zip -OutputFolder "c:\runbooks"
```

### <a name="log-files"></a>Loggfiler
Runbook-konverteraren kommer att skapa följande loggfiler på samma plats som den konverterade runbooken.  Om filerna redan finns kommer de att skrivas över med information från den senaste konverteringen.

| Fil | Innehåll |
|:--- |:--- |
| Runbook-omvandlare-Progress. log |Detaljerade steg i konverteringen inklusive information för varje aktivitet som har konverterats och varning för varje aktivitet som inte konverteras. |
| Runbook-konverterare – Sammanfattning. logg |Sammanfattning av den senaste konverteringen inklusive eventuella varningar och Uppföljnings aktiviteter som du behöver utföra, till exempel skapa en variabel som krävs för den konverterade runbooken. |

### <a name="exporting-runbooks-from-orchestrator"></a>Exportera Runbooks från Orchestrator
Runbook-konverteraren fungerar med en export fil från Orchestrator som innehåller en eller flera Runbooks.  En motsvarande Azure Automation Runbook skapas för varje Orchestrator Runbook i export filen.  

Om du vill exportera en Runbook från Orchestrator högerklickar du på namnet på runbooken i Runbook-designer och väljer **Exportera**.  Om du vill exportera alla Runbooks i en mapp högerklickar du på mappens namn och väljer **Exportera**.

### <a name="runbook-activities"></a>Runbook-aktiviteter
Runbook-konverteraren konverterar varje aktivitet i Orchestrator-runbooken till en motsvarande aktivitet i Azure Automation.  För de aktiviteter som inte kan konverteras skapas en plats hållare-aktivitet i Runbook med varnings text.  När du har importerat den konverterade runbooken till Azure Automation måste du ersätta alla dessa aktiviteter med giltiga aktiviteter som utför de nödvändiga funktionerna.

Alla Orchestrator-aktiviteter i [modulen standard aktiviteter](#standard-activities-module) kommer att konverteras.  Det finns vissa vanliga Orchestrator-aktiviteter som inte finns i den här modulen, men som inte konverteras.  Till exempel har **sändnings plattforms händelsen** ingen Azure Automation motsvarande eftersom händelsen är speciell för Orchestrator.

[Övervaknings aktiviteter](https://technet.microsoft.com/library/hh403827.aspx) konverteras inte eftersom det inte finns någon motsvarighet till dem i Azure Automation.  Undantaget är övervaka aktiviteter i [konverterade integrerings paket](#integration-pack-converter) som ska konverteras till plats hållar aktiviteten.

All aktivitet från ett [konverterat integrerings paket](#integration-pack-converter) kommer att konverteras om du anger sökvägen till integrerings modulen med parametern **modules** .  För System Center-integrerings paket kan du använda [System Center Orchestrator-integreringens moduler](#system-center-orchestrator-integration-modules).

### <a name="orchestrator-resources"></a>Orchestrator-resurser
Runbook-konverteraren konverterar bara Runbooks, inte andra Orchestrator-resurser som räknare, variabler eller anslutningar.  Räknare stöds inte i Azure Automation.  Variabler och anslutningar stöds, men du måste skapa dem manuellt.  Loggfilerna meddelar dig om runbooken kräver sådana resurser och anger motsvarande resurser som du måste skapa i Azure Automation för att den konverterade runbooken ska fungera korrekt.

En Runbook kan till exempel använda en variabel för att fylla i ett visst värde i en aktivitet.  Den konverterade runbooken kommer att konvertera aktiviteten och ange en variabel till gång i Azure Automation med samma namn som Orchestrator-variabeln.  Detta kommer att anges i **Runbook-konverteraren-Summary. log-** filen som skapas efter konverteringen.  Du måste skapa den här variabeln till gången manuellt i Azure Automation innan du använder runbooken.

### <a name="input-parameters"></a>Indataparametrar
Runbooks i Orchestrator accepterar indataparametrar med aktiviteten **Initiera data** .  Om den Runbook som konverteras innehåller den här aktiviteten, skapas en [indataparameter](automation-graphical-authoring-intro.md#runbook-input-and-output) i Azure Automation Runbook för varje parameter i aktiviteten.  En [kontroll aktivitet för arbets flödes skript](automation-graphical-authoring-intro.md#activities) skapas i den konverterade Runbook som hämtar och returnerar varje parameter.  Aktiviteter i en Runbook som använder en indataparameter refererar till utdata från den här aktiviteten.

Skälet till att den här strategin används är att bäst spegla funktionerna i Orchestrator-runbooken.  Aktiviteter i nya grafiska runbooks ska referera direkt till indataparametrarna med hjälp av en data källa för indata för Runbook.

### <a name="invoke-runbook-activity"></a>Anropa Runbook-aktivitet
Runbooks i Orchestrator starta andra Runbooks med aktiviteten **Anropa Runbook** . Om den Runbook som konverteras innehåller den här aktiviteten och alternativet **vänta på slut för ande** är inställt, skapas en Runbook-aktivitet för den i den konverterade runbooken.  Om alternativet **vänta på slut för ande** är inte angivet skapas en arbets flödes skript aktivitet som använder **Start-AzureAutomationRunbook** för att starta runbooken.  När du har importerat den konverterade runbooken till Azure Automation måste du ändra den här aktiviteten med den information som anges i aktiviteten.

## <a name="related-articles"></a>Relaterade artiklar
* [System Center 2012 – Orchestrator](https://technet.microsoft.com/library/hh237242.aspx)
* [Service Management Automation](https://technet.microsoft.com/library/dn469260.aspx)
* [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)
* [Orchestrator standard-aktiviteter](https://technet.microsoft.com/library/hh403832.aspx)
* [Hämta System Center Orchestrator Migration Toolkit](https://www.microsoft.com/en-us/download/details.aspx?id=47323)

