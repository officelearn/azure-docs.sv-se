---
title: Migrera från Orchestrator till Azure Automation
description: Beskriver hur du migrerar runbooks och integration packs från System Center Orchestrator till Azure Automation.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 80b0523f8442e30e6af329263be454fa545933d6
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2018
ms.locfileid: "52275290"
---
# <a name="migrating-from-orchestrator-to-azure-automation-beta"></a>Migrera från Orchestrator till Azure Automation (Beta)
Runbooks i [System Center Orchestrator](https://technet.microsoft.com/library/hh237242.aspx) baseras på aktiviteter från integrationspaket som är skrivna specifikt för Orchestrator medan runbooks i Azure Automation baseras på Windows PowerShell.  [Grafiska runbooks](automation-runbook-types.md#graphical-runbooks) i Azure Automation har en liknande utseende till Orchestrator-runbooks med deras aktiviteter som representerar PowerShell-cmdlet: ar, underordnade runbooks och tillgångar.

Den [System Center Orchestrator Migration Toolkit](https://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) innehåller verktyg som hjälper dig att konvertera runbooks från Orchestrator till Azure Automation.  Förutom att konvertera runbooks själva, måste du omvandla integreringspaket med aktiviteter som använder runbooks till integreringsmoduler med Windows PowerShell-cmdletar.  

Följande är den grundläggande processen för att konvertera Orchestrator-runbooks i Azure Automation.  Var och en av de här stegen beskrivs i detalj i avsnitten nedan.

1. Ladda ned den [System Center Orchestrator Migration Toolkit](https://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) som innehåller de verktyg och moduler som beskrivs i den här artikeln.
2. Importera [standardaktiviteter modulen](#standard-activities-module) till Azure Automation.  Detta inkluderar konverterade versioner av Orchestrator-standardaktiviteter som kan användas av konverterade runbooks.
3. Importera [integreringsmoduler för System Center Orchestrator](#system-center-orchestrator-integration-modules) till Azure Automation för dessa integrationspaket som används av dina runbooks som har åtkomst till System Center.
4. Konvertera anpassade och integreringspaket för tredje part med hjälp av den [Integration Pack Converter](#integration-pack-converter) och importera till Azure Automation.
5. Konvertera Orchestrator runbooks med den [Runbook Converter](#runbook-converter) och installera i Azure Automation.
6. Skapa Orchestrator-tillgångar som krävs i Azure Automation manuellt eftersom Runbook-Converter inte konverterar dessa resurser.
7. Konfigurera en [Hybrid Runbook Worker](#hybrid-runbook-worker) i ditt lokala Datacenter för att köra konverterade runbooks som har åtkomst till lokala resurser.

## <a name="service-management-automation"></a>Service Management Automation
[Service Management Automation](https://technet.microsoft.com/library/dn469260.aspx) (SMA) lagrar och kör runbooks i ditt lokala datacenter som Orchestrator och används samma integreringsmoduler som Azure Automation. Den [Runbook Converter](#runbook-converter) konverterar Orchestrator-runbooks till grafiska runbooks men som inte stöds i SMA.  Du kan fortfarande installera den [Standard aktiviteter modulen](#standard-activities-module) och [integreringsmoduler för System Center Orchestrator](#system-center-orchestrator-integration-modules) till SMA, men du måste manuellt [skriva om dina runbooks](https://technet.microsoft.com/library/dn469262.aspx).

## <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker
Runbooks i Orchestrator lagras på en databasserver och körs på runbook-servrar, både i ditt lokala datacenter.  Runbooks i Azure Automation lagras i Azure-molnet och kan köras i ditt lokala datacenter med hjälp av en [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md).  Detta är hur du vanligtvis köra runbooks som konverterats från Orchestrator eftersom de är avsedda att köras på lokala servrar.

## <a name="integration-pack-converter"></a>Integration Pack konverterare
Integration Pack Converter konverterar integreringspaket som har skapats med den [Orchestrator Integration Toolkit (OIT)](https://technet.microsoft.com/library/hh855853.aspx) till integreringsmoduler baserat på Windows PowerShell som kan importeras till Azure Automation eller Service Management Automation.  

När du kör Integration Pack Converter, visas en guide som gör att du kan välja en integrationspaketfilen (.oip).  Guiden visar en lista över aktiviteter som ingår i paketet sedan och kan du välja vilket kommer att migreras.  När du har slutfört guiden skapas en modul för integrering med en motsvarande cmdlet för var och en av aktiviteterna i det ursprungliga integreringspaketet.

### <a name="parameters"></a>Parametrar
Alla egenskaper för en aktivitet i integreringspaketet konverteras till parametrarna för motsvarande cmdlet i modulen integration.  Windows PowerShell-cmdlets har en uppsättning [gemensamma parametrar](https://technet.microsoft.com/library/hh847884.aspx) som kan användas med alla cmdletar.  Till exempel - Verbose parametern orsakar en cmdlet för att mata ut detaljerad information om åtgärden.  Inga cmdlet kan ha en parameter med samma namn som en parameter som vanligt.  Om en aktivitet har en egenskap med samma namn som en gemensam parameter, uppmanas du att ange ett annat namn för parametern.

### <a name="monitor-activities"></a>Övervakaraktiviteter
Övervaka runbooks i Orchestrator börjar med en [övervaka](https://technet.microsoft.com/library/hh403827.aspx) och kör kontinuerligt väntar på att anropas av en viss händelse.  Azure Automation stöder inte övervaka runbooks, så inte kommer att konvertera alla övervakaraktiviteter i integreringspaketet.  I stället skapas en platshållare cmdlet i modulen integration för övervakaraktiviteten.  Denna cmdlet har inga funktioner, men det gör att alla konverterade runbook som används för att installeras.  Denna runbook inte kan köras i Azure Automation, men den kan installeras så att du kan ändra den.

### <a name="integration-packs-that-cannot-be-converted"></a>Integreringspaket som inte kan konverteras
Integreringspaket som inte har skapats med OIT kan inte konverteras med Integration Pack Converter. Det finns även vissa integreringspaket som tillhandahålls av Microsoft som för närvarande inte kan konverteras till det här verktyget.  Konverterade versioner av integreringspaketen har [som finns att hämta](#system-center-orchestrator-integration-modules) så att de kan installeras i Azure Automation och Service Management Automation.

## <a name="standard-activities-module"></a>Standardaktiviteter modul
Orchestrator innehåller en uppsättning [standardaktiviteter](https://technet.microsoft.com/library/hh403832.aspx) som inte ingår i ett integreringspaket men används av många runbooks.  Standardaktiviteter-modul är en modul för integrering med en motsvarande cmdlet för var och en av dessa aktiviteter.  Innan du importerar konverterade runbooks som använder en standard aktiviteten måste du installera den här modulen integrering i Azure Automation.

Förutom att stöda konverterade runbooks, användas cmdlets i modulen standardaktiviteter av någon som är bekant med Orchestrator för att skapa nya runbooks i Azure Automation.  Även om funktionen för alla aktiviteter som standard kan utföras med cmdlet: ar, fungerar de på olika sätt.  Cmdlets i modulen konverterade standardaktiviteter kommer fungerar på samma sätt som deras motsvarande aktiviteter och använda samma parametrar.  Detta kan hjälpa befintliga Orchestrator runbook-redigeraren i sina övergången till Azure Automation-runbooks.

## <a name="system-center-orchestrator-integration-modules"></a>System Center Orchestrator-integreringsmoduler
Microsoft tillhandahåller [integreringspaket](https://technet.microsoft.com/library/hh295851.aspx) för att skapa runbooks för att automatisera System Center-komponenter och andra produkter.  Vissa av dessa integrationspaket för närvarande är baserade på OIT men för närvarande kan inte konverteras till integreringsmoduler på grund av kända problem.  [System Center Orchestrator-integreringsmoduler](https://www.microsoft.com/download/details.aspx?id=49555) innehåller konverterade versioner av integreringspaketen som kan importeras till Azure Automation och Service Management Automation.  

Uppdaterade versioner av integreringspaket baserat på OIT som kan konverteras med Integration Pack Converter RTM-versionen av det här verktyget kommer att publiceras.  Vägledning ges också för att hjälpa dig att konvertera runbooks med aktiviteter från integrationspaket som inte baseras på OIT.

## <a name="runbook-converter"></a>Runbook-konverterare
Runbook-Converter konverterar Orchestrator runbooks till [grafiska runbooks](automation-runbook-types.md#graphical-runbooks) som kan importeras till Azure Automation.  

Runbook Converter implementeras som en PowerShell-modul med en cmdlet som kallas **ConvertFrom SCORunbook** som kan utföra konverteringen.  När du installerar verktyget skapas en genväg till en PowerShell-session som läser in cmdlet: en.   

Följande är den grundläggande processen att konvertera en Orchestrator-runbook och importera den till Azure Automation.  Följande avsnitt innehåller ytterligare information om hur du använder verktyget och arbeta med konverterade runbooks.

1. Exportera en eller flera runbooks från Orchestrator.
2. Hämta integreringsmoduler för alla aktiviteter i runbook.
3. Konvertera Orchestrator-runbooks i den exporterade filen.
4. Granska information i loggarna för att verifiera konverteringen och fastställa alla manuella uppgifter som krävs.
5. Importera konverterade runbooks till Azure Automation.
6. Skapa alla nödvändiga resurser i Azure Automation.
7. Redigera runbook i Azure Automation för att ändra alla aktiviteter som krävs.

### <a name="using-runbook-converter"></a>Med hjälp av Runbook-konverterare
Syntaxen för **ConvertFrom SCORunbook** är följande:

```powershell
ConvertFrom-SCORunbook -RunbookPath <string> -Module <string[]> -OutputFolder <string>
```

* RunbookPath - sökvägen till filen med exporterade som innehåller runbooks för att konvertera.
* Modulen – kommaavgränsad lista över integreringsmoduler som innehåller aktiviteter i runbooks.
* OutputFolder - sökvägen till mappen för att skapa konverteras grafiska runbooks.

Följande exempelkommando konverterar runbooks i en exportfil kallas **MyRunbooks.ois_export**.  Dessa runbooks använder Active Directory och Data Protection Manager-integreringspaket.

```powershell
ConvertFrom-SCORunbook -RunbookPath "c:\runbooks\MyRunbooks.ois_export" -Module c:\ip\SystemCenter_IntegrationModule_ActiveDirectory.zip,c:\ip\SystemCenter_IntegrationModule_DPM.zip -OutputFolder "c:\runbooks"
```

### <a name="log-files"></a>Loggfiler
Runbook-Converter skapas följande loggfiler på samma plats som den konverterade runbooken.  Om filerna redan finns, sedan de kommer att skrivas över med information från senaste konverteringen.

| Fil | Innehåll |
|:--- |:--- |
| Runbook Converter - Progress.log |Detaljerade anvisningar för konverteringen, inklusive information för varje aktivitet som omvandlats och varning för varje aktivitet som inte konverteras. |
| Runbook Converter - Summary.log |Sammanfattning av senaste konverteringen, inklusive alla varningar och följ upp uppgifter som du behöver utföra, till exempel skapa en variabel som krävs för den konvertera runbooken. |

### <a name="exporting-runbooks-from-orchestrator"></a>Exportera runbooks från Orchestrator
Runbook-Converter fungerar med en exportfil från Orchestrator som innehåller en eller flera runbooks.  En motsvarande Azure Automation-runbook för varje Orchestrator runbook skapas i exportfilen.  

Om du vill exportera en runbook från Orchestrator, högerklicka på namnet på runbook i Runbook Designer och välj **exportera**.  Om du vill exportera alla runbooks i en mapp, högerklickar du på namnet på mappen och välj **exportera**.

### <a name="runbook-activities"></a>Runbook-aktiviteter
Runbook-Converter konverterar varje aktivitet i Orchestrator-runbook till en motsvarande aktivitet i Azure Automation.  För dessa aktiviteter som inte kan konverteras, skapas en platshållare aktivitet i runbooken med varningstexten.  När du har importerat den konverterade runbooken i Azure Automation måste du ersätta någon av dessa aktiviteter med giltig aktiviteter som utför de nödvändiga funktionerna.

Alla Orchestrator-aktiviteter i den [Standard aktiviteter modulen](#standard-activities-module) kommer att konverteras.  Det finns vissa Orchestrator-standardaktiviteter som inte är i den här modulen men och inte konverteras.  Till exempel **skicka Plattformshändelse** har ingen motsvarighet för Azure Automation eftersom händelsen är specifikt för Orchestrator.

[Övervaka aktiviteter](https://technet.microsoft.com/library/hh403827.aspx) konverteras inte eftersom det finns ingen motsvarighet till dem i Azure Automation.  Undantaget är övervaka aktiviteter i [konverteras integreringspaket](#integration-pack-converter) som kommer att konverteras till aktiviteten platshållare.

Alla aktiviteter från en [konverteras integreringspaketet](#integration-pack-converter) kommer att konverteras om du anger sökvägen till modul för integrering med den **moduler** parametern.  För System Center-integreringspaket, kan du använda den [integreringsmoduler för System Center Orchestrator](#system-center-orchestrator-integration-modules).

### <a name="orchestrator-resources"></a>Informationskällor för orchestrator
Runbook-Converter konverterar endast runbooks, inte andra informationskällor för Orchestrator som räknare, variabler eller anslutningar.  Räknare stöds inte i Azure Automation.  Variabler och anslutningar stöds, men du måste skapa dem manuellt.  Loggfilerna kommer informerar dig om runbook kräver dessa resurser och ange motsvarande resurser som du behöver skapa i Azure Automation för konverterade runbook ska fungera korrekt.

En runbook kan till exempel använda en variabel för att fylla i ett visst värde i en aktivitet.  Den konverterade runbooken konverterar aktiviteten och ange en variabel tillgång i Azure Automation med samma namn som Orchestrator-variabeln.  Detta anges i den **Runbook Converter - Summary.log** exportfil som har skapats efter konverteringen.  Du måste skapa den här variabeltillgång manuellt i Azure Automation innan du använder runbook.

### <a name="input-parameters"></a>Indataparametrar
Runbooks i Orchestrator godkänna indataparametrar med den **initiera Data** aktivitet.  Om runbook omvandlas innehåller den här aktiviteten och sedan en [Indataparametern](automation-graphical-authoring-intro.md#runbook-input-and-output) i Azure Automation runbook har skapats för varje parameter i aktiviteten.  En [Arbetsflödesskript kontroll](automation-graphical-authoring-intro.md#activities) aktivitet skapas i den konvertera runbook som hämtar och returnerar varje parameter.  Aktiviteter i runbook som använder en indataparameter finns utdata från den här aktiviteten.

Anledningen till att den här strategin används är för bäst spegling av funktioner i Orchestrator-runbook.  Aktiviteter i nya grafiska runbooks vända direkt om du vill med hjälp av en Runbook-källa för indata-indataparametrar.

### <a name="invoke-runbook-activity"></a>Aktiviteten anropa Runbook
Runbooks i Orchestrator starta andra runbooks med den **anropa Runbook** aktivitet. Om runbook omvandlas innehåller den här aktiviteten och **vänta på slutförande** alternativet har ställts in så skapas en runbook-aktivitet för den konverterade runbook.  Om den **vänta på slutförande** alternativet inte anges så skapas en Arbetsflödesskript aktivitet som använder **Start AzureAutomationRunbook** att starta runbooken.  När du har importerat den konverterade runbooken i Azure Automation måste du ändra den här aktiviteten med den information som anges i aktiviteten.

## <a name="related-articles"></a>Relaterade artiklar
* [System Center 2012 – Orchestrator](https://technet.microsoft.com/library/hh237242.aspx)
* [Service Management Automation](https://technet.microsoft.com/library/dn469260.aspx)
* [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)
* [Orchestrator-standardaktiviteter](https://technet.microsoft.com/library/hh403832.aspx)
* [Ladda ned System Center Orchestrator Migration Toolkit](https://www.microsoft.com/en-us/download/details.aspx?id=47323)
