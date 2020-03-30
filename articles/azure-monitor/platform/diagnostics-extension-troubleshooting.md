---
title: Felsöka Azure Diagnostics-tillägg
description: Felsöka problem när du använder Azure-diagnostik i Azure Virtual Machines, Service Fabric eller Cloud Services.
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/08/2019
ms.openlocfilehash: 043369bd6112c4cac36539bbd764393d889439c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274585"
---
# <a name="azure-diagnostics-troubleshooting"></a>Azure Diagnostics-felsökning
I den här artikeln beskrivs felsökningsinformation som är relevant för att använda Azure Diagnostics. Mer information om Azure-diagnostik finns i [översikt över Azure Diagnostics](diagnostics-extension-overview.md).

## <a name="logical-components"></a>Logiska komponenter
**Diagnostik plugin launcher (DiagnosticsPluginLauncher.exe)**: Startar Azure Diagnostics-tillägget. Fungerar som startpunktsprocess.

**Diagnostik plugin (DiagnosticsPlugin.exe)**: Konfigurerar, startar och hanterar livslängden för övervakningsagenten. Det är den viktigaste processen som lanseras av bärraketen.

**Övervakningsagent (MonAgent\*.exe-processer):** Övervakar, samlar in och överför diagnostikdata.  

## <a name="logartifact-paths"></a>Sökvägar för logg/artefakt
Följande är sökvägarna till några viktiga loggar och artefakter. Vi hänvisar till denna information i resten av dokumentet.

### <a name="azure-cloud-services"></a>Azure Cloud Services
| Artefakt | Sökväg |
| --- | --- |
| **Konfigurationsfil för Azure Diagnostics** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version>\Config.txt |
| **Loggfiler** | C:\Loggar\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version>\ |
| **Lokalt arkiv för diagnostikdata** | C:\Resurser\Directory\<CloudServiceDeploymentID>. \<RoleName>. DiagnosticStore\WAD0107\Tabeller |
| **Konfigurationsfil för övervakningsagent** | C:\Resurser\Directory\<CloudServiceDeploymentID>. \<RoleName>. DiagnosticStore\WAD0107\Konfiguration\MaConfig.xml |
| **Tilläggspaket för Azure Diagnostics** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version> |
| **Sökväg till logginsamling** | %SystemDrive%\Paket\GuestAgent\ |
| **MonAgentHost loggfil** | C:\Resurser\Directory\<CloudServiceDeploymentID>. \<RoleName>. DiagnosticStore\WAD0107\Configuration\MonAgentHost.<seq_num>.log |

### <a name="virtual-machines"></a>Virtuella datorer
| Artefakt | Sökväg |
| --- | --- |
| **Konfigurationsfil för Azure Diagnostics** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<version>\RuntimeSettings |
| **Loggfiler** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\ |
| **Lokalt arkiv för diagnostikdata** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Tables |
| **Konfigurationsfil för övervakningsagent** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Configuration\MaConfig.xml |
| **Statusfil** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<version>\Status |
| **Tilläggspaket för Azure Diagnostics** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>|
| **Sökväg till logginsamling** | C:\WindowsAzure\Loggar\WaAppAgent.log |
| **MonAgentHost loggfil** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Configuration\MonAgentHost.<seq_num>.log |

## <a name="metric-data-doesnt-appear-in-the-azure-portal"></a>Måttdata visas inte i Azure-portalen
Azure Diagnostics tillhandahåller måttdata som kan visas i Azure-portalen. Om du har problem med att se data\* i portalen kontrollerar du tabellen WADMetrics i Azure Diagnostics-lagringskontot för att se om motsvarande måttposter finns där och se till att [resursleverantören](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services) Microsoft.Insights är registrerad.

Här är **partitionsnyckeln** för tabellen resurs-ID, virtuell dator eller virtuell datorskalauppsättning. **RowKey** är måttnamnet (kallas även prestandaräknarens namn).

Om resurs-ID:t är felaktigt kontrollerar du > **Om**  > **diagnostikkonfigurationsmåtten**ResourceId kontrollerar om resurs-ID:t är korrekt inställt. **Diagnostics** **Configuration**

Om det inte finns några data för det specifika måttet kontrollerar du > **DiagnostikkonfigurationsprestandaUträknaren** för att se om måttet (prestandaräknaren) ingår. **Diagnostics Configuration** Vi aktiverar följande räknare som standard:
- \Processor(_Total)\% processortid
- \Memory\Tillgängliga byte
- \ASP.NET-program(__Totalt__)\Begäranden/Sek
- \ASP.NET program(__Totalt__)\Totalt antal fel/sek
- \ASP.NET\Begäranden i kö
- \ASP.NET\Begäranden avvisade
- \Processor(w3wp)\% processortid
- \Process(w3wp)\Privata byte
- \Processortid för process(WaIISHost)\%
- \Process(WaIISHost)\Privata byte
- \Processortid för Process(WaWorkerHost)\%
- \Process(WaWorkerHost)\Privata byte
- \Minne\Sidfel/sek
- \.NET CLR-minne(\% _Global)_ Tid i GC
- \LogicalDisk(C:)\Byte för diskskrivning/sek
- \LogicalDisk(C:)\Diskläsningsbyten per sekund
- \LogicalDisk(D:)\Byte för diskskrivning/sek
- \LogicalDisk(D:)\Diskläsningsbyten per sekund

Om konfigurationen är korrekt inställd men du fortfarande inte kan se måttdata använder du följande riktlinjer som hjälper dig att felsöka.


## <a name="azure-diagnostics-is-not-starting"></a>Azure Diagnostics startar inte
Information om varför Azure Diagnostics inte kunde starta finns i filerna **DiagnosticsPluginLauncher.log** och **DiagnosticsPlugin.log** på loggfilsplatsen som angavs tidigare.

Om dessa loggar `Monitoring Agent not reporting success after launch`indikerar betyder det att det uppstod ett fel när MonAgentHost.exe startades. Titta på loggarna på den plats som `MonAgentHost log file` anges i föregående avsnitt.

Den sista raden i loggfilerna innehåller avslutningskoden.  

```
DiagnosticsPluginLauncher.exe Information: 0 : [4/16/2016 6:24:15 AM] DiagnosticPlugin exited with code 0
```
Om du hittar en **negativ** [avslutningskod](#references)läser du tabellen för [avslutningskod](#azure-diagnostics-plugin-exit-codes) i avsnittet Referenser .

## <a name="diagnostics-data-is-not-logged-to-azure-storage"></a>Diagnostikdata loggas inte till Azure Storage
Ta reda på om ingen av data visas eller om vissa data visas.

### <a name="diagnostics-infrastructure-logs"></a>Infrastrukturloggar för diagnostik
Diagnostik loggar alla fel i diagnostikinfrastrukturloggarna. Kontrollera att du har aktiverat [insamlingen av diagnostikinfrastrukturloggar i konfigurationen](#how-to-check-diagnostics-extension-configuration). Sedan kan du snabbt leta efter relevanta fel `DiagnosticInfrastructureLogsTable` som visas i tabellen i det konfigurerade lagringskontot.

### <a name="no-data-is-appearing"></a>Inga data visas
Den vanligaste orsaken till att händelsedata inte visas alls är att lagringskontoinformationen definieras felaktigt.

Lösning: Korrigera diagnostikkonfigurationen och installera om diagnostiken.

Om lagringskontot är korrekt konfigurerat fjärråtkomst till datorn och kontrollera att *DiagnosticsPlugin.exe* och *MonAgentCore.exe* körs. Om de inte körs startar du inte stegen i [Azure Diagnostics](#azure-diagnostics-is-not-starting).

Om processerna körs går du till [Är data som fångas in lokalt?](#is-data-getting-captured-locally)

Om detta inte löser problemet kan du försöka:

1. Avinstallera agenten
2. Ta bort katalog C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics
3. Installera agent igen


### <a name="part-of-the-data-is-missing"></a>En del av uppgifterna saknas
Om du får vissa data men inte alla, betyder det att datainsamlingen/överföringspipelinen är korrekt inställd. Följ underavsnitten här för att begränsa problemet.

#### <a name="is-the-collection-configured"></a>Är samlingen konfigurerad?
Diagnostikkonfigurationen innehåller instruktioner för en viss typ av data som ska samlas in. [Granska konfigurationen](#how-to-check-diagnostics-extension-configuration) för att kontrollera att du bara letar efter data som du har konfigurerat för samlingen.

#### <a name="is-the-host-generating-data"></a>Genererar värden data?
- **Prestandaräknare:** Öppna perfmon och kontrollera räknaren.

- **Spårningsloggar:** Fjärråtkomst till den virtuella datorn och lägg till en TextWriterTraceListener i appens config-fil.  Se https://msdn.microsoft.com/library/sk36c28t.aspx för att ställa in textlyssaren.  Kontrollera att `<trace>` elementet har `<trace autoflush="true">`.<br />
Om du inte ser att spårningsloggar genereras läser du Mer om spårningsloggar som saknas.

- **ETW-spår:** Fjärråtkomst till den virtuella datorn och installera PerfView.  I PerfView kör > du > **Filanvändarkommando Lyssna** > **etwprovder1****etwprovider2**och så vidare. **File** Kommandot **Lyssna** är skiftlägeskänsligt och det kan inte finnas blanksteg mellan den kommaavgränsade listan över ETW-providers. Om kommandot inte körs kan du välja **loggknappen** längst ned till höger i Perfview-verktyget för att se vad som försökte köras och vad resultatet blev.  Förutsatt att ingången är korrekt, dyker ett nytt fönster upp. Om några sekunder börjar du se ETW-spår.

- **Händelseloggar**: Fjärråtkomst till den virtuella datorn. Öppna `Event Viewer`och se sedan till att händelserna finns.

#### <a name="is-data-getting-captured-locally"></a>Hämtas data lokalt?
Kontrollera sedan att data hämtas lokalt.
Data lagras lokalt i `*.tsf` filer i det lokala arkivet för diagnostikdata. Olika typer av loggar samlas `.tsf` in i olika filer. Namnen liknar tabellnamnen i Azure Storage.

Till exempel, `Performance Counters` få `PerformanceCountersTable.tsf`samlas in i . Händelseloggar samlas in `WindowsEventLogsTable.tsf`i . Använd instruktionerna i avsnittet [Lokal loggextrahering](#local-log-extraction) för att öppna de lokala samlingsfilerna och kontrollera att du ser dem hämtas på disk.

Om du inte ser loggar som samlas in lokalt och redan har verifierat att värden genererar data, har du förmodligen ett konfigurationsproblem. Granska konfigurationen noggrant.

Granska även konfigurationen som genererades för MonitoringAgent MaConfig.xml. Kontrollera att det finns ett avsnitt som beskriver relevant loggkälla. Kontrollera sedan att den inte går förlorad i översättningen mellan diagnostikkonfigurationen och övervakningsagentkonfigurationen.

#### <a name="is-data-getting-transferred"></a>Överförs data?
Om du har verifierat att data hämtas lokalt men fortfarande inte ser dem i ditt lagringskonto gör du följande:

- Kontrollera att du har angett ett korrekt lagringskonto och att du inte har rullat över nycklar för det angivna lagringskontot. För Azure Cloud Services ser vi ibland `useDevelopmentStorage=true`att andra inte uppdaterar .

- Kontrollera att det angivna lagringskontot är korrekt. Kontrollera att du inte har nätverksbegränsningar som hindrar komponenterna från att nå offentliga lagringsslutpunkter. Ett sätt att göra det är att fjärråtkomst till maskinen, och sedan försöka skriva något till samma lagringskonto själv.

- Slutligen kan du titta på vilka fel som rapporteras av övervakningsagenten. Övervakningsagenten skriver sina `maeventtable.tsf`loggar i , som finns i det lokala arkivet för diagnostikdata. Följ instruktionerna i avsnittet [Lokal loggextrahering](#local-log-extraction) för att öppna filen. Försök sedan att avgöra `errors` om det finns som indikerar fel läsning till lokala filer som skriver till lagring.

### <a name="capturing-and-archiving-logs"></a>Samla in och arkivera loggar
Om du funderar på att kontakta support, det första de kan be dig är att samla loggar från din maskin. Du kan spara tid genom att göra det själv. Kör `CollectGuestLogs.exe` verktyget på Sökvägen för logginsamling. Den genererar en ZIP-fil med alla relevanta Azure-loggar i samma mapp.

## <a name="diagnostics-data-tables-not-found"></a>Det gick inte att hitta datatabeller för diagnostik
Tabellerna i Azure-lagring som innehåller ETW-händelser namnges med hjälp av följande kod:

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
Den här koden genererar fyra tabeller:

| Händelse | Tabellnamn |
| --- | --- |
| provider="prov1" &lt;Händelse id="1" /&gt; |WADEvent+MD5("prov1")+"1" |
| provider="prov1" &lt;Händelse id="2" händelseDestination="dest1" /&gt; |WADdest1 (På andra) |
| provider="prov1" &lt;DefaultEvents /&gt; |WADDefault+MD5("prov1") |
| provider="prov2" &lt;DefaultEvents händelseDestination="dest2" /&gt; |WADdest2 (På andra) |

## <a name="references"></a>Referenser

### <a name="how-to-check-diagnostics-extension-configuration"></a>Så här kontrollerar du konfigurationen av diagnostiktillägg
Det enklaste sättet att kontrollera din tilläggskonfiguration är att gå till [Azure Resource Explorer](https://resources.azure.com)och sedan gå till den virtuella datorn eller molntjänsten där Azure Diagnostics-tillägget (IaaSDiagnostics / PaaDiagnostics) är.

Alternativt kan fjärrskrivbord till datorn och titta på Azure Diagnostics Configuration-filen som beskrivs i sökvägavsnittet Logga artefakter.

I båda fallen söker du efter **Microsoft.Azure.Diagnostics**och sedan efter fältet **xmlCfg** eller **WadCfg.**

Om du söker på en virtuell dator och **wadCfg-fältet** finns betyder det att konfigurationen är i JSON-format. Om **xmlCfg-fältet** finns betyder det att config är i XML och är base64-kodad. Du måste [avkoda](https://www.bing.com/search?q=base64+decoder) den för att se XML som lästes in av diagnostik.

Om du väljer konfigurationen från disken för molntjänstrollen är databasen base64-kodad, så du måste [avkoda](https://www.bing.com/search?q=base64+decoder) den för att se XML-koden som lästes in av diagnostik.

### <a name="azure-diagnostics-plugin-exit-codes"></a>Exit-koder för plugin-insticksprogram i Azure Diagnostics
Insticksprogrammet returnerar följande exitkoder:

| Slutkod | Beskrivning |
| --- | --- |
| 0 |Lyckades. |
| -1 |Allmänt fel. |
| -2 |Det gick inte att läsa in rcf-filen.<p>Det här interna felet ska endast inträffa om plugin-startprogrammet för gästagenten anropas felaktigt på den virtuella datorn. |
| -3 |Det går inte att läsa in konfigurationsfilen för diagnostik.<p><p>Lösning: Orsakas av att en konfigurationsfil inte skickar schemaverifiering. Lösningen är att tillhandahålla en konfigurationsfil som överensstämmer med schemat. |
| -4 |En annan instans av övervakningsagenten Diagnostik använder redan den lokala resurskatalogen.<p><p>Lösning: Ange ett annat värde för **LocalResourceDirectory**. |
| -6 |Gästagenten plugin launcher försökte starta Diagnostik med en ogiltig kommandorad.<p><p>Det här interna felet ska endast inträffa om plugin-startprogrammet för gästagenten anropas felaktigt på den virtuella datorn. |
| -10 |Insticksprogrammet Diagnostik avslutades med ett ohanterat undantag. |
| -11 |Gästagenten kunde inte skapa processen som ansvarar för att starta och övervaka övervakningsagenten.<p><p>Lösning: Kontrollera att det finns tillräckliga systemresurser för att starta nya processer.<p> |
| -101 |Ogiltiga argument när du anropar insticksprogrammet Diagnostik.<p><p>Det här interna felet ska endast inträffa om plugin-startprogrammet för gästagenten anropas felaktigt på den virtuella datorn. |
| -102 |Plugin-processen kan inte initiera sig själv.<p><p>Lösning: Kontrollera att det finns tillräckliga systemresurser för att starta nya processer. |
| -103 |Plugin-processen kan inte initiera sig själv. Det går inte att skapa loggerobjektet.<p><p>Lösning: Kontrollera att det finns tillräckliga systemresurser för att starta nya processer. |
| -104 |Det gick inte att läsa in rcf-filen som tillhandahålls av gästagenten.<p><p>Det här interna felet ska endast inträffa om plugin-startprogrammet för gästagenten anropas felaktigt på den virtuella datorn. |
| -105 |Insticksprogrammet Diagnostik kan inte öppna konfigurationsfilen för diagnostik.<p><p>Det här interna felet bör bara inträffa om insticksprogrammet diagnostik anropas manuellt felaktigt på den virtuella datorn. |
| -106 |Det går inte att läsa konfigurationsfilen för diagnostik.<p><p>Orsakas av en konfigurationsfil som inte passerar schemavalidering. <br><br>Lösning: Tillhandahålla en konfigurationsfil som överensstämmer med schemat. Mer information finns i [Så här kontrollerar du konfigurationen](#how-to-check-diagnostics-extension-configuration)av diagnostiktillägg . |
| -107 |Resurskatalogen går vidare till övervakningsagenten är ogiltig.<p><p>Det här interna felet bör bara inträffa om övervakningsagenten anropas felaktigt på den virtuella datorn.</p> |
| -108 |Det gick inte att konvertera konfigurationsfilen för diagnostik till konfigurationsfilen för övervakningsagenten.<p><p>Det här interna felet bör bara inträffa om insticksprogrammet diagnostik anropas manuellt med en ogiltig konfigurationsfil. |
| -110 |Konfigurationsfel för allmän diagnostik.<p><p>Det här interna felet bör bara inträffa om insticksprogrammet diagnostik anropas manuellt med en ogiltig konfigurationsfil. |
| -111 |Det gick inte att starta övervakningsagenten.<p><p>Lösning: Kontrollera att det finns tillräckliga systemresurser tillgängliga. |
| -112 |Allmänt fel |

### <a name="local-log-extraction"></a>Lokal loggextrahering
Övervakningsagenten samlar in loggar `.tsf` och artefakter som filer. Filen `.tsf` är inte läsbar men du `.csv` kan konvertera den till en enligt följande:

```
<Azure diagnostics extension package>\Monitor\x64\table2csv.exe <relevantLogFile>.tsf
```
En ny `<relevantLogFile>.csv` fil som anropas skapas `.tsf` i samma sökväg som motsvarande fil.

>[!NOTE]
> Du behöver bara köra det här verktyget mot huvudfilen .tsf (till exempel PerformanceCountersTable.tsf). De medföljande filerna (till exempel\*\*PerformanceCountersTables_ 001.tsf, PerformanceCountersTables_\*\*002.tsf och så vidare) bearbetas automatiskt.

### <a name="more-about-missing-trace-logs"></a>Mer om saknade spårningsloggar

>[!NOTE]
> Följande information gäller främst Azure Cloud Services om du inte har konfigurerat DiagnosticsMonitorTraceListener på ett program som körs på din IaaS VM.

- Kontrollera att **DiagnosticMonitorTraceListener** är konfigurerad i web.config eller app.config.  Detta är konfigurerat som standard i molntjänstprojekt. Vissa kunder kommenterar det dock, vilket gör att spårningssatserna inte samlas in av diagnostik.

- Om loggar inte skrivs från metoden **OnStart** eller **Run** kontrollerar du att **DiagnosticMonitorTraceListener** finns i app.config.  Som standard är det i web.config, men som bara gäller för kod som körs inom w3wp.exe. Så du behöver det i app.config för att fånga spår som körs i WaIISHost.exe.

- Kontrollera att du använder **Diagnostics.Trace.TraceXXX** i stället för **Diagnostics.Debug.WriteXXX.** Felsökningssatserna tas bort från en versionsversion.

- Kontrollera att den kompilerade koden faktiskt har **diagnostics.Trace-linjerna** (använd Reflektor, ildasm eller ILSpy för att verifiera). **Kommandon för Diagnostics.Trace** tas bort från den kompilerade binärfilen om du inte använder trace-villkorlig kompileringssymbol. Detta är ett vanligt problem som uppstår när du använder msbuild för att skapa ett projekt.   

## <a name="known-issues-and-mitigations"></a>Kända problem och mildrande åtgärder
Här är en lista över kända problem med kända mildrande åtgärder:

**1.**

Windows Azure Diagnostics Extension har ett körningsberoende av .NET 4.5-ramverket eller senare. I skrivande stund har alla datorer som är etablerade för Azure Cloud Services, liksom alla officiella avbildningar som baseras på virtuella Azure-datorer, .NET 4.5 eller senare installerade.

Det är fortfarande möjligt att stöta på en situation där du försöker köra Windows Azure Diagnostics Extension på en dator som inte har .NET 4.5 eller senare. Detta händer när du skapar datorn från en gammal bild eller ögonblicksbild, eller när du tar med din egen anpassade disk.

Detta visas vanligtvis som en avslutningskod **255** när **diagnostikPluginLauncher.exe körs.** Fel inträffar på grund av följande ohanterade undantag:
```
System.IO.FileLoadException: Could not load file or assembly 'System.Threading.Tasks, Version=1.5.11.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies
```

**Begränsning:** Installera .NET 4.5 eller senare på datorn.

**2. Prestandaräknare data är tillgänglig i lagring men inte visas i portalen**

Portalupplevelsen i de virtuella datorerna visar som standard vissa prestandaräknare. Om du inte ser prestandaräknarna och du vet att data genereras eftersom de är tillgängliga i lagringen kontrollerar du följande:

- Om data i lagring har motnamn på engelska. Om räknarnamnen inte är på engelska kan inte diagrammet för portalmått känna igen det. **Begränsning**: Ändra maskinens språk till engelska för systemkonton. Det gör du genom att välja**Administrativa** > **inställningar för** **Kontrollpanelen** > **Region** > region . Avmarkera sedan **välkomstskärm och systemkonton** så att det anpassade språket inte tillämpas på systemkontot.

- Om du använder jokertecken\*( ) i dina prestandaräknarenamn kan portalen inte korrelera den konfigurerade och insamlade räknaren när prestandaräknarna skickas till Azure Storage-diskbänken. **Begränsning:** För att vara säker på att du kan\*använda jokertecken och låta portalen expandera ( ), dirigera dina prestandaräknare till Azure Monitor-diskbänken.

