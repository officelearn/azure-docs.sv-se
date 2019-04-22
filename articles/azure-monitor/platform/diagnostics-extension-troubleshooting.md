---
title: Felsöka Azure Diagnostics-tillägg
description: Felsöka problem när du använder Azure-diagnostik i Azure Virtual Machines, Service Fabric och Cloud Services.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.subservice: diagnostic-extension
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: robb
ms.openlocfilehash: 81c93900acf2d75eeb8e4fdc8da7d563f3a59595
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59699106"
---
# <a name="azure-diagnostics-troubleshooting"></a>Felsökning av Azure-diagnostik
Den här artikeln beskriver felsökningsinformation som är relevant för med hjälp av Azure Diagnostics. Läs mer om Azure diagnostics [översikt över Azure Diagnostics](diagnostics-extension-overview.md).

## <a name="logical-components"></a>Logiska komponenter
**Diagnostics Plugin Launcher (DiagnosticsPluginLauncher.exe)**: Startar Azure Diagnostics-tillägget. Fungerar som posten pekar processen.

**Diagnostics Plugin (DiagnosticsPlugin.exe)**: Konfigurerar, startar och hanterar livslängden för övervakningsagenten. Det är den viktigaste process som startas av starta.

**Monitoring Agent (MonAgent\*.exe processer)**: Övervakar, samlar in och överför diagnostics-data.  

## <a name="logartifact-paths"></a>Logg-/ artefakt sökvägar
Följande är sökvägarna till vissa viktiga loggar och artefakter. Vi refererar till den här informationen i resten av dokumentet.

### <a name="azure-cloud-services"></a>Azure Cloud Services
| Artefakt | Sökväg |
| --- | --- |
| **Azure Diagnostics-konfigurationsfil** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version>\Config.txt |
| **Loggfiler** | C:\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version>\ |
| **Lokal lagring för diagnostikdata** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Tables |
| **Övervakning av agentens konfigurationsfil** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Configuration\MaConfig.xml |
| **Azure Diagnostics-tillägg-paketet** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version> |
| **Loggsökväg samling verktyg** | %SystemDrive%\Packages\GuestAgent\ |
| **MonAgentHost loggfil** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Configuration\MonAgentHost.<seq_num>.log |

### <a name="virtual-machines"></a>Virtuella datorer
| Artefakt | Sökväg |
| --- | --- |
| **Azure Diagnostics-konfigurationsfil** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<version>\RuntimeSettings |
| **Loggfiler** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\ |
| **Lokal lagring för diagnostikdata** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Tables |
| **Övervakning av agentens konfigurationsfil** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Configuration\MaConfig.xml |
| **Statusfil** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<version>\Status |
| **Azure Diagnostics-tillägg-paketet** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>|
| **Loggsökväg samling verktyg** | C:\WindowsAzure\Packages |
| **MonAgentHost loggfil** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Configuration\MonAgentHost.<seq_num>.log |

## <a name="metric-data-doesnt-appear-in-the-azure-portal"></a>Måttdata visas inte i Azure portal
Azure-diagnostik innehåller måttdata som kan visas i Azure-portalen. Om du har problem Se data i portalen, kontrollerar du WADMetrics\* tabell i Azure Diagnostics storage-konto och se om motsvarande mått poster är det.

Här är den **PartitionKey** i tabellen är resurs-ID, virtuell dator eller virtuell dator i skalningsuppsättningen. **RowKey** är Måttnamn (även kallat prestandaräknarnamnet).

Om resurs-ID är felaktigt, kontrollera **diagnostik** **Configuration** > **mått** > **ResourceId**att se om resurs-ID är korrekt.

Om det finns inga data för det specifika måttet, kontrollera **Diagnostiky** > **PerformanceCounter** att se om mått (prestandaräknaren) ingår. Vi möjliggör följande räknare som standard:
- \Processor(_Total)\% processortid
- \Memory\Tillgängliga byte
- \ASP.NET-program program (__totala__) \Requests/Sec
- \ASP.NET-program program (__totala__) \Errors totalt/sek
- \ASP.NET\Requests i kö
- \ASP.NET\Requests avvisades
- \Processor(W3wp)\% processortid
- \Process (w3wp) \Private byte
- \Process(WaIISHost)\% processortid
- \Process (WaIISHost) \Private byte
- \Process(WaWorkerHost)\% processortid
- \Process (WaWorkerHost) \Private byte
- \Memory\Page fel/sek
- \.NET CLR-minne (_globala_)\% tid i GC i
- \LogicalDisk(C:)\Disk Write Bytes/sec
- \LogicalDisk (C:) \Disk-lästa byte/s
- \LogicalDisk(D:)\Disk Write Bytes/sec
- \LogicalDisk (D:) \Disk-lästa byte/s

Om konfigurationen är korrekt, men du kan fortfarande inte kan se måttdata, Använd följande riktlinjer för felsökning.


## <a name="azure-diagnostics-is-not-starting"></a>Startar inte Azure-diagnostik
Information om varför Azure Diagnostics gick inte att starta finns i den **DiagnosticsPluginLauncher.log** och **DiagnosticsPlugin.log** filer på loggplatsen som angavs tidigare.

Om de här loggarna visar `Monitoring Agent not reporting success after launch`, betyder det uppstod ett fel som startar MonAgentHost.exe. Titta på loggarna på den plats som anges för `MonAgentHost log file` i föregående avsnitt.

Den sista raden i loggfilerna innehåller slutkoden.  

```
DiagnosticsPluginLauncher.exe Information: 0 : [4/16/2016 6:24:15 AM] DiagnosticPlugin exited with code 0
```
Om du hittar en **negativt** slutkod, referera till den [avsluta kod tabell](#azure-diagnostics-plugin-exit-codes) i den [refererar till avsnittet](#references).

## <a name="diagnostics-data-is-not-logged-to-azure-storage"></a>Diagnostikdata loggas inte till Azure Storage
Avgör om inga data visas eller vissa data visas.

### <a name="diagnostics-infrastructure-logs"></a>Diagnostikinfrastrukturloggar
Diagnostik loggar alla fel i diagnostikinfrastrukturloggar. Kontrollera att du har aktiverat den [infångandet av diagnostik infrastruktur loggar i konfigurationen](#how-to-check-diagnostics-extension-configuration). Du kan snabbt leta eventuella relevanta fel som visas i den `DiagnosticInfrastructureLogsTable` tabellen i konfigurerade lagringskontot.

### <a name="no-data-is-appearing"></a>Visas några data
Den vanligaste orsaken till att händelsedata inte alls är att lagringskontoinformation definieras felaktigt.

Lösning: Korrigera konfigurationen diagnostik och installera om diagnostik.

Om lagringskontot är korrekt konfigurerade, remote access in på datorn och kontrollera att *DiagnosticsPlugin.exe* och *MonAgentCore.exe* körs. Om de inte körs, följer du stegen i [startar inte Azure Diagnostics](#azure-diagnostics-is-not-starting).

Om processerna som körs, går du till [är data som komma hämtats lokalt?](#is-data-getting-captured-locally) och i anvisningarna.

Om detta inte löser problemet, försök att:

1. Avinstallera agenten
2. Ta bort katalogen C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics
3. Installera agenten igen


### <a name="part-of-the-data-is-missing"></a>En del av informationen som saknas
Om du får vissa data men inte alla, innebär det att datapipeline för samling/disköverföring är korrekt. Följ underavsnitt här om du vill begränsa problemet.

#### <a name="is-the-collection-configured"></a>Har konfigurerats för samlingen
Diagnostikkonfigurationen innehåller instruktioner för en viss typ av data ska samlas in. [Granska konfigurationen](#how-to-check-diagnostics-extension-configuration) att verifiera att du bara söker efter data som du har konfigurerat för samlingen.

#### <a name="is-the-host-generating-data"></a>Värden genererar data?
- **Prestandaräknare**: Öppna perfmon och kontrollera räknaren.

- **Spårningsloggar**:  Fjärransluten åtkomst till den virtuella datorn och Lägg till en TextWriterTraceListener i appens config-filen.  Se https://msdn.microsoft.com/library/sk36c28t.aspx att ställa in text-lyssnaren.  Kontrollera att den `<trace>` elementet har `<trace autoflush="true">`.<br />
Om du inte ser spårningsloggar som genereras, Läs mer om spårningsloggar som saknas.

- **ETW-spårningar**: Fjärråtkomst till den virtuella datorn och installera PerfView.  Kör i PerfView, **filen** > **användarkommando** > **lyssna etwprovder1** > **etwprovider2**och så vidare. Den **lyssna** kommandot är skiftlägeskänsligt och det får inte finnas blanksteg mellan CSV-listan för ETW-leverantörer. Om kommandot misslyckas att köra kan du välja den **Log** längst ned till höger i verktyget Perfview för att se vad försökte köra och vilka resultatet var.  Anta att indata är rätt och öppnas ett nytt fönster. I några sekunder börjar du se ETW-spårningar.

- **Händelseloggar**: Fjärråtkomst till den virtuella datorn. Öppna `Event Viewer`, och sedan kontrollera att det finns händelser.

#### <a name="is-data-getting-captured-locally"></a>Data komma avbildas lokalt?
Kontrollera därefter data komma avbildas lokalt.
Data lagras lokalt i `*.tsf` filer i det lokala arkivet för diagnostikdata. Olika typer av loggar hämta som samlas in i olika `.tsf` filer. Namnen liknar tabellnamn i Azure Storage.

Till exempel `Performance Counters` hämta som samlats in på `PerformanceCountersTable.tsf`. Händelseloggar hämta som samlas in i `WindowsEventLogsTable.tsf`. Följ instruktionerna i den [lokal logg extrahering](#local-log-extraction) avsnitt för att öppna filerna lokal insamling och kontrollera att du ser dem komma som samlats in på disk.

Om du inte ser komma lokalt insamlade loggar och redan har kontrollerat att värden genererar data, har du förmodligen konfigurationsproblem. Granska konfigurationen noggrant.

Granska även den konfiguration som genererades för MonitoringAgent MaConfig.xml. Kontrollera att det finns ett avsnitt som beskriver relevant logg-källa. Kontrollera att det inte förloras mellan diagnostikkonfigurationen och agentkonfigurationen av övervakningen.

#### <a name="is-data-getting-transferred"></a>Är data överföras?
Om du har kontrollerat att data komma avbildas lokalt men du fortfarande inte ser den i ditt storage-konto kan du vidta följande steg:

- Kontrollera att du har angett ett lagringskonto som är rätt och att du inte har flyttats över nycklar för det angivna lagringskontot. För Azure Cloud Services, ibland vi se att personer inte uppdatera `useDevelopmentStorage=true`.

- Kontrollera att det angivna lagringskontot är korrekt. Kontrollera att du inte har nätverksbegränsningar för som gör att komponenterna från att nå offentlig lagring slutpunkter. Ett sätt att göra det är att fjärråtkomst in på datorn och försök sedan att skriva till samma lagringskonto något själv.

- Slutligen kan du titta på vilka fel rapporterats av en Övervakningsagent. Övervakningsagenten skriver loggar i `maeventtable.tsf`, som finns i det lokala arkivet för diagnostikdata. Följ instruktionerna i den [lokal logg extrahering](#local-log-extraction) avsnittet för att öppna den här filen. Försök att fastställa om det finns `errors` som indikerar fel vid läsning av lokala filer skriva till lagring.

### <a name="capturing-and-archiving-logs"></a>Samla in och arkivera loggar
Om du funderar på att kontakta supporten, är det första kanske du ombeds att samla in loggar från din dator. Du kan spara tid genom att göra det själv. Kör den `CollectGuestLogs.exe` utility på loggsökväg samling-verktyget. Den genererar en .zip-fil med alla relevanta Azure-loggarna i samma mapp.

## <a name="diagnostics-data-tables-not-found"></a>Diagnostik datatabeller hittades inte
Tabellerna i Azure storage som innehåller ETW-händelser är namngivna med hjälp av följande kod:

```csharp
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
Den här koden skapar fyra tabeller:

| Händelse | Tabellnamn |
| --- | --- |
| Provider = ”prov1” &lt;händelse-id = ”1” /&gt; |WADEvent + MD5("prov1") + ”1” |
| Provider = ”prov1” &lt;händelse-id = ”2” eventDestination = ”dest1” /&gt; |WADdest1 |
| Provider = ”prov1” &lt;DefaultEvents /&gt; |WADDefault+MD5("prov1") |
| Provider = ”prov2” &lt;DefaultEvents eventDestination = ”dest2” /&gt; |WADdest2 |

## <a name="references"></a>Referenser

### <a name="how-to-check-diagnostics-extension-configuration"></a>Hur du kontrollerar konfigurationen för Diagnostiktillägg
Det enklaste sättet att kontrollera din tilläggskonfigurationen är att gå till [Azure Resource Explorer](http://resources.azure.com), och gå sedan till den virtuella datorn eller molnet var Azure Diagnostics-tillägget (IaaSDiagnostics / PaaDiagnostics) är.

Du kan också fjärrskrivbord på datorn och titta på Azure Diagnostics konfigurationsfilen som beskrivs i avsnittet Log artefakter sökväg.

I båda fallen söker du efter **Microsoft.Azure.Diagnostics**, och sedan för den **xmlCfg** eller **WadCfg** fält.

Om du söker på en virtuell dator och **WadCfg** fältet finns, betyder det konfig är i JSON-format. Om den **xmlCfg** fältet finns, innebär det att konfig XML och är base64-kodad. Du behöver [avkoda den](https://www.bing.com/search?q=base64+decoder) att se den XML som har lästs in av diagnostik.

För molntjänstroll, om du väljer konfigurationen från disken, data är base64-kodad, så du behöver [avkoda den](https://www.bing.com/search?q=base64+decoder) att se den XML som har lästs in av diagnostik.

### <a name="azure-diagnostics-plugin-exit-codes"></a>Azure Diagnostics-plugin-programmet slutkoder för aktiviteter
Plugin-programmet returnerar följande svarskoderna:

| Slutkod | Beskrivning |
| --- | --- |
| 0 |Lyckades. |
| -1 |Allmänt fel. |
| -2 |Det går inte att läsa in filen rcf.<p>Den här internt fel ska bara hända om plugin-programmet för gäst Agentstart anropas manuellt felaktigt på den virtuella datorn. |
| -3 |Det går inte att läsa in konfigurationsfilen diagnostik.<p><p>Lösning: Orsakas av en konfigurationsfil som passerar inte schemavalidering. Lösningen är att tillhandahålla en konfigurationsfil som överensstämmer med schemat. |
| -4 |En annan instans av övervakningsagenten diagnostik använder redan katalogen lokal resurs.<p><p>Lösning: Ange ett annat värde för **LocalResourceDirectory**. |
| -6 |Plugin-programmet för gäst Agentstart försökte starta diagnostik med en ogiltig kommandorad.<p><p>Den här internt fel ska bara hända om plugin-programmet för gäst Agentstart anropas manuellt felaktigt på den virtuella datorn. |
| -10 |Plugin-programmet diagnostik avslutades med ett ohanterat undantag. |
| -11 |Det gick inte att skapa processen som ansvarar för att starta och övervaka övervakningsagenten gästagenten.<p><p>Lösning: Kontrollera att det finns tillräckligt med systemresurser för att starta nya processer.<p> |
| -101 |Ogiltiga argument vid anrop av diagnostik-plugin-programmet.<p><p>Den här internt fel ska bara hända om plugin-programmet för gäst Agentstart anropas manuellt felaktigt på den virtuella datorn. |
| -102 |Processen för plugin-programmet kan inte initieras.<p><p>Lösning: Kontrollera att det finns tillräckligt med systemresurser för att starta nya processer. |
| -103 |Processen för plugin-programmet kan inte initieras. Mer specifikt kan det inte går att skapa logger-objektet.<p><p>Lösning: Kontrollera att det finns tillräckligt med systemresurser för att starta nya processer. |
| -104 |Det går inte att läsa in filen rcf som tillhandahålls av gästagenten.<p><p>Den här internt fel ska bara hända om plugin-programmet för gäst Agentstart anropas manuellt felaktigt på den virtuella datorn. |
| -105 |Diagnostik-plugin-programmet kan inte öppna konfigurationsfilen diagnostik.<p><p>Den här internt fel ska bara hända om diagnostik plugin-programmet anropas manuellt felaktigt på den virtuella datorn. |
| -106 |Det går inte att läsa konfigurationsfilen för diagnostik.<p><p>Orsakas av en konfigurationsfil som passerar inte schemavalidering. <br><br>Lösning: Ange en konfigurationsfil som överensstämmer med schemat. Mer information finns i [hur du kontrollerar konfigurationen av Diagnostiktillägget](#how-to-check-diagnostics-extension-configuration). |
| -107 |Resursen directory passet av övervakningsagenten är ogiltig.<p><p>Den här internt fel ska bara hända om övervakningsagenten anropas manuellt felaktigt på den virtuella datorn.</p> |
| -108 |Det går inte att konvertera diagnostik konfigurationsfilen till övervakning agentens konfigurationsfil.<p><p>Den här internt fel ska bara hända om plugin-programmet diagnostik manuellt har anropats med en ogiltig konfigurationsfil. |
| -110 |Allmänna diagnostik konfigurationsfel.<p><p>Den här internt fel ska bara hända om plugin-programmet diagnostik manuellt har anropats med en ogiltig konfigurationsfil. |
| -111 |Det går inte att starta övervakningsagenten.<p><p>Lösning: Kontrollera att det finns tillräckligt med systemresurser. |
| -112 |Allmänt fel |

### <a name="local-log-extraction"></a>Extrahering av lokal logg
Övervakningsagenten samlar in loggar och artefakter som `.tsf` filer. Den `.tsf` det går inte att läsa filen men du kan konvertera den till en `.csv` på följande sätt:

```
<Azure diagnostics extension package>\Monitor\x64\table2csv.exe <relevantLogFile>.tsf
```
En ny fil kallas `<relevantLogFile>.csv` skapas i samma sökväg som motsvarande `.tsf` fil.

>[!NOTE]
> Du behöver bara att köra det här verktyget mot den huvudsakliga .tsf-filen (till exempel PerformanceCountersTable.tsf). Tillhörande filer (till exempel PerformanceCountersTables_\*\*001.tsf PerformanceCountersTables_\*\*002.tsf och så vidare) bearbetas automatiskt.

### <a name="more-about-missing-trace-logs"></a>Mer om saknas spårningsloggar

>[!NOTE]
> Följande information gäller främst Azure Cloud Services om du har konfigurerat DiagnosticsMonitorTraceListener på ett program som körs på IaaS-VM.

- Kontrollera att den **DiagnosticMonitorTraceListener** konfigureras i web.config eller app.config.  Detta är konfigurerat som standard i molntjänstprojekt. Men vissa kunder kommentera den ut, vilket gör att spårningsinstruktioner som inte ska samlas in av diagnostik.

- Om loggar komma inte skrivs från den **OnStart** eller **kör** metod, se till att den **DiagnosticMonitorTraceListener** är i app.config.  Som standard är det i filen web.config, men som gäller endast för kod som körs i w3wp.exe. Så behöver du det i app.config att fånga in spårningar som körs i WaIISHost.exe.

- Kontrollera att du använder **Diagnostics.Trace.TraceXXX** i stället för **Diagnostics.Debug.WriteXXX.** Felsökningsuttryck tas bort från en slutversion.

- Kontrollera att den kompilerade koden faktiskt har den **Diagnostics.Trace rader** (Använd reflektor, ildasm eller ILSpy för att kontrollera). **Diagnostics.Trace** kommandon har tagits bort från den kompilerade binära filen om du inte använder symbolen TRACE villkorlig kompilering. Det här är ett vanligt problem som uppstår när du använder msbuild för att skapa ett projekt.   

## <a name="known-issues-and-mitigations"></a>Kända problem och åtgärder
Här är en lista över kända problem med kända åtgärder:

**1. .NET 4.5 beroende**

Windows Azure Diagnostics-tillägget har ett runtime beroende på framework .NET 4.5 eller senare. Vid tidpunkten för att skriva alla datorer som har etablerats för Azure Cloud Services, samt alla officiella avbildningar som baseras på Azure-datorer, .NET 4.5 eller senare installerat.

Det är fortfarande möjligt stöter en situation där du försöker köra Windows Azure Diagnostics-tillägg på en dator som inte har .NET 4.5 eller senare. Detta inträffar när du skapar din dator från en gammal bild eller en ögonblicksbild eller när du vill aktivera dina egna anpassade disk.

Detta vanligtvis manifest som en slutkod **255** när du kör **DiagnosticsPluginLauncher.exe.** Felet inträffar på grund av följande undantag:
```
System.IO.FileLoadException: Could not load file or assembly 'System.Threading.Tasks, Version=1.5.11.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies
```

**Lösning:** Installera .NET 4.5 eller senare på din dator.

**2. Data i prestandaräknare finns i lagring, men inte i portalen**

Portalmiljö på de virtuella datorerna visar vissa prestandaräknare som standard. Om du inte ser prestandaräknarna och du vet att data komma genereras eftersom det är tillgängligt för lagring bör du kontrollera följande:

- Om data i lagring har namn på prestandaräknare på engelska. Om räknarnamnen inte är på engelska, portal måttdiagram inte kunna identifiera den. **Minskning**: Ändra datorns språk till engelska för Systemkonton. Om du vill göra detta, Välj **Kontrollpanelen** > **Region** > **administrativa** > **inställningar**. Avmarkera sedan **Välkommen och systemkonton** så att anpassat språk inte används på system-kontot.

- Om du använder jokertecken (\*) i ditt namn på prestandaräknare, portalen kommer inte kunna korrelera räknaren konfigurerade och samlat in när prestandaräknarna som ska skickas till Azure Storage-mottagare. **Minskning**: Att kontrollera att du kan använda jokertecken och få portalen expanderar den (\*), dirigera dina prestandaräknare till den [”Azure Monitor” mottagare](diagnostics-extension-schema.md#diagnostics-extension-111).

