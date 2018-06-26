---
title: Azure Diagnostics tillägget 1.2 Konfigurationsschemat
description: Detta gäller endast om du använder Azure SDK 2.5 med Azure virtuella datorer, virtuella datorer, Service Fabric eller molntjänster.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/15/2017
ms.author: robb
ms.component: diagnostic-extension
ms.openlocfilehash: 8c3980231404e5c8068dbd011d20759f207d7fff
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36937962"
---
# <a name="azure-diagnostics-12-configuration-schema"></a>Azure Diagnostics 1.2 Konfigurationsschemat
> [!NOTE]
> Azure Diagnostics är en komponent som används för att samla in prestandaräknare och annan statistik från Azure virtuella datorer, virtuella datorer, Service Fabric och Cloud Services.  Den här sidan gäller endast om du använder någon av dessa tjänster.
>

Azure Diagnostics används tillsammans med andra produkter från Microsoft diagnostics som Azure-Monitor, Application Insights och logganalys.

Det här schemat definierar de möjliga värdena som du kan använda för att initiera diagnostiska konfigurationsinställningar när diagnostik monitor startar.  


 Hämta schemadefinitionen offentliga konfiguration filen genom att köra följande PowerShell-kommando:  

```PowerShell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  

 Mer information om hur du använder Azure-diagnostik finns [aktiverar diagnostik i Azure Cloud Services](http://azure.microsoft.com/documentation/articles/cloud-services-dotnet-diagnostics/).  

## <a name="example-of-the-diagnostics-configuration-file"></a>Exempel på konfigurationsfilen diagnostik  
 I följande exempel visas en typisk diagnostik konfigurationsfil:  

```xml
<?xml version="1.0" encoding="utf-8"?>  
<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">  
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
        <EtwEventSourceProviderConfiguration provider="MyProviderClass" scheduledTransferPeriod="PT5M">  
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
      <CrashDumps containerName="wad-crashdumps" directoryQuotaPercentage="30" dumpType="Mini">  
        <CrashDumpConfiguration processName="mynewprocess.exe" />  
        <CrashDumpConfiguration processName="badapp.exe"/>  
      </CrashDumps>  
    </DiagnosticMonitorConfiguration>  
  </WadCfg>  
</PublicConfig>  

```  

## <a name="diagnostics-configuration-namespace"></a>Diagnostik Configuration Namespace  
 XML-namnområdet för diagnostik-konfigurationsfilen är:  

```  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  
```  

## <a name="publicconfig-element"></a>PublicConfig Element  
 Det översta elementet i konfigurationsfilen diagnostik. I följande tabell beskrivs elementen i konfigurationsfilen.  

|Elementnamn|Beskrivning|  
|------------------|-----------------|  
|**WadCfg**|Krävs. Konfigurationsinställningar för telemetridata ska samlas in.|  
|**StorageAccount**|Namnet på Azure Storage-konto för att lagra data i. Detta kan också anges som en parameter när du kör cmdlet Set-AzureServiceDiagnosticsExtension.|  
|**LocalResourceDirectory**|Katalogen på den virtuella datorn som ska användas av Monitoring Agent för lagring av händelsedata. Om inte, standardkatalogen används:<br /><br /> För en arbetare/webbroll: `C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> För en virtuell dator: `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> Obligatoriska attribut är:<br /><br /> -                      **sökvägen** -katalogen på system som ska användas av Azure-diagnostik.<br /><br /> -                      **expandEnvironment** -styr om expanderas miljövariabler i sökvägen.|  

## <a name="wadcfg-element"></a>WadCFG Element  
Definierar konfigurationsinställningar för telemetridata ska hämtas. I följande tabell beskrivs underordnade element:  

|Elementnamn|Beskrivning|  
|------------------|-----------------|  
|**DiagnosticMonitorConfiguration**|Krävs. Valfria attribut är:<br /><br /> -                     **overallQuotaInMB** -maximal mängd lokalt diskutrymme som kan användas av de olika typerna av diagnostiska data som samlas in av Azure-diagnostik. Standardinställningen är 5 120 MB.<br /><br /> -                     **useProxyServer** -konfigurera Azure-diagnostik att använda inställningarna för proxyservern som angetts i Internet Explorer-inställningar.|  
|**CrashDumps**|Aktivera insamling av krascher Dumpar. Valfria attribut är:<br /><br /> -                     **containerName** -namnet på blob-behållaren i Azure Storage-konto som används för att lagra krascher Dumpar.<br /><br /> -                     **crashDumpType** -Dumpar konfigurerar Azure Diagnostics för att samla in Mini eller fullständig kraschar.<br /><br /> -                     **directoryQuotaPercentage**-konfigurerar procentandelen av **overallQuotaInMB** som ska reserveras för kraschdumpar på den virtuella datorn.|  
|**DiagnosticInfrastructureLogs**|Aktivera insamling av loggar som genereras av Azure-diagnostik. Infrastruktur för diagnostiska loggar är användbara för att felsöka systemets diagnostik. Valfria attribut är:<br /><br /> -                     **scheduledTransferLogLevelFilter** -konfigurerar lägsta allvarlighetsgrad för loggar samlas in.<br /><br /> -                     **scheduledTransferPeriod** -intervallet mellan schemalagda överföringar till lagring avrundat uppåt till närmaste minut. Värdet är en [XML ”varaktighet datatyp”.](http://www.w3schools.com/xml/schema_dtypes_date.asp)|  
|**Kataloger**|Aktiverar insamlingen av innehållet i en katalog, IIS kunde inte åtkomstloggar för begäran och/eller IIS-loggar. Valfria attribut:<br /><br /> **scheduledTransferPeriod** -intervallet mellan schemalagda överföringar till lagring avrundat uppåt till närmaste minut. Värdet är en [XML ”varaktighet datatyp”.](http://www.w3schools.com/xml/schema_dtypes_date.asp)|  
|**EtwProviders**|Konfigurerar samling ETW-händelser från EventSource och/eller ETW Manifest baserade providers.|  
|**Mått**|Det här elementet kan du generera en prestandaräknaren tabell som är optimerad för snabb frågor. Varje prestandaräknare som definieras i den **PerformanceCounters** element lagras i tabellen mått förutom tabellen prestandaräknaren. Obligatoriskt attribut:<br /><br /> **resourceId** -detta är resurs-ID för den virtuella datorn som du distribuerar till Azure-diagnostik. Hämta den **resourceID** från den [Azure-portalen](https://portal.azure.com). Välj **Bläddra** -> **resursgrupper** -> **< namn\>**. Klicka på den **egenskaper** panelen och kopiera värdet från den **ID** fältet.|  
|**PerformanceCounters**|Aktiverar insamlingen av prestandaräknare. Valfria attribut:<br /><br /> **scheduledTransferPeriod** -intervallet mellan schemalagda överföringar till lagring avrundat uppåt till närmaste minut. Värdet är en [XML ”varaktighet datatyp”.](http://www.w3schools.com/xml/schema_dtypes_date.asp)|  
|**WindowsEventLog**|Aktiverar insamlingen av Windows-händelseloggar. Valfria attribut:<br /><br /> **scheduledTransferPeriod** -intervallet mellan schemalagda överföringar till lagring avrundat uppåt till närmaste minut. Värdet är en [XML ”varaktighet datatyp”.](http://www.w3schools.com/xml/schema_dtypes_date.asp)|  

## <a name="crashdumps-element"></a>CrashDumps Element  
 Aktiverar krascher Dumpar samling. I följande tabell beskrivs underordnade element:  

|Elementnamn|Beskrivning|  
|------------------|-----------------|  
|**CrashDumpConfiguration**|Krävs. Obligatoriskt attribut:<br /><br /> **Processnamn** -namnet på processen som du vill att Azure-diagnostik för att samla in en krasch-dump för.|  
|**crashDumpType**|Konfigurerar Azure-diagnostik för att samla in Dumpar mini eller fullständig kraschar.|  
|**directoryQuotaPercentage**|Konfigurerar procentandelen av **overallQuotaInMB** som ska reserveras för kraschdumpar på den virtuella datorn.|  

## <a name="directories-element"></a>Kataloger Element  
 Aktiverar insamlingen av innehållet i en katalog, IIS kunde inte åtkomstloggar för begäran och/eller IIS-loggar. I följande tabell beskrivs underordnade element:  

|Elementnamn|Beskrivning|  
|------------------|-----------------|  
|**Datakällor**|En lista över kataloger om du vill övervaka.|  
|**FailedRequestLogs**|Inklusive det här elementet i konfigurationen möjliggör insamling av loggar för misslyckade begäranden till en IIS-webbplats eller program. Du måste även aktivera spårningsalternativ under **system. Webbserver** i **Web.config**.|  
|**IISLogs**|Inkludera det här elementet i konfigurationen aktiverar insamlingen av IIS-loggar:<br /><br /> **containerName** -namnet på blob-behållaren i Azure Storage-konto som används för att lagra IIS-loggarna.|  

## <a name="datasources-element"></a>Datakällor Element  
 En lista över kataloger om du vill övervaka. I följande tabell beskrivs underordnade element:  

|Elementnamn|Beskrivning|  
|------------------|-----------------|  
|**DirectoryConfiguration**|Krävs. Obligatoriskt attribut:<br /><br /> **containerName** -namnet på blob-behållaren i Azure Storage-konto som används för att lagra loggfilerna.|  

## <a name="directoryconfiguration-element"></a>DirectoryConfiguration Element  
 **DirectoryConfiguration** kan innehålla antingen den **absolut** eller **LocalResource** element, men inte båda. I följande tabell beskrivs underordnade element:  

|Elementnamn|Beskrivning|  
|------------------|-----------------|  
|**Absolut**|Den absoluta sökvägen till katalogen som ska övervakas. Det krävs följande attribut:<br /><br /> -                     **Sökvägen** -den absoluta sökvägen till katalogen som ska övervakas.<br /><br /> -                      **expandEnvironment** -anger om miljövariabler i sökvägen expanderas.|  
|**LocalResource**|Sökväg i förhållande till en lokal resurs för att övervaka. Obligatoriska attribut är:<br /><br /> -                     **Namnet** – den lokala resursen som innehåller katalogen som ska övervakas<br /><br /> -                     **relativePath** -sökväg i förhållande till namn som innehåller katalogen som ska övervakas|  

## <a name="etwproviders-element"></a>EtwProviders Element  
 Konfigurerar samling ETW-händelser från EventSource och/eller ETW Manifest baserade providers. I följande tabell beskrivs underordnade element:  

|Elementnamn|Beskrivning|  
|------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|Konfigurerar insamling av händelser som genereras från [EventSource klassen](http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). Obligatoriskt attribut:<br /><br /> **Providern** -klassnamnet för händelsen EventSource.<br /><br /> Valfria attribut är:<br /><br /> -                     **scheduledTransferLogLevelFilter** -lägsta allvarlighetsgrad att överföra till ditt lagringskonto.<br /><br /> -                     **scheduledTransferPeriod** -intervallet mellan schemalagda överföringar till lagring avrundat uppåt till närmaste minut. Värdet är en [XML-datatypen för varaktighet](http://www.w3schools.com/xml/schema_dtypes_date.asp).|  
|**EtwManifestProviderConfiguration**|Obligatoriskt attribut:<br /><br /> **Providern** -GUID av Händelseprovidern<br /><br /> Valfria attribut är:<br /><br /> - **scheduledTransferLogLevelFilter** -lägsta allvarlighetsgrad att överföra till ditt lagringskonto.<br /><br /> -                     **scheduledTransferPeriod** -intervallet mellan schemalagda överföringar till lagring avrundat uppåt till närmaste minut. Värdet är en [XML-datatypen för varaktighet](http://www.w3schools.com/xml/schema_dtypes_date.asp).|  

## <a name="etweventsourceproviderconfiguration-element"></a>EtwEventSourceProviderConfiguration Element  
 Konfigurerar insamling av händelser som genereras från [EventSource klassen](http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). I följande tabell beskrivs underordnade element:  

|Elementnamn|Beskrivning|  
|------------------|-----------------|  
|**DefaultEvents**|Valfria attribut:<br /><br /> **eventDestination** -namnet på tabellen för att lagra händelser i|  
|**Händelse**|Obligatoriskt attribut:<br /><br /> **ID** -id för händelsen.<br /><br /> Valfria attribut:<br /><br /> **eventDestination** -namnet på tabellen för att lagra händelser i|  

## <a name="etwmanifestproviderconfiguration-element"></a>EtwManifestProviderConfiguration Element  
 I följande tabell beskrivs underordnade element:  

|Elementnamn|Beskrivning|  
|------------------|-----------------|  
|**DefaultEvents**|Valfria attribut:<br /><br /> **eventDestination** -namnet på tabellen för att lagra händelser i|  
|**Händelse**|Obligatoriskt attribut:<br /><br /> **ID** -id för händelsen.<br /><br /> Valfria attribut:<br /><br /> **eventDestination** -namnet på tabellen för att lagra händelser i|  

## <a name="metrics-element"></a>Mått Element  
 Kan du generera en prestandaräknaren tabell som är optimerad för snabb frågor. I följande tabell beskrivs underordnade element:  

|Elementnamn|Beskrivning|  
|------------------|-----------------|  
|**MetricAggregation**|Obligatoriskt attribut:<br /><br /> **scheduledTransferPeriod** -intervallet mellan schemalagda överföringar till lagring avrundat uppåt till närmaste minut. Värdet är en [XML-datatypen för varaktighet](http://www.w3schools.com/xml/schema_dtypes_date.asp).|  

## <a name="performancecounters-element"></a>PerformanceCounters elementet  
 Aktiverar insamlingen av prestandaräknare. I följande tabell beskrivs underordnade element:  

|Elementnamn|Beskrivning|  
|------------------|-----------------|  
|**PerformanceCounterConfiguration**|Det krävs följande attribut:<br /><br /> -                     **counterSpecifier** -namnet på prestandaräknaren. Till exempel `\Processor(_Total)\% Processor Time`. För att hämta en lista över prestanda räknare på din värd kör kommandot `typeperf`.<br /><br /> -                     **sampleRate** -hur ofta räknaren ska samlas in.<br /><br /> Valfria attribut:<br /><br /> **enhet** -enheten för räknaren.|  

## <a name="performancecounterconfiguration-element"></a>PerformanceCounterConfiguration Element  
 I följande tabell beskrivs underordnade element:  

|Elementnamn|Beskrivning|  
|------------------|-----------------|  
|**anteckningen**|Obligatoriskt attribut:<br /><br /> **displayName** -visningsnamnet för räknaren<br /><br /> Valfria attribut:<br /><br /> **språk** -språket som ska användas för att visa räknarnamnet|  

## <a name="windowseventlog-element"></a>WindowsEventLog Element  
 I följande tabell beskrivs underordnade element:  

|Elementnamn|Beskrivning|  
|------------------|-----------------|  
|**DataSource**|Windows-händelseloggar att samla in. Obligatoriskt attribut:<br /><br /> **namnet** - XPath-frågan som beskriver de windows-händelserna som ska hämtas. Exempel:<br /><br /> `Application!*[System[(Level >= 3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level >= 3]]`<br /><br /> Om du vill samla in alla händelser, ange ”*”.|
