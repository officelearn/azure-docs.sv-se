---
title: "Felsöka Azure Diagnostics | Microsoft Docs"
description: "Felsökning av problem när du använder Azure-diagnostik i Azure Virtual Machines, Service Fabric och Cloud Services."
services: monitoring-and-diagnostics
documentationcenter: .net
author: rboucher
manager: carmonm
editor: 
ms.assetid: 66469bce-d457-4d1e-b550-a08d2be4d28c
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/12/2017
ms.author: robb
ms.openlocfilehash: b03265b52886b30e4b9de0b0293e5dadd6d2413a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-diagnostics-troubleshooting"></a>Azure Diagnostics felsökning
Den här artikeln beskriver felsökningsinformation som är relevant för med hjälp av Azure-diagnostik. Läs mer om Azure diagnostics [översikt över Azure-diagnostik](azure-diagnostics.md).

## <a name="logical-components"></a>Logiska komponenter
**Starta diagnostik-plugin-programmet (DiagnosticsPluginLauncher.exe)**: startar tillägget Azure-diagnostik. Agerar som posten rapporttjänstplatsen.

**Diagnostik-plugin-programmet (DiagnosticsPlugin.exe)**: konfigurerar, startar och hanterar livslängden för övervakningsagenten. Det är hur startas av starta.

**Monitoring Agent (MonAgent\*.exe processer)**: Övervakare, samlar in och överför diagnostikdata.  

## <a name="logartifact-paths"></a>Log-artefakt sökvägar
Följande är sökvägar till vissa viktiga loggar och artefakter. Vi refererar till den här informationen i resten av dokumentet.

### <a name="azure-cloud-services"></a>Azure Cloud Services
| Artefakt | Sökväg |
| --- | --- |
| **Azure Diagnostics-konfigurationsfil** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version > \Config.txt |
| **Loggfiler** | C:\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version > \ |
| **Lokal lagring för diagnostikdata** | C:\Resources\Directory\<CloudServiceDeploymentID >.\< RoleName >. DiagnosticStore\WAD0107\Tables |
| **Monitoring agent-konfigurationsfil** | C:\Resources\Directory\<CloudServiceDeploymentID >.\< RoleName >. DiagnosticStore\WAD0107\Configuration\MaConfig.xml |
| **Azure Diagnostics extension-paketet** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version > |
| **Verktyget loggsökvägen-samling** | %SystemDrive%\Packages\GuestAgent\ |
| **MonAgentHost loggfil** | C:\Resources\Directory\<CloudServiceDeploymentID >.\< RoleName >. DiagnosticStore\WAD0107\Configuration\MonAgentHost. < seq_num > .log |

### <a name="virtual-machines"></a>Virtuella datorer
| Artefakt | Sökväg |
| --- | --- |
| **Azure Diagnostics-konfigurationsfil** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<version > \RuntimeSettings |
| **Loggfiler** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<version > \Logs\ |
| **Lokal lagring för diagnostikdata** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion > \WAD0107\Tables |
| **Monitoring agent-konfigurationsfil** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion > \WAD0107\Configuration\MaConfig.xml |
| **Statusfil** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<version > \Status |
| **Azure Diagnostics extension-paketet** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion >|
| **Verktyget loggsökvägen-samling** | C:\WindowsAzure\Packages |
| **MonAgentHost loggfil** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion > \WAD0107\Configuration\MonAgentHost. < seq_num > .log |

## <a name="metric-data-doesnt-appear-in-the-azure-portal"></a>Mått data visas inte i Azure-portalen
Azure Diagnostics tillhandahåller mått data som kan visas i Azure-portalen. Om du har problem med se data i portalen kontrollerar WADMetrics\* tabell i Azure-diagnostik storage-konto att se om motsvarande mått poster förekommer. 

Här är den **PartitionKey** i tabellen är skaluppsättning för resurs-ID, virtuell dator eller virtuell dator. **RowKey** är måttnamnet (även kallat prestandaräknarnamnet).

Kontrollera om resurs-ID är felaktigt **diagnostik** **Configuration** > **mått** > **ResourceId**resurs-ID har angetts korrekt.

Kontrollera om det finns inga data för den specifika måtten, **diagnostik Configuration** > **PerformanceCounter** att se om måttet (prestandaräknaren) ingår. Vi aktivera följande räknare som standard:
- \Processor(_Total)\% processortid
- \Memory\Tillgängliga byte
- \ASP.NET program (__totalt__) \Requests/Sec
- \ASP.NET program (__totalt__) \Errors totalt/sek
- \ASP.NET\Requests i kö
- \ASP.NET\Requests avvisade
- \Processor(W3wp)\% processortid
- \Process (w3wp) \Private byte
- \Process(WaIISHost)\% processortid
- \Process (WaIISHost) \Private byte
- \Process(WaWorkerHost)\% processortid
- \Process (WaWorkerHost) \Private byte
- \Memory\Page fel per sekund
- \.NET CLR-minne (_globala_)\% tid i GC i
- \LogicalDisk (C:) \Disk skrivna byte/s
- \LogicalDisk (C:) \Disk-lästa byte/s
- \LogicalDisk (D:) \Disk skrivna byte/s
- \LogicalDisk (D:) \Disk-lästa byte/s

Om konfigurationen är korrekt, men du fortfarande inte kan se måttinformationen, Använd följande riktlinjer för felsökning.


## <a name="azure-diagnostics-isnt-starting"></a>Azure-diagnostik är inte startar
Information om varför Azure-diagnostik kunde inte starta finns i **DiagnosticsPluginLauncher.log** och **DiagnosticsPlugin.log** filer på loggplatsen som angavs tidigare. 

Om de här loggarna visar `Monitoring Agent not reporting success after launch`, betyder det gick inte att starta MonAgentHost.exe. Titta på loggfilerna på plats som angetts för `MonAgentHost log file` i föregående avsnitt.

Den sista raden i loggfilerna innehåller slutkoden.  

```
DiagnosticsPluginLauncher.exe Information: 0 : [4/16/2016 6:24:15 AM] DiagnosticPlugin exited with code 0
```
Om du hittar en **negativa** slutkod, referera till den [avsluta kod tabell](#azure-diagnostics-plugin-exit-codes) i den [refererar till avsnittet](#references).

## <a name="diagnostics-data-is-not-logged-to-azure-storage"></a>Diagnostikdata är inte inloggad till Azure Storage
Avgör om inga data visas eller vissa data visas.

### <a name="diagnostics-infrastructure-logs"></a>Diagnostik infrastruktur loggar
Diagnostik loggar alla fel i loggarna för diagnostik-infrastruktur. Kontrollera att du har aktiverat den [insamlingen av diagnostik infrastruktur loggar i konfigurationen](#how-to-check-diagnostics-extension-configuration). Sedan kan du snabbt se alla relevanta fel som visas i den `DiagnosticInfrastructureLogsTable` tabellen i konfigurerade storage-konto.

### <a name="no-data-is-appearing"></a>Inga data visas
Den vanligaste orsaken till att händelsedata inte visas alls är att du har angett felaktigt kontoinformation för lagring.

Lösning: Korrigera diagnostik-konfiguration och installera om diagnostik.

Om lagringskontot är korrekt konfigurerade och fjärransluten åtkomst till datorn och kontrollera att DiagnosticsPlugin.exe och MonAgentCore.exe körs. Om de inte körs, följer du stegen i [startar inte Azure-diagnostik](#azure-diagnostics-is-not-starting). 

Om processerna som körs, gå till [är data som hämtats komma lokalt?](#is-data-getting-captured-locally) och följ instruktionerna där.

### <a name="part-of-the-data-is-missing"></a>En del av data som saknas
Om du får vissa data men inte alla, innebär det att data collection/överföring pipeline är korrekt. Följ underavsnitt här om du vill begränsa problemet.

#### <a name="is-the-collection-configured"></a>Har konfigurerats för samlingen 
Diagnostik-konfigurationen innehåller instruktioner för en viss typ av data ska samlas in. [Granska konfigurationen](#how-to-check-diagnostics-extension-configuration) att verifiera att du endast söker efter data som du har konfigurerat för samlingen.

#### <a name="is-the-host-generating-data"></a>Värden genererar data?
- **Prestandaräknare**: öppna perfmon och kontrollera räknaren.

- **Spårningsloggar**: fjärransluten åtkomst till den virtuella datorn och lägga till en TextWriterTraceListener appens config-fil.  Se http://msdn.microsoft.com/library/sk36c28t.aspx att ställa in text-lyssnaren.  Kontrollera att den `<trace>` element har `<trace autoflush="true">`.<br />
Om du inte ser loggar som genereras, se [mer om spårningsloggar saknas](#more-about-trace-logs-missing).

- **ETW-spårning**: fjärråtkomst till den virtuella datorn och installera förhandsgranskning.  Förhandsgranskning, köra **filen** > **användarkommando** > **lyssna etwprovder1** > **etwprovider2**och så vidare. Den **lyssna** kommandot är skiftlägeskänsligt och det får inte finnas blanksteg mellan kommaavgränsade listan över ETW-providers. Om kommandot misslyckas att köra, kan du välja den **loggen** knappen i nederkant högra verktyget förhandsgranskning för att se vad det gjordes ett försök att köra och var vilka resultatet.  Under förutsättning att indata är rätt, visas ett nytt fönster. Om några sekunder börjar du se ETW-spårning.

- **Händelseloggar**: fjärråtkomst till den virtuella datorn. Öppna `Event Viewer`, och sedan kontrollera att det finns händelser.

#### <a name="is-data-getting-captured-locally"></a>Data komma avbildas lokalt?
Kontrollera därefter data komma avbildas lokalt.
Data lagras lokalt i `*.tsf` filer i [lokal lagring för diagnostikdata](#log-artifacts-path). Olika typer av loggar hämta som samlas in i olika `.tsf` filer. Namn som liknar tabellnamn i Azure Storage. 

Till exempel `Performance Counters` hämta samlas in i `PerformanceCountersTable.tsf`. Händelseloggar hämta som samlas in i `WindowsEventLogsTable.tsf`. Följ instruktionerna i den [lokal logg extrahering](#local-log-extraction) avsnittet för att öppna filerna lokal insamling och kontrollera att du ser dem komma in på disk.

Om du inte ser loggar komma som samlats in lokalt och redan har kontrollerat att värden genererar data, har du förmodligen konfigurationsproblem. Granska konfigurationen noggrant. 

Läs igenom den konfiguration som har genererats för MonitoringAgent [MaConfig.xml](#log-artifacts-path). Kontrollera att det finns ett avsnitt som beskriver relevant logg-källa. Kontrollera att det inte går förlorad i översättningen mellan diagnostik-konfiguration och konfigurationen av övervakning.

#### <a name="is-data-getting-transferred"></a>Data komma överförs?
Om du har kontrollerat att data komma avbildas lokalt men du fortfarande inte ser det i ditt lagringskonto, gör du följande: 

- Kontrollera att du har angett rätt lagringskontot och att du inte har flyttats över nycklar för det angivna lagringskontot. Azure Cloud Services, ibland vi finns att inte uppdatera personer `useDevelopmentStorage=true`.

- Kontrollera att det angivna lagringskontot är korrekt. Kontrollera att du inte har nätverksbegränsningar som gör att komponenterna från att nå offentlig lagring slutpunkter. Ett sätt att göra det är att fjärråtkomst till datorn och försök sedan att skriva till samma lagringskonto något själv.

- Slutligen kan du se vilka fel rapporterats av en Övervakningsagent. Övervakningsagenten skriver loggar i `maeventtable.tsf`, som finns i [lokal lagring för diagnostikdata](#log-artifacts-path). Följ instruktionerna i den [lokal logg extrahering](#local-log-extraction) avsnitt för att öppna den här filen. Försök att avgöra om det finns `errors` som anger fel läsning av lokala filer till lagring.

### <a name="capturing-and-archiving-logs"></a>Samla in och arkivera loggar
Om du funderar att kontakta supporten, är det första kanske du ombeds att samla in loggar från din dator. Du kan spara tid genom att göra den själv. Kör den `CollectGuestLogs.exe` utility på [loggsökvägen samling verktyget](#log-artifacts-path). Den genererar en .zip-fil med alla relevanta Azure loggar i samma mapp.

## <a name="diagnostics-data-tables-not-found"></a>Diagnostik datatabeller hittades inte
Tabellerna i Azure storage som innehar ETW-händelser är namngivna med hjälp av följande kod:

```C#
        if (String.IsNullOrEmpty(eventDestination)) {
            if (e == "DefaultEvents")
                tableName = "WADDefault" + MD5(provider);
            else
                tableName = "WADEvent" + MD5(provider) + eventId;
        }
        else
            tableName = "WAD" + eventDestination;
```

Här är ett exempel:

```XML
        <EtwEventSourceProviderConfiguration provider="prov1">
          <Event id="1" />
          <Event id="2" eventDestination="dest1" />
          <DefaultEvents />
        </EtwEventSourceProviderConfiguration>
        <EtwEventSourceProviderConfiguration provider="prov2">
          <DefaultEvents eventDestination="dest2" />
        </EtwEventSourceProviderConfiguration>
```
```JSON
"EtwEventSourceProviderConfiguration": [
    {
        "provider": "prov1",
        "Event": [
            {
                "id": 1
            },
            {
                "id": 2,
                "eventDestination": "dest1"
            }
        ],
        "DefaultEvents": {
            "eventDestination": "DefaultEventDestination",
            "sinks": ""
        }
    },
    {
        "provider": "prov2",
        "DefaultEvents": {
            "eventDestination": "dest2"
        }
    }
]
```
Den här koden genererar fyra tabeller:

| Händelse | Tabellnamnet |
| --- | --- |
| Provider = ”prov1” &lt;händelse-id = ”1” /&gt; |WADEvent + MD5("prov1") + ”1” |
| Provider = ”prov1” &lt;händelse-id = ”2” eventDestination = ”dest1” /&gt; |WADdest1 |
| Provider = ”prov1” &lt;DefaultEvents /&gt; |WADDefault+MD5("prov1") |
| Provider = ”prov2” &lt;DefaultEvents eventDestination = ”dest2” /&gt; |WADdest2 |

## <a name="references"></a>Referenser

### <a name="how-to-check-diagnostics-extension-configuration"></a>Hur du kontrollerar konfiguration för diagnostik
Det enklaste sättet att kontrollera konfigurationen av tillägget är att gå till [resursutforskaren Azure](http://resources.azure.com), och sedan gå till den virtuella datorn eller cloud service var Azure-diagnostik-tillägget (IaaSDiagnostics / PaaDiagnostics) är.

Du kan också fjärrskrivbord till datorn och titta på Azure Diagnostics konfigurationsfilen som beskrivs i den [logga artefakter sökvägssektion](#log-artifacts-path).

I båda fallen kan du söka efter **Microsoft.Azure.Diagnostics**, och sedan för den **xmlCfg** eller **WadCfg** fältet. 

Om du söker på en virtuell dator och **WadCfg** är tillgänglig, innebär det konfigurationen i JSON-format. Om den **xmlCfg** fältet finns, innebär det konfig XML och är base64-kodad. Du behöver [avkoda den](http://www.bing.com/search?q=base64+decoder) att se den XML som har lästs in av diagnostik.

För rolltjänsten molnet om du väljer konfigurationen från disken, data är base64-kodat, så du behöver [avkoda den](http://www.bing.com/search?q=base64+decoder) att se den XML som har lästs in av diagnostik.

### <a name="azure-diagnostics-plugin-exit-codes"></a>Azure Diagnostics plugin-slutkoder
Plugin-programmet returnerar följande slutkoderna:

| Slutkod | Beskrivning |
| --- | --- |
| 0 |Lyckades. |
| -1 |Allmänt fel. |
| -2 |Det gick inte att läsa in filen rcf.<p>Den här internt fel ska bara hända om plugin-programmet för gäst Agentstart anropas manuellt felaktigt på den virtuella datorn. |
| -3 |Det går inte att läsa in konfigurationsfilen diagnostik.<p><p>Lösning: På grund av en konfigurationsfil som inte skickar schemavalidering. Lösningen är att tillhandahålla en konfigurationsfil som överensstämmer med schemat. |
| -4 |En annan instans av en övervakningsagent diagnostik används redan av den lokala resurs-katalogen.<p><p>Lösning: Ange ett annat värde för **LocalResourceDirectory**. |
| -6 |Gästen plugin-programmet Agentstart försökte starta diagnostik med en ogiltig kommandorad.<p><p>Den här internt fel ska bara hända om plugin-programmet för gäst Agentstart anropas manuellt felaktigt på den virtuella datorn. |
| -10 |Diagnostik för plugin-programmet avslutades med ett ohanterat undantag. |
| -11 |Gästagenten kunde inte skapa processen som ansvarar för att starta och övervaka övervakningsagenten.<p><p>Lösning: Kontrollera att det finns tillräckligt med systemresurser för att starta nya processer.<p> |
| -101 |Ogiltiga argument vid anrop av diagnostik plugin-programmet.<p><p>Den här internt fel ska bara hända om plugin-programmet för gäst Agentstart anropas manuellt felaktigt på den virtuella datorn. |
| -102 |Processen för plugin-programmet kan inte initieras.<p><p>Lösning: Kontrollera att det finns tillräckligt med systemresurser för att starta nya processer. |
| -103 |Processen för plugin-programmet kan inte initieras. Det är särskilt går inte att skapa objektet meddelandeloggfiler.<p><p>Lösning: Kontrollera att det finns tillräckligt med systemresurser för att starta nya processer. |
| -104 |Det gick inte att läsa in filen rcf som tillhandahålls av gästagenten.<p><p>Den här internt fel ska bara hända om plugin-programmet för gäst Agentstart anropas manuellt felaktigt på den virtuella datorn. |
| -105 |Diagnostik för plugin-programmet kan inte öppna konfigurationsfilen diagnostik.<p><p>Den här internt fel ska bara hända om diagnostik plugin-programmet anropas manuellt felaktigt på den virtuella datorn. |
| -106 |Det går inte att läsa konfigurationsfilen diagnostik.<p><p>På grund av en konfigurationsfil som inte skickar schemavalidering. <br><br>Lösning: Ange en konfigurationsfil som överensstämmer med schemat. Mer information finns i [Kontrollera konfiguration för diagnostik](#how-to-check-diagnostics-extension-configuration). |
| -107 |Resursen directory pass till övervakningsagenten är ogiltig.<p><p>Den här internt fel ska bara hända om övervakningsagenten anropas manuellt felaktigt på den virtuella datorn.</p> |
| -108 |Det gick inte att konvertera konfigurationsfilen diagnostik i konfigurationsfilen i övervakning agent.<p><p>Den här internt fel ska bara hända om diagnostik plugin-programmet anropas manuellt med en ogiltig konfigurationsfil. |
| -110 |Allmänna diagnostik konfigurationsfel.<p><p>Den här internt fel ska bara hända om diagnostik plugin-programmet anropas manuellt med en ogiltig konfigurationsfil. |
| -111 |Det går inte att starta övervakningsagenten.<p><p>Lösning: Kontrollera att det finns tillräckligt med systemresurser för. |
| -112 |Allmänt fel |

### <a name="local-log-extraction"></a>Lokal logg extrahering
Övervakningsagenten samlar in loggar och artefakter som `.tsf` filer. Den `.tsf` filen kan inte läsas men du kan konvertera den till en `.csv` på följande sätt: 

```
<Azure diagnostics extension package>\Monitor\x64\table2csv.exe <relevantLogFile>.tsf
```
En ny fil kallas `<relevantLogFile>.csv` skapas i samma sökväg som motsvarande `.tsf` fil.

>[!NOTE] 
> Du behöver bara köra det här verktyget mot den huvudsakliga .tsf-filen (till exempel PerformanceCountersTable.tsf). De tillhörande filerna (till exempel PerformanceCountersTables_\*\*001.tsf PerformanceCountersTables_\*\*002.tsf och så vidare) bearbetas automatiskt.

### <a name="more-about-missing-trace-logs"></a>Mer information om saknas spårningsloggar 

>[!NOTE]
> Följande information gäller främst Azure Cloud Services om du har konfigurerat DiagnosticsMonitorTraceListener på ett program som körs på IaaS-VM. 

- Kontrollera att den **DiagnosticMonitorTraceListener** konfigureras i web.config eller app.config.  Detta är konfigurerat som standard i molntjänstprojekt. Dock vissa kunder kommentera det ut, vilket gör att trace-satser som inte ska samlas in av diagnostik. 

- Om inte komma skrivs från den **OnStart** eller **kör** metod, kontrollera att den **DiagnosticMonitorTraceListener** i app.config.  Som standard är det i web.config, men som gäller endast för kod som körs i w3wp.exe. Du behöver det i app.config att fånga in spårningar som körs i WaIISHost.exe.

- Kontrollera att du använder **Diagnostics.Trace.TraceXXX** i stället för **Diagnostics.Debug.WriteXXX.** Felsökningsuttryck tas bort från en slutversion.

- Kontrollera att den kompilerade koden har den **Diagnostics.Trace rader** (Använd Reflector, ildasm eller ILSpy för att kontrollera). **Diagnostics.Trace** kommandon tas bort från den kompilerade binärfilen såvida du inte använder symbolen TRACE villkorlig kompilering. Det här är ett vanligt problem som uppstår när du använder msbuild för att skapa ett projekt.   

## <a name="known-issues-and-mitigations"></a>Kända problem och åtgärder
Här följer en lista över kända problem med kända åtgärder:

**1. .NET 4.5 beroende**

Windows Azure Diagnostics tillägget har beroende runtime på framework .NET 4.5 eller senare. Vid tidpunkten för skrivning, alla datorer som har etablerats för Azure Cloud Services, samt alla officiella bilder som baseras på virtuella Azure-datorer, .NET 4.5 eller senare installerat. 

Det är fortfarande möjligt inträffar en situation där du försöker köra tillägget för Windows Azure-diagnostik på en dator som inte har .NET 4.5 eller senare. Detta händer när du skapar din dator från en gammal bild eller en ögonblicksbild eller när du vill aktivera anpassade disken.

Detta visar vanligtvis som en slutkod **255** när du kör **DiagnosticsPluginLauncher.exe.** Felet inträffar på grund av följande undantag: 
```
System.IO.FileLoadException: Could not load file or assembly 'System.Threading.Tasks, Version=1.5.11.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies
```

**Lösning:** installerar .NET 4.5 eller senare på datorn.

**2. Räknare för prestandadata är tillgängliga i lagring men visas inte i portalen**

Portaler i de virtuella datorerna visar vissa prestandaräknare som standard. Om du inte ser prestandaräknarna och du vet att data komma genereras eftersom den är tillgänglig för lagring, kontrollerar du följande:

- Om data i lagring har räknarnamn på engelska. Om räknaren namnen inte är på engelska portal mått diagrammet inte kunna identifiera den.

- Om du använder jokertecken (\*) i ditt namn på prestandaräknare, portalen kommer inte kunna korrelera konfigurerade och samlat in räknaren.

**Minskning**: ändra datorns språket till engelska för Systemkonton. Om du vill göra det, Välj **Kontrollpanelen** > **Region** > **administrativa** > **inställningar**. Sedan avmarkera **Välkommen och systemkonton** så att det anpassade språket inte används för system-kontot. Kontrollera också att du inte använda jokertecken om du vill att portalen ska vara din primära förbrukning upplevelse.
