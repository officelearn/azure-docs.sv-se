---
title: Azure Diagnostics-tillägg 1.3 och senare konfigurationsschema
description: Schemaversion 1.3 och senare Azure-diagnostik levereras som en del av Microsoft Azure SDK 2.4 och senare.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: reference
ms.date: 09/20/2018
ms.author: robb
ms.subservice: diagnostic-extension
ms.openlocfilehash: fa03017c35c76d986139eeee00eea8a9b4a00e62
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/11/2019
ms.locfileid: "59497091"
---
# <a name="azure-diagnostics-13-and-later-configuration-schema"></a>Azure Diagnostics 1.3 och senare konfigurationsschema
> [!NOTE]
> Azure Diagnostics-tillägget är den komponent som används för att samla in prestandaräknare och annan statistik från:
> - Azure Virtual Machines
> - Virtual Machine Scale Sets
> - Service Fabric
> - Cloud Services
> - Nätverkssäkerhetsgrupper
>
> Den här sidan gäller endast om du använder någon av dessa tjänster.

Den här sidan är giltig för versioner 1.3 och senare (Azure SDK 2.4 och nyare). Nyare konfigurationsavsnitt kommenterade ska visas i vilken version de har lagts till.  

Konfigurationsfilen som beskrivs här används för att ange inställningar för diagnostik när diagnostik monitor startar.  

Tillägget används tillsammans med andra produkter som Azure Monitor, som innehåller Application Insights och Log Analytics-diagnostik.



Hämta offentliga konfiguration filen schemadefinitionen genom att köra följande PowerShell-kommando:  

```powershell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  

Läs mer om hur du använder Azure Diagnostics [Azure Diagnostics-tillägget](diagnostics-extension-overview.md).  

## <a name="example-of-the-diagnostics-configuration-file"></a>Exempel på konfigurationsfilen diagnostik  
 I följande exempel visas en typisk diagnostik konfigurationsfil:  

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
> Offentliga konfig definition för Azure Monitor-mottagare har två egenskaper, resourceId och region. Detta är endast krävs för klassiska virtuella datorer och klassiskt molntjänster. De här egenskaperna ska inte användas för Resource Manager Virtual Machines eller skalningsuppsättningar för virtuella datorer.
> Det finns också ett ytterligare privata Config-element för Azure Monitor-mellanlagringsplatsen, som skickas i ett huvudkonto-Id och hemlighet. Detta är endast krävs för klassiska virtuella datorer och klassiskt molntjänster. För Resource Manager virtuella datorer och VMSS i Azure Monitor kan-definitionen i det privata config-elementet undantas.
>

JSON motsvarande tidigare XML-konfigurationsfilen.

PublicConfig och PrivateConfig är åtskilda eftersom json användning oftast de skickas som olika variabler. Dessa objekt omfattar ofta Resource Manager-mallar, VM-skalningsuppsättningen PowerShell och Visual Studio.

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
> Offentliga konfig definition för Azure Monitor-mottagare har två egenskaper, resourceId och region. Detta är endast krävs för klassiska virtuella datorer och klassiskt molntjänster.
> De här egenskaperna ska inte användas för Resource Manager Virtual Machines eller skalningsuppsättningar för virtuella datorer.
>

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

> [!NOTE]
> Det finns en ytterligare privata Config-element för Azure Monitor-mellanlagringsplatsen, som skickas i ett huvudkonto-Id och hemlighet. Detta är endast krävs för klassiska virtuella datorer och klassiskt molntjänster. För Resource Manager virtuella datorer och VMSS i Azure Monitor kan-definitionen i det privata config-elementet undantas.
>


## <a name="reading-this-page"></a>Läsa den här sidan  
 De taggar som följer är ungefär i ordning som visas i föregående exempel.  Om du inte ser en fullständig beskrivning där du förväntar dig, söksida för elementet eller attributet.  

## <a name="common-attribute-types"></a>Vanliga attributtyper  
 **scheduledTransferPeriod** attributet visas i flera element. Det är intervallet mellan schemalagda överföringar till lagringen avrundas uppåt till närmaste minut. Värdet är en [XML ”varaktighet datatyp”.](https://www.w3schools.com/xml/schema_dtypes_date.asp)


## <a name="diagnosticsconfiguration-element"></a>DiagnosticsConfiguration Element  
 *Trädet: Rot - DiagnosticsConfiguration*

Har lagts till i version 1.3.  

Det översta elementet i konfigurationsfilen för diagnostik.  

**Attributet** xmlns - XML-namnområdet för diagnostik-konfigurationsfilen är:  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  


|Underordnade element|Beskrivning|  
|--------------------|-----------------|  
|**PublicConfig**|Krävs. Se beskrivning någon annanstans på den här sidan.|  
|**PrivateConfig**|Valfri. Se beskrivning någon annanstans på den här sidan.|  
|**IsEnabled**|Booleskt värde. Se beskrivning någon annanstans på den här sidan.|  

## <a name="publicconfig-element"></a>PublicConfig Element  
 *Trädet: Rot - DiagnosticsConfiguration - PublicConfig*

 Beskriver offentliga diagnostikkonfigurationen.  

|Underordnade element|Beskrivning|  
|--------------------|-----------------|  
|**WadCfg**|Krävs. Se beskrivning någon annanstans på den här sidan.|  
|**StorageAccount**|Namnet på Azure Storage-konto för att lagra data i. Kan också anges som en parameter när du kör cmdleten Set-AzureServiceDiagnosticsExtension.|  
|**StorageType**|Kan vara *tabell*, *Blob*, eller *TableAndBlob*. Tabellen är standard. När du har valt TableAndBlob skrivs diagnostiska data två gånger – en gång till varje typ av.|  
|**LocalResourceDirectory**|Katalogen på den virtuella datorn där Monitoring Agent lagrar händelsedata. Om du inte har angetts standardkatalogen:<br /><br /> För en Worker/web-roll: `C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> För en virtuell dator: `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> Obligatoriska attribut är:<br /><br /> - **sökvägen** -katalogen på system som ska användas av Azure Diagnostics.<br /><br /> - **expandEnvironment** – styr om miljövariabler expanderas i Sökvägens namn.|  

## <a name="wadcfg-element"></a>WadCFG Element  
 *Trädet: Root - DiagnosticsConfiguration - PublicConfig - WadCFG*

 Identifierar och konfigurerar telemetridata som ska samlas in.  


## <a name="diagnosticmonitorconfiguration-element"></a>DiagnosticMonitorConfiguration Element
 *Trädet: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration*

 Krävs

|Attribut|Beskrivning|  
|----------------|-----------------|  
| **overallQuotaInMB** | Längsta lokalt diskutrymme som kan användas av de olika typerna av diagnostiska data som samlas in av Azure Diagnostics. Standardinställningen är 4 096 MB.<br />
|**useProxyServer** | Konfigurera Azure Diagnostics-data om du vill använda inställningarna för proxyservern som angetts i Internet Explorer-inställningarna.|
|**mottagare** | Har lagts till i 1.5. Valfri. Pekar på en plats för mottagaren att även skicka diagnostikdata för alla underordnade element som har stöd för mottagare. Mottagare exempel är Application Insights eller Event Hubs.|  


<br /> <br />

|Underordnade element|Beskrivning|  
|--------------------|-----------------|  
|**CrashDumps**|Se beskrivning någon annanstans på den här sidan.|  
|**DiagnosticInfrastructureLogs**|Aktivera insamling av loggar som genereras av Azure Diagnostics. Diagnostic infrastructure-loggar är användbara vid felsökning av själva systemet diagnostik. Valfritt attribut är:<br /><br /> - **scheduledTransferLogLevelFilter** – konfigurerar den lägsta allvarlighetsgraden för loggarna som samlas in.<br /><br /> - **scheduledTransferPeriod** -intervallet mellan schemalagda överföringar till lagringen avrundas uppåt till närmaste minut. Värdet är en [XML ”varaktighet datatyp”.](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  
|**Kataloger**|Se beskrivning någon annanstans på den här sidan.|  
|**EtwProviders**|Se beskrivning någon annanstans på den här sidan.|  
|**Mått**|Se beskrivning någon annanstans på den här sidan.|  
|**PerformanceCounters**|Se beskrivning någon annanstans på den här sidan.|  
|**WindowsEventLog**|Se beskrivning någon annanstans på den här sidan.|
|**DockerSources**|Se beskrivning någon annanstans på den här sidan. |



## <a name="crashdumps-element"></a>CrashDumps Element  
 *Trädet: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - CrashDumps*

 Aktivera insamling av kraschdumpar.  

|Attribut|Beskrivning|  
|----------------|-----------------|  
|**containerName**|Valfri. Namnet på blobbehållaren i ditt Azure Storage-konto som används för att lagra kraschdumpar.|  
|**crashDumpType**|Valfri.  Konfigurerar Azure-diagnostik för att samla in Dumpar mini eller fullständig krasch.|  
|**directoryQuotaPercentage**|Valfri.  Konfigurerar procentandelen **overallQuotaInMB** som ska reserveras för kraschdumpar på den virtuella datorn.|  

|Underordnade element|Beskrivning|  
|--------------------|-----------------|  
|**CrashDumpConfiguration**|Krävs. Definierar konfigurationsvärden för varje process.<br /><br /> Det krävs också följande attribut:<br /><br /> **processName** -namnet på processen som du vill att Azure-diagnostik för att samla in en kraschdumpfil för.|  

## <a name="directories-element"></a>Directories Element
 *Trädet: Rot - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - kataloger*

 Aktiverar insamlingen av innehållet i en katalog, IIS misslyckades åtkomstloggar för begäran och/eller IIS-loggar.  

 Valfritt **scheduledTransferPeriod** attribut. Se tidigare förklaring.  

|Underordnade element|Beskrivning|  
|--------------------|-----------------|  
|**IISLogs**|Inkludera det här elementet i konfigurationen aktiverar insamlingen av IIS-loggar:<br /><br /> **containerName** -namnet på blobbehållaren i ditt Azure Storage-konto som används för att lagra IIS-loggar.|   
|**FailedRequestLogs**|Inkludera det här elementet i konfigurationen kan loggsamlingar om misslyckade förfrågningar till ett IIS-webbplats eller ett program. Du måste även aktivera spårningsalternativ under **system. Webbserver** i **Web.config**.|  
|**Datakällor**|En lista över kataloger som ska övervaka.|




## <a name="datasources-element"></a>DataSources Element  
 *Trädet: Rot - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - kataloger - datakällor*

 En lista över kataloger som ska övervaka.  

|Underordnade element|Beskrivning|  
|--------------------|-----------------|  
|**DirectoryConfiguration**|Krävs. Obligatoriskt attribut:<br /><br /> **containerName** -namnet på blob-behållare i Azure Storage-konto som ska användas för att lagra loggfilerna.|  





## <a name="directoryconfiguration-element"></a>DirectoryConfiguration Element  
 *Trädet: Rot - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - kataloger - datakällor - DirectoryConfiguration*

 Kan innehålla antingen den **absolut** eller **LocalResource** element, men inte båda.  

|Underordnade element|Beskrivning|  
|--------------------|-----------------|  
|**Absolut**|Den absoluta sökvägen till katalogen som ska övervakas. Det krävs följande attribut:<br /><br /> - **Sökvägen** -den absoluta sökvägen till katalogen som ska övervakas.<br /><br /> - **expandEnvironment** – konfigurerar om miljövariabler i sökvägen expanderas.|  
|**LocalResource**|Sökväg i förhållande till en lokal resurs du övervakar. Obligatoriska attribut är:<br /><br /> - **Namn på** – den lokala resursen som innehåller katalogen som ska övervakas<br /><br /> - **relativePath** -sökväg i förhållande till namn som innehåller katalogen som ska övervakas|  



## <a name="etwproviders-element"></a>EtwProviders Element  
 *Trädet: Rot - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders*

 Konfigurerar insamling av ETW-händelser från EventSource och/eller ETW Manifest baserade providers.  

|Underordnade element|Beskrivning|  
|--------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|Konfigurerar insamling av händelser som genereras från [EventSource klass](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). Obligatoriskt attribut:<br /><br /> **Providern** -klassnamnet händelsens EventSource.<br /><br /> Valfritt attribut är:<br /><br /> - **scheduledTransferLogLevelFilter** -den lägsta allvarlighetsgraden att överföra till ditt lagringskonto.<br /><br /> - **scheduledTransferPeriod** -intervallet mellan schemalagda överföringar till lagringen avrundas uppåt till närmaste minut. Värdet är en [XML ”varaktighet datatyp”.](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  
|**EtwManifestProviderConfiguration**|Obligatoriskt attribut:<br /><br /> **Providern** -GUID för händelseprovider<br /><br /> Valfritt attribut är:<br /><br /> - **scheduledTransferLogLevelFilter** -den lägsta allvarlighetsgraden att överföra till ditt lagringskonto.<br /><br /> - **scheduledTransferPeriod** -intervallet mellan schemalagda överföringar till lagringen avrundas uppåt till närmaste minut. Värdet är en [XML ”varaktighet datatyp”.](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  



## <a name="etweventsourceproviderconfiguration-element"></a>EtwEventSourceProviderConfiguration Element  
 *Trädet: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders- EtwEventSourceProviderConfiguration*

 Konfigurerar insamling av händelser som genereras från [EventSource klass](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx).  

|Underordnade element|Beskrivning|  
|--------------------|-----------------|  
|**DefaultEvents**|Valfritt attribut:<br/><br/> **eventDestination** -namnet på tabellen för att lagra händelser i|  
|**Händelse**|Obligatoriskt attribut:<br /><br /> **ID** -id för händelsen.<br /><br /> Valfritt attribut:<br /><br /> **eventDestination** -namnet på tabellen för att lagra händelser i|  



## <a name="etwmanifestproviderconfiguration-element"></a>EtwManifestProviderConfiguration Element  
 *Trädet: Rot - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders - EtwManifestProviderConfiguration*

|Underordnade element|Beskrivning|  
|--------------------|-----------------|  
|**DefaultEvents**|Valfritt attribut:<br /><br /> **eventDestination** -namnet på tabellen för att lagra händelser i|  
|**Händelse**|Obligatoriskt attribut:<br /><br /> **ID** -id för händelsen.<br /><br /> Valfritt attribut:<br /><br /> **eventDestination** -namnet på tabellen för att lagra händelser i|  



## <a name="metrics-element"></a>Metrics Element  
 *Trädet: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Metrics*

 Kan du skapa en tabell med prestandaräknaren som är optimerad för snabba frågor. Varje prestandaräknare som definieras i den **PerformanceCounters** elementet lagras i tabellen mått förutom tabellen prestandaräknaren.  

 Den **resourceId** attributet är obligatoriskt.  Resurs-ID för den virtuella datorn eller Virtual Machine Scale Sets du distribuerar Azure Diagnostics-data till. Hämta den **resourceID** från den [Azure-portalen](https://portal.azure.com). Välj **Bläddra** -> **resursgrupper** -> **< namn\>**. Klicka på den **egenskaper** panelen och kopiera värdet från den **ID** fält.  

|Underordnade element|Beskrivning|  
|--------------------|-----------------|  
|**MetricAggregation**|Obligatoriskt attribut:<br /><br /> **scheduledTransferPeriod** -intervallet mellan schemalagda överföringar till lagringen avrundas uppåt till närmaste minut. Värdet är en [XML ”varaktighet datatyp”.](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  



## <a name="performancecounters-element"></a>PerformanceCounters Element  
 *Trädet: Rot - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - PerformanceCounters*

 Aktiverar insamling av prestandaräknare.  

 Valfritt attribut:  

 Valfritt **scheduledTransferPeriod** attribut. Se tidigare förklaring.

|Child Element|Beskrivning|  
|-------------------|-----------------|  
|**PerformanceCounterConfiguration**|Det krävs följande attribut:<br /><br /> - **counterSpecifier** -namnet på prestandaräknaren. Till exempel `\Processor(_Total)\% Processor Time`. Om du vill hämta en lista över prestandaräknare på din värd kör du kommandot `typeperf`.<br /><br /> - **sampleRate** -hur ofta räknaren ska aktiveras.<br /><br /> Valfritt attribut:<br /><br /> **enhet** -måttenhet för räknaren.|
|**mottagare** | Har lagts till i 1.5. Valfri. Pekar på en plats för mottagaren att också skicka diagnostikdata. Azure Monitor eller Event Hubs.|    




## <a name="windowseventlog-element"></a>WindowsEventLog Element
 *Trädet: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - WindowsEventLog*

 Aktiverar insamlingen av Windows-händelseloggar.  

 Valfritt **scheduledTransferPeriod** attribut. Se tidigare förklaring.  

|Child Element|Beskrivning|  
|-------------------|-----------------|  
|**DataSource**|Windows-händelseloggar att samla in. Obligatoriskt attribut:<br /><br /> **namn på** – XPath-frågan som beskriver windows-händelser som ska samlas in. Exempel:<br /><br /> `Application!*[System[(Level <=3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level <= 3)]`<br /><br /> Om du vill samla in alla händelser, ange ”*”|  




## <a name="logs-element"></a>Loggar Element  
 *Trädet: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Logs*

 Presentera i version 1.0 och 1.1. Saknas i 1.2. Har lagts till i 1.3.  

 Definierar konfigurationen buffert för grundläggande Azure loggar.  

|Attribut|Typ|Beskrivning|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|**unsignedInt**|Valfri. Anger den maximala mängden lagringsutrymme för filsystem som är tillgänglig för angivna data.<br /><br /> Standardvärdet är 0.|  
|**scheduledTransferLogLevelFilter**|**sträng**|Valfri. Anger den lägsta allvarlighetsgraden för loggposter som överförs. Standardvärdet är **Undefined**, som överför alla loggar. Andra möjliga värden (i ordningen för de flesta till minst information) är **utförlig**, **Information**, **varning**, **fel**, och **Kritiska**.|  
|**scheduledTransferPeriod**|**varaktighet**|Valfri. Anger intervallet mellan schemalagda överföring av data, avrundat uppåt till närmaste minut.<br /><br /> Standardvärdet är PT0S.|  
|**mottagare** |**sträng**| Har lagts till i 1.5. Valfri. Pekar på en plats för mottagaren att också skicka diagnostikdata. Till exempel Application Insights eller Event Hubs.|  

## <a name="dockersources"></a>DockerSources
 *Trädet: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - DockerSources*

 Har lagts till i 1.9.

|Elementnamn|Beskrivning|  
|------------------|-----------------|  
|**Statistik**|Anger att samla in statistik för Docker-behållare|  

## <a name="sinksconfig-element"></a>SinksConfig Element  
 *Trädet: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig*

 En lista över platser för att skicka diagnostikdata till och den konfiguration som associeras med dessa platser.  

|Elementnamn|Beskrivning|  
|------------------|-----------------|  
|**Kanalmottagare**|Se beskrivning någon annanstans på den här sidan.|  

## <a name="sink-element"></a>Sink Element
 *Trädet: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig - Sink*

 Har lagts till i version 1.5.  

 Definierar platser för att skicka diagnostikdata till. Till exempel Application Insights-tjänsten.  

|Attribut|Typ|Beskrivning|  
|---------------|----------|-----------------|  
|**namn**|sträng|En sträng som identifierar sinkname.|  

|Element|Typ|Beskrivning|  
|-------------|----------|-----------------|  
|**Application Insights**|sträng|Används endast när data skickas till Application Insights. Innehåller Instrumenteringsnyckeln för ett aktivt Application Insights-konto som du har åtkomst till.|  
|**Kanaler**|sträng|En för varje ytterligare filtrering som strömmar som du|  

## <a name="channels-element"></a>Channels Element  
 *Trädet: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig - Sink - Channels*

 Har lagts till i version 1.5.  

 Definierar filter för strömmar av loggdata som passerar genom en mottagare.  

|Element|Typ|Beskrivning|  
|-------------|----------|-----------------|  
|**Kanal**|sträng|Se beskrivning någon annanstans på den här sidan.|  

## <a name="channel-element"></a>Channel Element
 *Trädet: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig - Sink - Channels - Channel*

 Har lagts till i version 1.5.  

 Definierar platser för att skicka diagnostikdata till. Till exempel Application Insights-tjänsten.  

|Attribut|Typ|Beskrivning|  
|----------------|----------|-----------------|  
|**logLevel**|**sträng**|Anger den lägsta allvarlighetsgraden för loggposter som överförs. Standardvärdet är **Undefined**, som överför alla loggar. Andra möjliga värden (i ordningen för de flesta till minst information) är **utförlig**, **Information**, **varning**, **fel**, och **Kritiska**.|  
|**namn**|**sträng**|Ett unikt namn för kanalen att referera till|  


## <a name="privateconfig-element"></a>PrivateConfig Element
 *Trädet: Rot - DiagnosticsConfiguration - PrivateConfig*

 Har lagts till i version 1.3.  

 Valfri  

 Lagrar privat information om storage-konto (namn, nyckel och slutpunkt). Den här informationen skickas till den virtuella datorn, men kan inte hämtas från den.  

|Underordnade element|Beskrivning|  
|--------------------|-----------------|  
|**StorageAccount**|Storage-konto du använder. Följande attribut krävs<br /><br /> - **namn på** -namnet på lagringskontot.<br /><br /> - **nyckeln** -nyckeln till lagringskontot.<br /><br /> - **slutpunkten** -slutpunkt för att komma åt lagringskontot. <br /><br /> -**sasToken** (läggs till 1.8.1)-som du kan ange en SAS-token i stället för en lagringskontonyckel in privat-konfigurationen. Om anges, ignoreras lagringskontonyckeln. <br />Krav för SAS-Token: <br />– Stöder endast konto SAS-token <br />- *b*, *t* tjänsttyper krävs. <br /> - *en*, *c*, *u*, *w* behörigheter som krävs. <br /> - *c*, *o* resurstyper krävs. <br /> – Stöder HTTPS-protokollet <br /> -Starta och förfallotiden måste vara giltigt.|  


## <a name="isenabled-element"></a>IsEnabled Element  
 *Trädet: Rot - DiagnosticsConfiguration - IsEnabled*

 Booleskt värde. Använd `true` att aktivera diagnostiken eller `false` att inaktivera diagnostiken.

