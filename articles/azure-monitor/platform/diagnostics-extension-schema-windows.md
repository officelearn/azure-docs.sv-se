---
title: Tilläggsschema för Windows-diagnostik
description: Konfigurationsschemareferens för Windows diagnostics-tillägg (WAD) i Azure Monitor.
ms.subservice: diagnostic-extension
ms.topic: reference
author: bwren
ms.author: bwren
ms.date: 01/20/2020
ms.openlocfilehash: c04fc82b8b04e474a656a0849177f7aa5d27b427
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676419"
---
# <a name="windows-diagnostics-extension-schema"></a>Tilläggsschema för Windows-diagnostik
Azure Diagnostics-tillägget är en agent i Azure Monitor som samlar in övervakningsdata från gästoperativsystemet och arbetsbelastningar för Azure-beräkningsresurser. I den här artikeln beskrivs schemat som används för konfiguration av diagnostiktillägget på virtuella Datorer i Windows och andra beräkningsresurser.

> [!NOTE]
> Schemat i den här artikeln gäller för versionerna 1.3 och nyare (Azure SDK 2.4 och nyare). Nyare konfigurationsavsnitt kommenteras för att visa i vilken version de lades till. Version 1.0 och 1.2 av schemat har arkiverats och inte längre är tillgängliga. 

## <a name="public-configuration-file-schema"></a>Schema för offentlig konfigurationsfil

Hämta den offentliga konfigurationsfilschemadefinitionen genom att köra följande PowerShell-kommando:  

```powershell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  


## <a name="common-attribute-types"></a>Vanliga attributtyper  
 **attributet scheduledTransferPeriod** visas i flera element. Det är intervallet mellan schemalagda överföringar till lagring avrundat uppåt till närmaste minut. Värdet är en [XML "Varaktighetsdatatyp".](https://www.w3schools.com/xml/schema_dtypes_date.asp)


## <a name="diagnosticsconfiguration-element"></a>Diagnostikkonfigureringselement  
 *Träd: Rot - DiagnostikKonfigurering*

Tillagd i version 1.3.  

Elementet på den översta nivån i konfigurationsfilen för diagnostik.  

**Attribut** xmlns - XML-namnområdet för konfigurationsfilen för diagnostik är:  
`http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration`


|Underordnade element|Beskrivning|  
|--------------------|-----------------|  
|**OffentligaConfig**|Krävs. Se beskrivning någon annanstans på den här sidan.|  
|**PrivatConfig**|Valfri. Se beskrivning någon annanstans på den här sidan.|  
|**IsEnabled**|Boolean. Se beskrivning någon annanstans på den här sidan.|  

## <a name="publicconfig-element"></a>PublicConfig Element  
 *Träd: Rot - DiagnostikKonfigurering - OffentligKonfig*

 Beskriver konfigurationen för offentlig diagnostik.  

|Underordnade element|Beskrivning|  
|--------------------|-----------------|  
|**WadCfg (på en)**|Krävs. Se beskrivning någon annanstans på den här sidan.|  
|**StorageAccount**|Namnet på Azure Storage-kontot för att lagra data i. Kan också anges som en parameter när du kör cmdleten Set-AzureServiceDiagnosticsExtension.|  
|**StorageType (lagringstyp)**|Kan vara *Tabell,* *Blob*eller *TableAndBlob*. Tabellen är standard. När TableAndBlob väljs skrivs diagnostikdata två gånger - en gång till varje typ.|  
|**LocalResourceDirectory**|Katalogen på den virtuella datorn där övervakningsagenten lagrar händelsedata. Om inte, ange, används standardkatalogen:<br /><br /> För en arbetar-/webbroll:`C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> För en virtuell dator:`C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> Obligatoriska attribut är:<br /><br /> - **sökväg** - Katalogen på det system som ska användas av Azure Diagnostics.<br /><br /> - **expandEnvironment** - Styr om miljövariabler expanderas i sökvägens namn.|  

## <a name="wadcfg-element"></a>WadCFG-element  
 *Träd: Root - DiagnostikKonfigurering - PublicConfig - WadCFG*

 Identifierar och konfigurerar telemetridata som ska samlas in.  


## <a name="diagnosticmonitorconfiguration-element"></a>Element för diagnostikövervakarkonfiguration
 *Träd: Rot - DiagnostikKonfigurering - PublicConfig - WadCFG - DiagnosticMonitorConfiguration*

 Krävs

|Attribut|Beskrivning|  
|----------------|-----------------|  
| **overallQuotaInMB** | Den maximala mängden lokalt diskutrymme som kan förbrukas av olika typer av diagnostikdata som samlas in av Azure Diagnostics. Standardinställningen är 4096 MB.<br />
|**användaProxyServer** | Konfigurera Azure Diagnostics så att proxyserverinställningarna används enligt IE-inställningarna.|
|**Sjunker** | Tillagd i 1,5. Valfri. Pekar på en sink-plats om du också vill skicka diagnostikdata för alla underordnade element som stöder sänkor. Sink exempel är Application Insights eller Event Hubs.|  


<br /> <br />

|Underordnade element|Beskrivning|  
|--------------------|-----------------|  
|**CrashDumps (CrashDumps)**|Se beskrivning någon annanstans på den här sidan.|  
|**DiagnosticInfrastructureLogs**|Aktivera insamling av loggar som genereras av Azure Diagnostics. Diagnostikinfrastrukturloggarna är användbara för felsökning av själva diagnostiksystemet. Valfria attribut är:<br /><br /> - **scheduledTransferLogLevelFilter** - Konfigurerar den lägsta allvarlighetsgraden för de insamlade loggarna.<br /><br /> - **scheduledTransferPeriod** - Intervallet mellan schemalagda överföringar till lagring avrundas uppåt till närmaste minut. Värdet är en [XML "Varaktighetsdatatyp".](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  
|**Kataloger**|Se beskrivning någon annanstans på den här sidan.|  
|**EtwProviders (EtwProviders)**|Se beskrivning någon annanstans på den här sidan.|  
|**Mått**|Se beskrivning någon annanstans på den här sidan.|  
|**PerformanceCounters (PerformanceCounters)**|Se beskrivning någon annanstans på den här sidan.|  
|**WindowsEventLog**|Se beskrivning någon annanstans på den här sidan.|
|**DockerSources**|Se beskrivning någon annanstans på den här sidan. |



## <a name="crashdumps-element"></a>CrashDumps Element  
 *Träd: Rot - DiagnostikKonfigurering - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - CrashDumps*

 Aktivera insamling av kraschdumpar.  

|Attribut|Beskrivning|  
|----------------|-----------------|  
|**containerName (containerName)**|Valfri. Namnet på blob-behållaren i ditt Azure Storage-konto som ska användas för att lagra kraschdumpar.|  
|**crashDumpType**|Valfri.  Konfigurerar Azure Diagnostics för att samla in mini- eller fullständiga kraschdumpar.|  
|**katalogQuotaPercentage**|Valfri.  Konfigurerar procentandelen **av den totalaquotaInMB** som ska reserveras för kraschdumpar på den virtuella datorn.|  

|Underordnade element|Beskrivning|  
|--------------------|-----------------|  
|**CrashDumpKonfigurering**|Krävs. Definierar konfigurationsvärden för varje process.<br /><br /> Följande attribut krävs också:<br /><br /> **processName** - Namnet på den process som du vill att Azure Diagnostics ska samla in en kraschdump för.|  

## <a name="directories-element"></a>Katalogelement
 *Träd: Rot - DiagnostikKonfigurering - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Kataloger*

 Aktiverar insamling av innehållet i en katalog, IIS misslyckades åtkomstbegäran loggar och / eller IIS loggar.  

 Valfrit **attributet scheduledTransferPeriod.** Se förklaring tidigare.  

|Underordnade element|Beskrivning|  
|--------------------|-----------------|  
|**IISLogs**|Om du inkluderar det här elementet i konfigurationen kan insamlingen av IIS-loggar:<br /><br /> **containerName** - Namnet på blob-behållaren i ditt Azure Storage-konto som ska användas för att lagra IIS-loggarna.|   
|**MisslyckadeRequestLogs**|Om du inkluderar det här elementet i konfigurationen kan insamling av loggar om misslyckade begäranden till en IIS-plats eller ett IIS-program. Du måste också aktivera spårningsalternativ under **systemet. Webbserver** i **Web.config**.|  
|**Datasources**|En lista över kataloger att övervaka.|




## <a name="datasources-element"></a>DataSources-element  
 *Träd: Rot - DiagnostikKonfigurering - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Kataloger - DataSources*

 En lista över kataloger att övervaka.  

|Underordnade element|Beskrivning|  
|--------------------|-----------------|  
|**Katalogkonfigurering**|Krävs. Obligatoriskt attribut:<br /><br /> **containerName** - Namnet på blob-behållaren i ditt Azure Storage-konto som ska användas för att lagra loggfilerna.|  





## <a name="directoryconfiguration-element"></a>Katalogkonfigurationselement  
 *Träd: Rot - DiagnostikKonfigurering - Offentligkonfigurering - WadCFG - DiagnosticMonitorConfiguration - Kataloger - DataSources - DirectoryConfiguration*

 Kan innehålla antingen **Absolute-** eller **LocalResource-elementet** men inte båda.  

|Underordnade element|Beskrivning|  
|--------------------|-----------------|  
|**Absolut**|Den absoluta sökvägen till katalogen att övervaka. Följande attribut krävs:<br /><br /> - **Sökväg** - Den absoluta sökvägen till katalogen att övervaka.<br /><br /> - **expandEnvironment** - Konfigurerar om miljövariabler i Sökväg expanderas.|  
|**LocalResource**|Sökvägen i förhållande till en lokal resurs att övervaka. Obligatoriska attribut är:<br /><br /> - **Namn** - Den lokala resurs som innehåller katalogen för att övervaka<br /><br /> - **relativePath** - Sökvägen i förhållande till Namn som innehåller katalogen som ska övervakas|  



## <a name="etwproviders-element"></a>EtwProviders-element  
 *Träd: Rot - DiagnostikKonfigurering - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders*

 Konfigurerar insamling av ETW-händelser från EventSource- och/eller ETW Manifest-baserade leverantörer.  

|Underordnade element|Beskrivning|  
|--------------------|-----------------|  
|**EtwEventSourceProviderKonfigurering**|Konfigurerar samling av händelser som genereras från [klassen EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). Obligatoriskt attribut:<br /><br /> **provider** - Klassnamnet för EventSource-händelsen.<br /><br /> Valfria attribut är:<br /><br /> - **scheduledTransferLogLevelFilter** - Den minsta allvarlighetsgraden som ska överföras till ditt lagringskonto.<br /><br /> - **scheduledTransferPeriod** - Intervallet mellan schemalagda överföringar till lagring avrundas uppåt till närmaste minut. Värdet är en [XML "Varaktighetsdatatyp".](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  
|**EtwManifestProviderKonfigurering**|Obligatoriskt attribut:<br /><br /> **provider** - GUID för händelseleverantören<br /><br /> Valfria attribut är:<br /><br /> - **scheduledTransferLogLevelFilter** - Den minsta allvarlighetsgraden som ska överföras till ditt lagringskonto.<br /><br /> - **scheduledTransferPeriod** - Intervallet mellan schemalagda överföringar till lagring avrundas uppåt till närmaste minut. Värdet är en [XML "Varaktighetsdatatyp".](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  



## <a name="etweventsourceproviderconfiguration-element"></a>EtwEventSourceProviderKonfigurationselement  
 *Träd: Rot - DiagnostikKonfigurering - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders- EtwEventSourceProviderConfiguration*

 Konfigurerar samling av händelser som genereras från [klassen EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx).  

|Underordnade element|Beskrivning|  
|--------------------|-----------------|  
|**DefaultEvents**|Valfritt attribut:<br/><br/> **eventDestination** - Namnet på tabellen för att lagra händelserna i|  
|**Händelse**|Obligatoriskt attribut:<br /><br /> **id** - ID för händelsen.<br /><br /> Valfritt attribut:<br /><br /> **eventDestination** - Namnet på tabellen för att lagra händelserna i|  



## <a name="etwmanifestproviderconfiguration-element"></a>EtwManifestProviderKonfigurationselement  
 *Träd: Rot - DiagnostikKonfigurering - Offentligkonfigurering - WadCFG - DiagnosticMonitorConfiguration - EtwProviders - EtwManifestProviderConfiguration*

|Underordnade element|Beskrivning|  
|--------------------|-----------------|  
|**DefaultEvents**|Valfritt attribut:<br /><br /> **eventDestination** - Namnet på tabellen för att lagra händelserna i|  
|**Händelse**|Obligatoriskt attribut:<br /><br /> **id** - ID för händelsen.<br /><br /> Valfritt attribut:<br /><br /> **eventDestination** - Namnet på tabellen för att lagra händelserna i|  



## <a name="metrics-element"></a>Måttelement  
 *Träd: Rot - DiagnostikKonfigurering - Offentligkonfigurering - WadCFG - DiagnosticMonitorConfiguration - Metrics*

 Gör att du kan generera en prestandaräknare tabell som är optimerad för snabba frågor. Varje prestandaräknare som definieras i **performancecounters-elementet** lagras i tabellen Mått utöver tabellen Prestandaräknare.  

 **Attributet resourceId** krävs.  Resurs-ID för den virtuella datorn eller virtual machine-skalningsuppsättningen som du distribuerar Azure Diagnostics till. Hämta **resourceID** från [Azure-portalen](https://portal.azure.com). Välj **Bläddra bland** -> **resursgrupper**  ->  **<namn\>**. Klicka på panelen **Egenskaper** och kopiera värdet från **ID-fältet.**  

|Underordnade element|Beskrivning|  
|--------------------|-----------------|  
|**Mått aggregering**|Obligatoriskt attribut:<br /><br /> **scheduledTransferPeriod** - Intervallet mellan schemalagda överföringar till lagring avrundas uppåt till närmaste minut. Värdet är en [XML "Varaktighetsdatatyp".](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  



## <a name="performancecounters-element"></a>PerformanceCounters-element  
 *Träd: Rot - DiagnostikKonfigurering - Offentligkonfigurering - WadCFG - DiagnosticMonitorConfiguration - PerformanceCounters*

 Aktiverar insamling av prestandaräknare.  

 Valfritt attribut:  

 Valfrit **attributet scheduledTransferPeriod.** Se förklaring tidigare.

|Underordnat element|Beskrivning|  
|-------------------|-----------------|  
|**PerformanceCounterKonfigureration**|Följande attribut krävs:<br /><br /> - **counterSpecifier** - Namnet på prestandaräknaren. Till exempel `\Processor(_Total)\% Processor Time`. Om du vill få en lista över prestandaräknare på värden kör du kommandot `typeperf`.<br /><br /> - **sampleRate** - Hur ofta räknaren ska provtas.<br /><br /> Valfritt attribut:<br /><br /> **enhet** - Enheten för disken. Värden är tillgängliga i [UnitType-klassen](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.sql.models.unittype?view=azure-dotnet) |
|**Sjunker** | Tillagd i 1,5. Valfri. Pekar på en sink-plats för att även skicka diagnostikdata. Till exempel Azure Monitor eller Event Hubs.|    




## <a name="windowseventlog-element"></a>WindowsEventLog-element
 *Träd: Rot - DiagnostikKonfigurering - Offentligkonfigurering - WadCFG - DiagnosticMonitorConfiguration - WindowsEventLog*

 Aktiverar samlingen av Windows-händelseloggar.  

 Valfrit **attributet scheduledTransferPeriod.** Se förklaring tidigare.  

|Underordnat element|Beskrivning|  
|-------------------|-----------------|  
|**Datakälla**|Windows-händelseloggarna som ska samlas in. Obligatoriskt attribut:<br /><br /> **namn** - XPath-frågan som beskriver de windowshändelser som ska samlas in. Ett exempel:<br /><br /> `Application!*[System[(Level <=3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level <= 3)]`<br /><br /> Om du vill samla in alla händelser anger du "*" |
|**Sjunker** | Tillagd i 1,5. Valfri. Pekar på en sink-plats om du också vill skicka diagnostikdata för alla underordnade element som stöder sänkor. Sink exempel är Application Insights eller Event Hubs.|  


## <a name="logs-element"></a>Logelement  
 *Träd: Rot - DiagnostikKonfigurering - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Loggar*

 Finns i version 1.0 och 1.1. Saknas i 1,2. La tillbaka i 1,3.  

 Definierar buffertkonfigurationen för grundläggande Azure-loggar.  

|Attribut|Typ|Beskrivning|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|**unsignedInt**|Valfri. Anger den maximala mängden filsystemlagring som är tillgänglig för de angivna data.<br /><br /> Standardvärdet är 0.|  
|**schemalagdtransferloglevelfilter**|**Sträng**|Valfri. Anger den lägsta allvarlighetsgraden för loggtransaktioner som överförs. Standardvärdet är **Odefinierat**, som överför alla loggar. Andra möjliga värden (i ordning efter mest till minst information) är **Utförlig**, **Information**, **Varning**, **Fel**och **Kritisk**.|  
|**schemalagdtransferperiod**|**Varaktighet**|Valfri. Anger intervallet mellan schemalagda överföringar av data, avrundade uppåt till närmaste minut.<br /><br /> Standard är PT0S.|  
|**Sjunker** |**Sträng**| Tillagd i 1,5. Valfri. Pekar på en sink-plats för att även skicka diagnostikdata. Till exempel Application Insights eller Event Hubs.|  

## <a name="dockersources"></a>DockerSources
 *Träd: Rot - DiagnostikKonfigurering - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - DockerSources*

 Tillagd i 1,9.

|Elementnamn|Beskrivning|  
|------------------|-----------------|  
|**Statistik**|Talar om för systemet att samla in statistik för Docker-containrar|  

## <a name="sinksconfig-element"></a>SinksConfig Element  
 *Träd: Root - DiagnostikKonfigurering - PublicConfig - WadCFG - SinksConfig*

 En lista över platser som ska skickas diagnostikdata till och den konfiguration som är associerad med dessa platser.  

|Elementnamn|Beskrivning|  
|------------------|-----------------|  
|**Kanalmottagare**|Se beskrivning någon annanstans på den här sidan.|  

## <a name="sink-element"></a>Sink Element
 *Träd: Rot - DiagnostikKonfigurering - PublicConfig - WadCFG - SinksConfig - Sink*

 Tillagd i version 1.5.  

 Definierar platser att skicka diagnostikdata till. Till exempel tjänsten Application Insights.  

|Attribut|Typ|Beskrivning|  
|---------------|----------|-----------------|  
|**Namn**|sträng|En sträng som identifierar sinkname.|  

|Element|Typ|Beskrivning|  
|-------------|----------|-----------------|  
|**Application Insights**|sträng|Används endast när data skickas till Application Insights. Innehåller instrumenteringsnyckeln för ett aktivt Application Insights-konto som du har åtkomst till.|  
|**Kanaler**|sträng|En för varje ytterligare filtrering som strömmar som du|  

## <a name="channels-element"></a>Element för kanaler  
 *Träd: Root - DiagnostikKonfiguration - PublicConfig - WadCFG - SinksConfig - Sink - Kanaler*

 Tillagd i version 1.5.  

 Definierar filter för strömmar av loggdata som passerar genom en diskho.  

|Element|Typ|Beskrivning|  
|-------------|----------|-----------------|  
|**Kanal**|sträng|Se beskrivning någon annanstans på den här sidan.|  

## <a name="channel-element"></a>Kanalelement
 *Träd: Rot - DiagnostikKonfigurering - PublicConfig - WadCFG - SinksConfig - Sink - Kanaler - Kanal*

 Tillagd i version 1.5.  

 Definierar platser att skicka diagnostikdata till. Till exempel tjänsten Application Insights.  

|Attribut|Typ|Beskrivning|  
|----------------|----------|-----------------|  
|**logLevel**|**Sträng**|Anger den lägsta allvarlighetsgraden för loggtransaktioner som överförs. Standardvärdet är **Odefinierat**, som överför alla loggar. Andra möjliga värden (i ordning efter mest till minst information) är **Utförlig**, **Information**, **Varning**, **Fel**och **Kritisk**.|  
|**Namn**|**Sträng**|Ett unikt namn på kanalen som ska referera till|  


## <a name="privateconfig-element"></a>PrivateConfig Element
 *Träd: Root - DiagnostikKonfigurering - PrivateConfig*

 Tillagd i version 1.3.  

 Valfri  

 Lagrar den privata informationen för lagringskontot (namn, nyckel och slutpunkt). Den här informationen skickas till den virtuella datorn, men kan inte hämtas från den.  

|Underordnade element|Beskrivning|  
|--------------------|-----------------|  
|**StorageAccount**|Det lagringskonto som ska användas. Följande attribut krävs<br /><br /> - **namn** - Namnet på lagringskontot.<br /><br /> - **nyckel** - Nyckeln till lagringskontot.<br /><br /> - **slutpunkt** - Slutpunkten för åtkomst till lagringskontot. <br /><br /> -**sasToken** (added 1.8.1)- Du kan ange en SAS-token i stället för en lagringskontonyckel i den privata konfigurationen. Om det anges ignoreras lagringskontonyckeln. <br />Krav för SAS-token: <br />- Stöder endast konto-SAS-token <br />- *b,* *t-tjänsttyper* krävs. <br /> - *a*, *c*, *u*, *w* behörigheter krävs. <br /> - *c,* *o* resurstyper krävs. <br /> - Stöder endast HTTPS-protokollet <br /> - Start- och utgångstid måste vara giltig.|  


## <a name="isenabled-element"></a>IsEnabled Element  
 *Träd: Root - DiagnostikKonfigurering - IsEnabled*

 Boolean. Används `true` för att aktivera `false` diagnostiken eller inaktivera diagnostiken.

## <a name="example-configuration"></a>Exempelkonfiguration
 Nedan följer en fullständig exempelkonfiguration för Windows diagnostiktillägg som visas i både JSON och XML.

 
### <a name="json"></a>JSON

*PublicConfig* och *PrivateConfig* separeras eftersom de i de flesta JSON-användningsfall skickas som olika variabler. Dessa ärenden omfattar Resource Manager-mallar, PowerShell och Visual Studio.

> [!NOTE]
> Den offentliga config Azure Monitor sink-definitionen har två egenskaper, *resourceId* och *region*. Dessa krävs endast för klassiska virtuella datorer och klassiska molntjänster. Dessa egenskaper bör inte användas för andra resurser.

```json
"PublicConfig" {
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 10000,
            "DiagnosticInfrastructureLogs": {
                "scheduledTransferLogLevelFilter": "Error"
            },
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "sinks": "AzureMonitorSink",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT1M",
                        "unit": "percent"
                    }
                ]
            },
            "Directories": {
                "scheduledTransferPeriod": "PT5M",
                "IISLogs": {
                    "containerName": "iislogs"
                },
                "FailedRequestLogs": {
                    "containerName": "iisfailed"
                },
                "DataSources": [
                    {
                        "containerName": "mynewprocess",
                        "Absolute": {
                            "path": "C:\\MyNewProcess",
                            "expandEnvironment": false
                        }
                    },
                    {
                        "containerName": "badapp",
                        "Absolute": {
                            "path": "%SYSTEMDRIVE%\\BadApp",
                            "expandEnvironment": true
                        }
                    },
                    {
                        "containerName": "goodapp",
                        "LocalResource": {
                            "relativePath": "..\\PeanutButter",
                            "name": "Skippy"
                        }
                    }
                ]
            },
            "EtwProviders": {
                "sinks": "",
                "EtwEventSourceProviderConfiguration": [
                    {
                        "scheduledTransferPeriod": "PT5M",
                        "provider": "MyProviderClass",
                        "Event": [
                            {
                                "id": 0
                            },
                            {
                                "id": 1,
                                "eventDestination": "errorTable"
                            }
                        ],
                        "DefaultEvents": {
                        }
                    }
                ],
                "EtwManifestProviderConfiguration": [
                    {
                        "scheduledTransferPeriod": "PT2M",
                        "scheduledTransferLogLevelFilter": "Information",
                        "provider": "5974b00b-84c2-44bc-9e58-3a2451b4e3ad",
                        "Event": [
                            {
                                "id": 0
                            }
                        ],
                        "DefaultEvents": {
                        }
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT5M",
                "DataSource": [
                    {
                        "name": "System!*[System[Provider[@Name='Microsoft Antimalware']]]"
                    },
                    {
                        "name": "System!*[System[Provider[@Name='NTFS'] and (EventID=55)]]"
                    },
                    {
                        "name": "System!*[System[Provider[@Name='disk'] and (EventID=7 or EventID=52 or EventID=55)]]"
                    }
                ]
            },
            "Logs": {
                "scheduledTransferPeriod": "PT1M",
                "scheduledTransferLogLevelFilter": "Verbose",
                "sinks": "ApplicationInsights.AppLogs"
            },
            "CrashDumps": {
                "directoryQuotaPercentage": 30,
                "dumpType": "Mini",
                "containerName": "wad-crashdumps",
                "CrashDumpConfiguration": [
                    {
                        "processName": "mynewprocess.exe"
                    },
                    {
                        "processName": "badapp.exe"
                    }
                ]
            }
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "AzureMonitorSink",
                    "AzureMonitor":
                    {
                        "ResourceId": "{insert resourceId if a classic VM or cloud service, else property not needed}",
                        "Region": "{insert Azure region of resource if a classic VM or cloud service, else property not needed}"
                    }
                },
                {
                    "name": "ApplicationInsights",
                    "ApplicationInsights": "{Insert InstrumentationKey}",
                    "Channels": {
                        "Channel": [
                            {
                                "logLevel": "Error",
                                "name": "Errors"
                            },
                            {
                                "logLevel": "Verbose",
                                "name": "AppLogs"
                            }
                        ]
                    }
                },
                {
                    "name": "EventHub",
                    "EventHub": {
                        "Url": "https://myeventhub-ns.servicebus.windows.net/diageventhub",
                        "SharedAccessKeyName": "SendRule",
                        "usePublisherId": false
                    }
                },
                {
                    "name": "secondaryEventHub",
                    "EventHub": {
                        "Url": "https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub",
                        "SharedAccessKeyName": "SendRule",
                        "usePublisherId": false
                    }
                },
                {
                    "name": "secondaryStorageAccount",
                    "StorageAccount": {
                        "name": "secondarydiagstorageaccount",
                        "endpoint": "https://core.windows.net"
                    }
                }
            ]
        }
    },
    "StorageAccount": "diagstorageaccount",
    "StorageType": "TableAndBlob"
}
```

> [!NOTE]
> Den privata config Azure Monitor sink-definitionen har två egenskaper, *PrincipalId* och *Secret*. Dessa krävs endast för klassiska virtuella datorer och klassiska molntjänster. Dessa egenskaper bör inte användas för andra resurser.


```json
"PrivateConfig" {
    "storageAccountName": "diagstorageaccount",
    "storageAccountKey": "{base64 encoded key}",
    "storageAccountEndPoint": "https://core.windows.net",
    "storageAccountSasToken": "{sas token}",
    "EventHub": {
        "Url": "https://myeventhub-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    },
    "AzureMonitorAccount": {
        "ServicePrincipalMeta": {
            "PrincipalId": "{Insert service principal client Id}",
            "Secret": "{Insert service principal client secret}"
        }
    },
    "SecondaryStorageAccounts": {
        "StorageAccount": [
            {
                "name": "secondarydiagstorageaccount",
                "key": "{base64 encoded key}",
                "endpoint": "https://core.windows.net",
                "sasToken": "{sas token}"
            }
        ]
    },
    "SecondaryEventHubs": {
        "EventHub": [
            {
                "Url": "https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub",
                "SharedAccessKeyName": "SendRule",
                "SharedAccessKey": "{base64 encoded key}"
            }
        ]
    }
}

```

### <a name="xml"></a>XML

```xml  
<?xml version="1.0" encoding="utf-8"?>  
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">   
  <PublicConfig>  
    <WadCfg>  
      <DiagnosticMonitorConfiguration overallQuotaInMB="10000">  

        <PerformanceCounters scheduledTransferPeriod="PT1M", sinks="AzureMonitorSink">  
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />  
        </PerformanceCounters>  

        <Directories scheduledTransferPeriod="PT5M">  
          <IISLogs containerName="iislogs" />  
          <FailedRequestLogs containerName="iisfailed" />  

          <DataSources>  
            <DirectoryConfiguration containerName="mynewprocess">  
              <Absolute path="C:\MyNewProcess" expandEnvironment="false" />  
            </DirectoryConfiguration>  
            <DirectoryConfiguration containerName="badapp">  
              <Absolute path="%SYSTEMDRIVE%\BadApp" expandEnvironment="true" />  
            </DirectoryConfiguration>  
            <DirectoryConfiguration containerName="goodapp">  
              <LocalResource name="Skippy" relativePath="..\PeanutButter"/>  
            </DirectoryConfiguration>  
          </DataSources>  

        </Directories>  

        <EtwProviders>  
          <EtwEventSourceProviderConfiguration   
                       provider="MyProviderClass"   
                       scheduledTransferPeriod="PT5M">  
            <Event id="0"/>  
            <Event id="1" eventDestination="errorTable"/>  
            <DefaultEvents />  
          </EtwEventSourceProviderConfiguration>  
          <EtwManifestProviderConfiguration provider="5974b00b-84c2-44bc-9e58-3a2451b4e3ad" scheduledTransferLogLevelFilter="Information" scheduledTransferPeriod="PT2M">  
            <Event id="0"/>  
            <DefaultEvents eventDestination="defaultTable"/>  
          </EtwManifestProviderConfiguration>  
        </EtwProviders>  

        <WindowsEventLog scheduledTransferPeriod="PT5M">  
          <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>  
          <DataSource name="System!*[System[Provider[@Name='NTFS'] and (EventID=55)]]" />  
          <DataSource name="System!*[System[Provider[@Name='disk'] and (EventID=7 or EventID=52 or EventID=55)]]" />  
        </WindowsEventLog>  

        <Logs  bufferQuotaInMB="1024"   
             scheduledTransferPeriod="PT1M"   
             scheduledTransferLogLevelFilter="Verbose"   
             sinks="ApplicationInsights.AppLogs"/>  <!-- sinks attribute added in 1.5 -->  

        <CrashDumps containerName="wad-crashdumps" directoryQuotaPercentage="30" dumpType="Mini">  
          <CrashDumpConfiguration processName="mynewprocess.exe" />  
          <CrashDumpConfiguration processName="badapp.exe"/>  
        </CrashDumps>  

        <DockerSources> <!-- Added in 1.9 -->
          <Stats enabled="true" sampleRate="PT1M" scheduledTransferPeriod="PT1M" />
        </DockerSources>

      </DiagnosticMonitorConfiguration>  

      <SinksConfig>   <!-- Added in 1.5 -->  
        <Sink name="AzureMonitorSink">
            <AzureMonitor> <!-- Added in 1.11 -->
                <resourceId>{insert resourceId}</ResourceId> <!-- Parameter only needed for classic VMs and Classic Cloud Services, exclude VMSS and Resource Manager VMs-->
                <Region>{insert Azure region of resource}</Region> <!-- Parameter only needed for classic VMs and Classic Cloud Services, exclude VMSS and Resource Manager VMs -->
            </AzureMonitor>
        </Sink>
        <Sink name="ApplicationInsights">   
          <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>   
          <Channels>   
            <Channel logLevel="Error" name="Errors"  />   
            <Channel logLevel="Verbose" name="AppLogs"  />   
          </Channels>   
        </Sink>   
        <Sink name="EventHub"> <!-- Added in 1.7 -->
          <EventHub Url="https://myeventhub-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" usePublisherId="false" />
        </Sink>
        <Sink name="secondaryEventHub"> <!-- Added in 1.7 -->
          <EventHub Url="https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub" SharedAccessKeyName="SendRule" usePublisherId="false" />
        </Sink>
        <Sink name="secondaryStorageAccount"> <!-- Added in 1.7 -->
          <StorageAccount name="secondarydiagstorageaccount" endpoint="https://core.windows.net" />
        </Sink>
   </SinksConfig>

  </WadCfg>  

  <StorageAccount>diagstorageaccount</StorageAccount>
  <StorageType>TableAndBlob</StorageType> <!-- Added in 1.8 -->  
  </PublicConfig>  

  <PrivateConfig>  <!-- Added in 1.3 -->  
    <StorageAccount name="" key="" endpoint="" sasToken="{sas token}"  />  <!-- sasToken in Private config added in 1.8.1 -->  
    <EventHub Url="https://myeventhub-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />

    <AzureMonitorAccount>
        <ServicePrincipalMeta> <!-- Added in 1.11; only needed for classic VMs and Classic cloud services -->
            <PrincipalId>{Insert service principal clientId}</PrincipalId>
            <Secret>{Insert service principal client secret}</Secret>
        </ServicePrincipalMeta>
    </AzureMonitorAccount>

    <SecondaryStorageAccounts>
       <StorageAccount name="secondarydiagstorageaccount" key="{base64 encoded key}" endpoint="https://core.windows.net" sasToken="{sas token}" />
    </SecondaryStorageAccounts>

    <SecondaryEventHubs>
       <EventHub Url="https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />
    </SecondaryEventHubs>

  </PrivateConfig>  
  <IsEnabled>true</IsEnabled>  
</DiagnosticsConfiguration>  

```  
> [!NOTE]
> Den offentliga config Azure Monitor sink definition har två egenskaper, resourceId och region. Dessa krävs endast för klassiska virtuella datorer och klassiska molntjänster. Dessa egenskaper bör inte användas för virtuella resurser eller virtuella datorskaleuppsättningar.
> Det finns också ytterligare ett privat Config-element för Azure Monitor-diskbänken, som passerar i ett huvud-ID och Hemligt. Detta krävs endast för klassiska virtuella datorer och klassiska molntjänster. För virtuella datorer i Resource Manager och VMSS kan Azure Monitor-definitionen i det privata konfigurationselementet uteslutas.
>
