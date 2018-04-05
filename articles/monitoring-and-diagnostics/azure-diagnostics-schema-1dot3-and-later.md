---
title: Azure Diagnostics tillägget 1.3 och senare Konfigurationsschemat | Microsoft Docs
description: Schemaversionen 1.3 och senare Azure diagnostics levereras som en del av Microsoft Azure SDK 2.4 och senare.
services: monitoring-and-diagnostics
documentationcenter: .net
author: rboucher
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/15/2017
ms.author: robb
ms.openlocfilehash: 02656c5bb4d2acd944f565d1397984ce94ced0bd
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="azure-diagnostics-13-and-later-configuration-schema"></a>Azure Diagnostics 1.3 och senare Konfigurationsschemat
> [!NOTE]
> Tillägget för Azure-diagnostik är den komponent som används för att samla in prestandaräknare och annan statistik från:
> - Azure Virtual Machines 
> - Skalningsuppsättningar för Virtual Machines
> - Service Fabric 
> - Cloud Services 
> - Nätverkssäkerhetsgrupper
> 
> Den här sidan gäller endast om du använder någon av dessa tjänster.

Den här sidan gäller versioner 1.3 och senare (Azure SDK 2,4 och senare). Nyare konfigurationsavsnitt kommenterats ska visas i vilken version de har lagts till.  

Konfigurationsfilen som beskrivs här används för att ange inställningar för diagnostik när diagnostik monitor startar.  

Tillägget används tillsammans med andra produkter från Microsoft diagnostics som Azure-Monitor, Application Insights och logganalys.



Hämta schemadefinitionen offentliga konfiguration filen genom att köra följande PowerShell-kommando:  

```powershell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  

Mer information om hur du använder Azure-diagnostik finns [Azure Diagnostics tillägget](azure-diagnostics.md).  

## <a name="example-of-the-diagnostics-configuration-file"></a>Exempel på konfigurationsfilen diagnostik  
 I följande exempel visas en typisk diagnostik konfigurationsfil:  

```xml  
<?xml version="1.0" encoding="utf-8"?>  
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">   
  <PublicConfig>  
    <WadCfg>  
      <DiagnosticMonitorConfiguration overallQuotaInMB="10000">  

        <PerformanceCounters scheduledTransferPeriod="PT1M">  
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

JSON motsvarande tidigare XML-konfigurationsfilen. 

PublicConfig och PrivateConfig avgränsas eftersom json användning oftast skickas de som olika variabler. Dessa fall innehåller Resource Manager-mallar, Virtual Machine Scale set PowerShell och Visual Studio. 

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

## <a name="reading-this-page"></a>Läsa den här sidan  
 De taggar som följer är ungefär i ordning som visas i föregående exempel.  Om du inte ser en fullständig beskrivning där du förväntar dig, kan du söka sidan för elementet eller attributet.  

## <a name="common-attribute-types"></a>Vanliga attribut  
 **scheduledTransferPeriod** attributet visas i flera element. Det är intervallet mellan schemalagda överföringar till lagring avrundat till närmaste minut. Värdet är en [XML ”varaktighet datatyp”.](http://www.w3schools.com/schema/schema_dtypes_date.asp)


## <a name="diagnosticsconfiguration-element"></a>DiagnosticsConfiguration Element  
 *: Trädrot - DiagnosticsConfiguration*

Lägga till i version 1.3.  

Det översta elementet i konfigurationsfilen diagnostik.  

**Attributet** xmlns - XML-namnområdet för diagnostik-konfigurationsfilen är:  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  


|Underordnade element|Beskrivning|  
|--------------------|-----------------|  
|**PublicConfig**|Krävs. Se beskrivning på annan plats på den här sidan.|  
|**PrivateConfig**|Valfri. Se beskrivning på annan plats på den här sidan.|  
|**IsEnabled**|Booleskt värde. Se beskrivning på annan plats på den här sidan.|  

## <a name="publicconfig-element"></a>PublicConfig Element  
 *Trädet: Rot - DiagnosticsConfiguration - PublicConfig*

 Beskriver offentliga diagnostik-konfigurationen.  

|Underordnade element|Beskrivning|  
|--------------------|-----------------|  
|**WadCfg**|Krävs. Se beskrivning på annan plats på den här sidan.|  
|**StorageAccount**|Namnet på Azure Storage-konto för att lagra data i. Kan också anges som en parameter när du kör cmdlet Set-AzureServiceDiagnosticsExtension.|  
|**StorageType**|Kan vara *tabell*, *Blob*, eller *TableAndBlob*. Tabellen är standard. När du har valt TableAndBlob skrivs diagnostikdata två gånger – en gång till varje typ av.|  
|**LocalResourceDirectory**|Katalogen på den virtuella datorn där Monitoring Agent lagrar händelsedata. Om du inte har angetts standardkatalogen:<br /><br /> För en arbetare/webbroll: `C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> För en virtuell dator: `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> Obligatoriska attribut är:<br /><br /> - **sökvägen** -katalogen på system som ska användas av Azure-diagnostik.<br /><br /> - **expandEnvironment** -styr om expanderas miljövariabler i sökvägen.|  

## <a name="wadcfg-element"></a>WadCFG Element  
 *Trädet: Rot - DiagnosticsConfiguration - PublicConfig - WadCFG*
 
 Identifierar och konfigurerar att samla in telemetridata.  


## <a name="diagnosticmonitorconfiguration-element"></a>DiagnosticMonitorConfiguration Element 
 *Trädet: Rot - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration*

 Krävs 

|Attribut|Beskrivning|  
|----------------|-----------------|  
| **overallQuotaInMB** | Den maximala mängden lokalt diskutrymme som kan användas av de olika typerna av diagnostiska data som samlas in av Azure-diagnostik. Standardinställningen är 4 096 MB.<br />
|**useProxyServer** | Konfigurera Azure-diagnostik för att använda inställningarna för proxyservern som angetts i Internet Explorer-inställningar.|  

<br /> <br />

|Underordnade element|Beskrivning|  
|--------------------|-----------------|  
|**CrashDumps**|Se beskrivning på annan plats på den här sidan.|  
|**DiagnosticInfrastructureLogs**|Aktivera insamling av loggar som genereras av Azure-diagnostik. Infrastruktur för diagnostiska loggar är användbara för att felsöka systemets diagnostik. Valfria attribut är:<br /><br /> - **scheduledTransferLogLevelFilter** -konfigurerar lägsta allvarlighetsgrad för loggar samlas in.<br /><br /> - **scheduledTransferPeriod** -intervallet mellan schemalagda överföringar till lagring avrundat uppåt till närmaste minut. Värdet är en [XML ”varaktighet datatyp”.](http://www.w3schools.com/schema/schema_dtypes_date.asp) |  
|**Kataloger**|Se beskrivning på annan plats på den här sidan.|  
|**EtwProviders**|Se beskrivning på annan plats på den här sidan.|  
|**Mått**|Se beskrivning på annan plats på den här sidan.|  
|**performanceCounters**|Se beskrivning på annan plats på den här sidan.|  
|**WindowsEventLog**|Se beskrivning på annan plats på den här sidan.| 
|**DockerSources**|Se beskrivning på annan plats på den här sidan. | 



## <a name="crashdumps-element"></a>CrashDumps Element  
 *Trädet: Rot - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - CrashDumps*
 
 Aktivera insamling av krascher Dumpar.  

|Attribut|Beskrivning|  
|----------------|-----------------|  
|**containerName**|Valfri. Namnet på blob-behållaren i Azure Storage-konto som används för att lagra krascher Dumpar.|  
|**crashDumpType**|Valfri.  Konfigurerar Azure-diagnostik för att samla in Dumpar mini eller fullständig kraschar.|  
|**directoryQuotaPercentage**|Valfri.  Konfigurerar procentandelen av **overallQuotaInMB** som ska reserveras för kraschdumpar på den virtuella datorn.|  

|Underordnade element|Beskrivning|  
|--------------------|-----------------|  
|**CrashDumpConfiguration**|Krävs. Definierar konfigurationsvärden för varje process.<br /><br /> Det krävs också följande attribut:<br /><br /> **Processnamn** -namnet på processen som du vill att Azure-diagnostik för att samla in en krasch-dump för.|  

## <a name="directories-element"></a>Kataloger Element 
 *Trädet: Rot - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - kataloger*

 Aktiverar insamlingen av innehållet i en katalog, IIS kunde inte åtkomstloggar för begäran och/eller IIS-loggar.  

 Valfria **scheduledTransferPeriod** attribut. Se tidigare förklaring.  

|Underordnade element|Beskrivning|  
|--------------------|-----------------|  
|**IISLogs**|Inkludera det här elementet i konfigurationen aktiverar insamlingen av IIS-loggar:<br /><br /> **containerName** -namnet på blob-behållaren i Azure Storage-konto som används för att lagra IIS-loggarna.|   
|**FailedRequestLogs**|Inklusive det här elementet i konfigurationen möjliggör insamling av loggar för misslyckade begäranden till en IIS-webbplats eller program. Du måste även aktivera spårningsalternativ under **system. Webbserver** i **Web.config**.|  
|**Datakällor**|En lista över kataloger om du vill övervaka.| 




## <a name="datasources-element"></a>Datakällor Element  
 *Trädet: Rot - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - kataloger - datakällor*

 En lista över kataloger om du vill övervaka.  

|Underordnade element|Beskrivning|  
|--------------------|-----------------|  
|**DirectoryConfiguration**|Krävs. Obligatoriskt attribut:<br /><br /> **containerName** -namnet på blob-behållaren i Azure Storage-konto som används för att lagra loggfilerna.|  





## <a name="directoryconfiguration-element"></a>DirectoryConfiguration Element  
 *Trädet: Rot - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - kataloger - datakällor - DirectoryConfiguration*

 Kan innehålla antingen den **absolut** eller **LocalResource** element, men inte båda.  

|Underordnade element|Beskrivning|  
|--------------------|-----------------|  
|**Absolut**|Den absoluta sökvägen till katalogen som ska övervakas. Det krävs följande attribut:<br /><br /> - **Sökvägen** -den absoluta sökvägen till katalogen som ska övervakas.<br /><br /> - **expandEnvironment** -anger om miljövariabler i sökvägen expanderas.|  
|**LocalResource**|Sökväg i förhållande till en lokal resurs för att övervaka. Obligatoriska attribut är:<br /><br /> - **Namnet** – den lokala resursen som innehåller katalogen som ska övervakas<br /><br /> - **relativePath** -sökväg i förhållande till namn som innehåller katalogen som ska övervakas|  



## <a name="etwproviders-element"></a>EtwProviders Element  
 *Trädet: Rot - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders*

 Konfigurerar samling ETW-händelser från EventSource och/eller ETW Manifest baserade providers.  

|Underordnade element|Beskrivning|  
|--------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|Konfigurerar insamling av händelser som genereras från [EventSource klassen](http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). Obligatoriskt attribut:<br /><br /> **Providern** -klassnamnet för händelsen EventSource.<br /><br /> Valfria attribut är:<br /><br /> - **scheduledTransferLogLevelFilter** -lägsta allvarlighetsgrad att överföra till ditt lagringskonto.<br /><br /> - **scheduledTransferPeriod** -intervallet mellan schemalagda överföringar till lagring avrundat uppåt till närmaste minut. Värdet är en [XML ”varaktighet datatyp”.](http://www.w3schools.com/schema/schema_dtypes_date.asp) |  
|**EtwManifestProviderConfiguration**|Obligatoriskt attribut:<br /><br /> **Providern** -GUID av Händelseprovidern<br /><br /> Valfria attribut är:<br /><br /> - **scheduledTransferLogLevelFilter** -lägsta allvarlighetsgrad att överföra till ditt lagringskonto.<br /><br /> - **scheduledTransferPeriod** -intervallet mellan schemalagda överföringar till lagring avrundat uppåt till närmaste minut. Värdet är en [XML ”varaktighet datatyp”.](http://www.w3schools.com/schema/schema_dtypes_date.asp) |  



## <a name="etweventsourceproviderconfiguration-element"></a>EtwEventSourceProviderConfiguration Element  
 *: Trädrot - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders - EtwEventSourceProviderConfiguration*

 Konfigurerar insamling av händelser som genereras från [EventSource klassen](http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx).  

|Underordnade element|Beskrivning|  
|--------------------|-----------------|  
|**DefaultEvents**|Valfria attribut:<br/><br/> **eventDestination** -namnet på tabellen för att lagra händelser i|  
|**Händelse**|Obligatoriskt attribut:<br /><br /> **ID** -id för händelsen.<br /><br /> Valfria attribut:<br /><br /> **eventDestination** -namnet på tabellen för att lagra händelser i|  



## <a name="etwmanifestproviderconfiguration-element"></a>EtwManifestProviderConfiguration Element  
 *Trädet: Rot - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders - EtwManifestProviderConfiguration*

|Underordnade element|Beskrivning|  
|--------------------|-----------------|  
|**DefaultEvents**|Valfria attribut:<br /><br /> **eventDestination** -namnet på tabellen för att lagra händelser i|  
|**Händelse**|Obligatoriskt attribut:<br /><br /> **ID** -id för händelsen.<br /><br /> Valfria attribut:<br /><br /> **eventDestination** -namnet på tabellen för att lagra händelser i|  



## <a name="metrics-element"></a>Mått Element  
 *Trädet: Rot - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - mått*

 Kan du generera en prestandaräknaren tabell som är optimerad för snabb frågor. Varje prestandaräknare som definieras i den **PerformanceCounters** element lagras i tabellen mått förutom tabellen prestandaräknaren.  

 Den **resourceId** -attribut krävs.  Resurs-ID för den virtuella datorn eller virtuella datorn du distribuerar till Azure-diagnostik. Hämta den **resourceID** från den [Azure-portalen](https://portal.azure.com). Välj **Bläddra** -> **resursgrupper** -> **< namn\>**. Klicka på den **egenskaper** panelen och kopiera värdet från den **ID** fältet.  

|Underordnade element|Beskrivning|  
|--------------------|-----------------|  
|**MetricAggregation**|Obligatoriskt attribut:<br /><br /> **scheduledTransferPeriod** -intervallet mellan schemalagda överföringar till lagring avrundat uppåt till närmaste minut. Värdet är en [XML ”varaktighet datatyp”.](http://www.w3schools.com/schema/schema_dtypes_date.asp) |  



## <a name="performancecounters-element"></a>PerformanceCounters Element  
 *Trädet: Rot - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - prestandaräknarna*

 Aktiverar insamlingen av prestandaräknare.  

 Valfria attribut:  

 Valfria **scheduledTransferPeriod** attribut. Se tidigare förklaring.

|Underordnat Element|Beskrivning|  
|-------------------|-----------------|  
|**PerformanceCounterConfiguration**|Det krävs följande attribut:<br /><br /> - **counterSpecifier** -namnet på prestandaräknaren. Till exempel `\Processor(_Total)\% Processor Time`. Om du vill hämta en lista över prestandaräknare på värddatorn, kör du kommandot `typeperf`.<br /><br /> - **sampleRate** -hur ofta räknaren ska samlas in.<br /><br /> Valfria attribut:<br /><br /> **enhet** -enheten för räknaren.|  




## <a name="windowseventlog-element"></a>WindowsEventLog Element
 *Trädet: Rot - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - WindowsEventLog*
 
 Aktiverar insamlingen av Windows-händelseloggar.  

 Valfria **scheduledTransferPeriod** attribut. Se tidigare förklaring.  

|Underordnat Element|Beskrivning|  
|-------------------|-----------------|  
|**DataSource**|Windows-händelseloggar att samla in. Obligatoriskt attribut:<br /><br /> **namnet** - XPath-frågan som beskriver de windows-händelserna som ska hämtas. Exempel:<br /><br /> `Application!*[System[(Level <=3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level <= 3)]`<br /><br /> Om du vill samla in alla händelser, ange ”*”|  




## <a name="logs-element"></a>Loggar Element  
 *Trädet: Rot - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - loggar*

 Ge i version 1.0- och 1.1. Saknas i 1.2. Lägga till i 1.3.  

 Definierar konfigurationen buffert för grundläggande Azure loggar.  

|Attribut|Typ|Beskrivning|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|**unsignedInt**|Valfri. Anger maximal mängd fillagring för system som är tillgängliga för angivna data.<br /><br /> Standardvärdet är 0.|  
|**scheduledTransferLogLevelFilterr**|**Sträng**|Valfri. Anger den lägsta allvarlighetsgraden för loggposter som överförs. Standardvärdet är **Undefined**, som överför alla loggar. Andra möjliga värden (i ordningen för de flesta till minst information) är **utförlig**, **Information**, **varning**, **fel**, och **Kritiska**.|  
|**scheduledTransferPeriod**|**Varaktighet**|Valfri. Anger intervallet mellan schemalagda dataöverföringar, avrundat till närmaste minut.<br /><br /> Standardvärdet är PT0S.|  
|**egenskaperna** lades till i 1.5|**Sträng**|Valfri. Pekar på en plats för mottagaren att också skicka diagnostikdata. Till exempel Application Insights.|  

## <a name="dockersources"></a>DockerSources
 *Trädet: Rot - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - DockerSources*

 Lägga till i 1.9.

|Elementnamn|Beskrivning|  
|------------------|-----------------|  
|**Statistik**|Anger att samla in statistik för Docker-behållare|  

## <a name="sinksconfig-element"></a>SinksConfig Element  
 *Trädet: Rot - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig*

 En lista över platser för att skicka diagnostikdata till och den konfiguration som är kopplade till dessa platser.  

|Elementnamn|Beskrivning|  
|------------------|-----------------|  
|**Sink**|Se beskrivning på annan plats på den här sidan.|  

## <a name="sink-element"></a>Sink Element
 *Trädet: Rot - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig - mottagare*

 Lägga till i version 1.5.  

 Definierar platser för att skicka diagnostikdata till. Till exempel Application Insights-tjänsten.  

|Attribut|Typ|Beskrivning|  
|---------------|----------|-----------------|  
|**Namn**|sträng|En sträng som identifierar sinkname.|  

|Element|Typ|Beskrivning|  
|-------------|----------|-----------------|  
|**Application Insights**|sträng|Används endast när data skickades till Application Insights. Innehåller den Instrumentation nyckeln för ett aktivt Application Insights-konto som du har åtkomst till.|  
|**kanaler**|sträng|En för varje ytterligare filtrering strömma som du|  

## <a name="channels-element"></a>Kanaler Element  
 *Trädet: Rot - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig - Sink - kanaler*

 Lägga till i version 1.5.  

 Definierar filter för dataströmmar logg går genom en mottagare.  

|Element|Typ|Beskrivning|  
|-------------|----------|-----------------|  
|**Channel**|sträng|Se beskrivning på annan plats på den här sidan.|  

## <a name="channel-element"></a>Kanal Element
 *Trädet: Rot - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig - Sink - kanaler - kanal*

 Lägga till i version 1.5.  

 Definierar platser för att skicka diagnostikdata till. Till exempel Application Insights-tjänsten.  

|Attribut|Typ|Beskrivning|  
|----------------|----------|-----------------|  
|**logLevel**|**Sträng**|Anger den lägsta allvarlighetsgraden för loggposter som överförs. Standardvärdet är **Undefined**, som överför alla loggar. Andra möjliga värden (i ordningen för de flesta till minst information) är **utförlig**, **Information**, **varning**, **fel**, och **Kritiska**.|  
|**Namn**|**Sträng**|Ett unikt namn för att referera till kanalen|  


## <a name="privateconfig-element"></a>PrivateConfig Element 
 *Trädet: Rot - DiagnosticsConfiguration - PrivateConfig*

 Lägga till i version 1.3.  

 Valfri  

 Lagrar privat information om lagringskontot (namn, nyckel och slutpunkten). Den här informationen skickas till den virtuella datorn, men kan inte hämtas från den.  

|Underordnade element|Beskrivning|  
|--------------------|-----------------|  
|**StorageAccount**|Lagringskontot för att använda. Följande attribut krävs<br /><br /> - **namnet** -namnet på lagringskontot.<br /><br /> - **nyckeln** -nyckeln till lagringskontot.<br /><br /> - **slutpunkten** -slutpunkter för åtkomst till lagringskontot. <br /><br /> -**sasToken** (läggs till 1.8.1)-som du kan ange en SAS-token i stället för en lagringskontonyckel i privata konfig. Om anges ignoreras lagringskontots åtkomstnyckel. <br />Krav för SAS-Token: <br />– Stöder endast konto SAS-token <br />- *b*, *t* tjänsttyper krävs. <br /> - *en*, *c*, *u*, *w* behörigheter som krävs. <br /> - *c*, *o* resurstyper krävs. <br /> – Stöder HTTPS-protokollet <br /> -Start och förfallotiden måste vara giltigt.|  


## <a name="isenabled-element"></a>IsEnabled Element  
 *Trädet: Rot - DiagnosticsConfiguration - IsEnabled*

 Booleskt värde. Använd `true` att aktivera diagnostiken eller `false` att inaktivera diagnostiken.
