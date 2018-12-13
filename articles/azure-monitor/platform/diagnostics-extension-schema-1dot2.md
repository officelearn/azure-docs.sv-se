---
title: Azure Diagnostics-tillägg 1.2 konfigurationsschema
description: Detta gäller endast om du använder Azure SDK 2.5 med Azure Virtual Machines, Virtual Machine Scale Sets, Service Fabric och Cloud Services.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/15/2017
ms.author: robb
ms.component: diagnostic-extension
ms.openlocfilehash: 322cd75fe9198bae459e7c22bed794f583d13363
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2018
ms.locfileid: "53326370"
---
# <a name="azure-diagnostics-12-configuration-schema"></a>Schema för Azure Diagnostics 1.2-konfiguration
> [!NOTE]
> Azure-diagnostik är den komponent som används för att samla in prestandaräknare och annan statistik från Azure Virtual Machines, Virtual Machine Scale Sets, Service Fabric och Cloud Services.  Den här sidan gäller endast om du använder någon av dessa tjänster.
>

Azure-diagnostik används med andra Microsoft-produkter för diagnostik som Azure Monitor, Application Insights och Log Analytics.

Det här schemat definierar de möjliga värdena som du kan använda för att initiera diagnostiska inställningar när diagnostik monitor startar.  


 Hämta offentliga konfiguration filen schemadefinitionen genom att köra följande PowerShell-kommando:  

```PowerShell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  

 Läs mer om hur du använder Azure Diagnostics [hur du aktiverar diagnostik i Azure Cloud Services](https://azure.microsoft.com/documentation/articles/cloud-services-dotnet-diagnostics/).  

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
 Översta elementet i konfigurationsfilen för diagnostik. I följande tabell beskriver elementen i konfigurationsfilen.  

|Elementnamn|Beskrivning|  
|------------------|-----------------|  
|**WadCfg**|Krävs. Konfigurationsinställningar för telemetridata samlas in.|  
|**StorageAccount**|Namnet på Azure Storage-konto för att lagra data i. Detta kan också anges som en parameter när du kör cmdleten Set-AzureServiceDiagnosticsExtension.|  
|**LocalResourceDirectory**|Katalogen på den virtuella datorn som ska användas av Monitoring Agent för att lagra händelsedata. Om inte mängd standardkatalogen används:<br /><br /> För en Worker/web-roll: `C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> För en virtuell dator: `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> Obligatoriska attribut är:<br /><br /> -                      **sökvägen** -katalogen på system som ska användas av Azure Diagnostics.<br /><br /> -                      **expandEnvironment** – styr om miljövariabler expanderas i Sökvägens namn.|  

## <a name="wadcfg-element"></a>WadCFG Element  
Definierar konfigurationsinställningar för dessa data som ska samlas in. I följande tabell beskrivs underordnade element:  

|Elementnamn|Beskrivning|  
|------------------|-----------------|  
|**DiagnosticMonitorConfiguration**|Krävs. Valfritt attribut är:<br /><br /> -                     **overallQuotaInMB** -maximal mängd lokalt diskutrymme som kan användas av de olika typerna av diagnostiska data som samlas in av Azure Diagnostics. Standardinställningen är 5 120 MB.<br /><br /> -                     **useProxyServer** – konfigurera Azure Diagnostics för att använda inställningarna för proxyservern som angetts i Internet Explorer-inställningarna.|  
|**CrashDumps**|Aktivera insamling av kraschdumpar. Valfritt attribut är:<br /><br /> -                     **containerName** -namnet på blobbehållaren i ditt Azure Storage-konto som används för att lagra kraschdumpar.<br /><br /> -                     **crashDumpType** -konfigurerar Azure Diagnostics för att samla in Mini eller fullständig kraschen Dumpar.<br /><br /> -                     **directoryQuotaPercentage**-konfigurerar procentandelen **overallQuotaInMB** som ska reserveras för kraschdumpar på den virtuella datorn.|  
|**DiagnosticInfrastructureLogs**|Aktivera insamling av loggar som genereras av Azure Diagnostics. Diagnostic infrastructure-loggar är användbara vid felsökning av själva systemet diagnostik. Valfritt attribut är:<br /><br /> -                     **scheduledTransferLogLevelFilter** – konfigurerar den lägsta allvarlighetsgraden för loggarna som samlas in.<br /><br /> -                     **scheduledTransferPeriod** -intervallet mellan schemalagda överföringar till lagringen avrundas uppåt till närmaste minut. Värdet är en [XML ”varaktighet datatyp”.](http://www.w3schools.com/xml/schema_dtypes_date.asp)|  
|**Kataloger**|Aktiverar insamlingen av innehållet i en katalog, IIS misslyckades åtkomstloggar för begäran och/eller IIS-loggar. Valfritt attribut:<br /><br /> **scheduledTransferPeriod** -intervallet mellan schemalagda överföringar till lagringen avrundas uppåt till närmaste minut. Värdet är en [XML ”varaktighet datatyp”.](http://www.w3schools.com/xml/schema_dtypes_date.asp)|  
|**EtwProviders**|Konfigurerar insamling av ETW-händelser från EventSource och/eller ETW Manifest baserade providers.|  
|**Mått**|Det här elementet kan du skapa en tabell med prestandaräknaren som är optimerad för snabba frågor. Varje prestandaräknare som definieras i den **PerformanceCounters** elementet lagras i tabellen mått förutom tabellen prestandaräknaren. Obligatoriskt attribut:<br /><br /> **resourceId** – detta är resurs-ID för den virtuella datorn som du distribuerar Azure Diagnostics-data till. Hämta den **resourceID** från den [Azure-portalen](https://portal.azure.com). Välj **Bläddra** -> **resursgrupper** -> **< namn\>**. Klicka på den **egenskaper** panelen och kopiera värdet från den **ID** fält.|  
|**PerformanceCounters**|Aktiverar insamling av prestandaräknare. Valfritt attribut:<br /><br /> **scheduledTransferPeriod** -intervallet mellan schemalagda överföringar till lagringen avrundas uppåt till närmaste minut. Värdet är en [XML ”varaktighet datatypen”.](http://www.w3schools.com/xml/schema_dtypes_date.asp)|  
|**WindowsEventLog**|Aktiverar insamlingen av Windows-händelseloggar. Valfritt attribut:<br /><br /> **scheduledTransferPeriod** -intervallet mellan schemalagda överföringar till lagringen avrundas uppåt till närmaste minut. Värdet är en [XML ”varaktighet datatypen”.](http://www.w3schools.com/xml/schema_dtypes_date.asp)|  

## <a name="crashdumps-element"></a>CrashDumps Element  
 Aktiverar insamling av kraschdumpar. I följande tabell beskrivs underordnade element:  

|Elementnamn|Beskrivning|  
|------------------|-----------------|  
|**CrashDumpConfiguration**|Krävs. Obligatoriskt attribut:<br /><br /> **processName** -namnet på processen som du vill att Azure-diagnostik för att samla in en kraschdumpfil för.|  
|**crashDumpType**|Konfigurerar Azure-diagnostik för att samla in Dumpar mini eller fullständig krasch.|  
|**directoryQuotaPercentage**|Konfigurerar procentandelen **overallQuotaInMB** som ska reserveras för kraschdumpar på den virtuella datorn.|  

## <a name="directories-element"></a>Kataloger Element  
 Aktiverar insamlingen av innehållet i en katalog, IIS misslyckades åtkomstloggar för begäran och/eller IIS-loggar. I följande tabell beskrivs underordnade element:  

|Elementnamn|Beskrivning|  
|------------------|-----------------|  
|**Datakällor**|En lista över kataloger som ska övervaka.|  
|**FailedRequestLogs**|Inkludera det här elementet i konfigurationen kan loggsamlingar om misslyckade förfrågningar till ett IIS-webbplats eller ett program. Du måste även aktivera spårningsalternativ under **system. Webbserver** i **Web.config**.|  
|**IISLogs**|Inkludera det här elementet i konfigurationen aktiverar insamlingen av IIS-loggar:<br /><br /> **containerName** -namnet på blobbehållaren i ditt Azure Storage-konto som används för att lagra IIS-loggar.|  

## <a name="datasources-element"></a>Datakällor Element  
 En lista över kataloger som ska övervaka. I följande tabell beskrivs underordnade element:  

|Elementnamn|Beskrivning|  
|------------------|-----------------|  
|**DirectoryConfiguration**|Krävs. Obligatoriskt attribut:<br /><br /> **containerName** -namnet på blobbehållaren i ditt Azure Storage-konto som används för att lagra loggfilerna.|  

## <a name="directoryconfiguration-element"></a>DirectoryConfiguration Element  
 **DirectoryConfiguration** kan innehålla antingen den **absolut** eller **LocalResource** element, men inte båda. I följande tabell beskrivs underordnade element:  

|Elementnamn|Beskrivning|  
|------------------|-----------------|  
|**Absolut**|Den absoluta sökvägen till katalogen som ska övervakas. Det krävs följande attribut:<br /><br /> -                     **Sökvägen** -den absoluta sökvägen till katalogen som ska övervakas.<br /><br /> -                      **expandEnvironment** – konfigurerar om miljövariabler i sökvägen expanderas.|  
|**LocalResource**|Sökväg i förhållande till en lokal resurs du övervakar. Obligatoriska attribut är:<br /><br /> -                     **Namn på** – den lokala resursen som innehåller katalogen som ska övervakas<br /><br /> -                     **relativePath** -sökväg i förhållande till namn som innehåller katalogen som ska övervakas|  

## <a name="etwproviders-element"></a>EtwProviders Element  
 Konfigurerar insamling av ETW-händelser från EventSource och/eller ETW Manifest baserade providers. I följande tabell beskrivs underordnade element:  

|Elementnamn|Beskrivning|  
|------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|Konfigurerar insamling av händelser som genereras från [EventSource klass](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). Obligatoriskt attribut:<br /><br /> **Providern** -klassnamnet händelsens EventSource.<br /><br /> Valfritt attribut är:<br /><br /> -                     **scheduledTransferLogLevelFilter** -den lägsta allvarlighetsgraden att överföra till ditt lagringskonto.<br /><br /> -                     **scheduledTransferPeriod** -intervallet mellan schemalagda överföringar till lagringen avrundas uppåt till närmaste minut. Värdet är en [varaktighet för XML-datatyp](http://www.w3schools.com/xml/schema_dtypes_date.asp).|  
|**EtwManifestProviderConfiguration**|Obligatoriskt attribut:<br /><br /> **Providern** -GUID för händelseprovider<br /><br /> Valfritt attribut är:<br /><br /> - **scheduledTransferLogLevelFilter** -den lägsta allvarlighetsgraden att överföra till ditt lagringskonto.<br /><br /> -                     **scheduledTransferPeriod** -intervallet mellan schemalagda överföringar till lagringen avrundas uppåt till närmaste minut. Värdet är en [varaktighet för XML-datatyp](http://www.w3schools.com/xml/schema_dtypes_date.asp).|  

## <a name="etweventsourceproviderconfiguration-element"></a>EtwEventSourceProviderConfiguration Element  
 Konfigurerar insamling av händelser som genereras från [EventSource klass](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). I följande tabell beskrivs underordnade element:  

|Elementnamn|Beskrivning|  
|------------------|-----------------|  
|**DefaultEvents**|Valfritt attribut:<br /><br /> **eventDestination** -namnet på tabellen för att lagra händelser i|  
|**Händelse**|Obligatoriskt attribut:<br /><br /> **ID** -id för händelsen.<br /><br /> Valfritt attribut:<br /><br /> **eventDestination** -namnet på tabellen för att lagra händelser i|  

## <a name="etwmanifestproviderconfiguration-element"></a>EtwManifestProviderConfiguration Element  
 I följande tabell beskrivs underordnade element:  

|Elementnamn|Beskrivning|  
|------------------|-----------------|  
|**DefaultEvents**|Valfritt attribut:<br /><br /> **eventDestination** -namnet på tabellen för att lagra händelser i|  
|**Händelse**|Obligatoriskt attribut:<br /><br /> **ID** -id för händelsen.<br /><br /> Valfritt attribut:<br /><br /> **eventDestination** -namnet på tabellen för att lagra händelser i|  

## <a name="metrics-element"></a>Mått-Element  
 Kan du skapa en tabell med prestandaräknaren som är optimerad för snabba frågor. I följande tabell beskrivs underordnade element:  

|Elementnamn|Beskrivning|  
|------------------|-----------------|  
|**MetricAggregation**|Obligatoriskt attribut:<br /><br /> **scheduledTransferPeriod** -intervallet mellan schemalagda överföringar till lagringen avrundas uppåt till närmaste minut. Värdet är en [varaktighet för XML-datatyp](http://www.w3schools.com/xml/schema_dtypes_date.asp).|  

## <a name="performancecounters-element"></a>PerformanceCounters Element  
 Aktiverar insamling av prestandaräknare. I följande tabell beskrivs underordnade element:  

|Elementnamn|Beskrivning|  
|------------------|-----------------|  
|**PerformanceCounterConfiguration**|Det krävs följande attribut:<br /><br /> -                     **counterSpecifier** -namnet på prestandaräknaren. Till exempel `\Processor(_Total)\% Processor Time`. För att hämta en lista över prestanda räknare på din värd kör kommandot `typeperf`.<br /><br /> -                     **sampleRate** -hur ofta räknaren ska aktiveras.<br /><br /> Valfritt attribut:<br /><br /> **enhet** -måttenhet för räknaren.|  

## <a name="performancecounterconfiguration-element"></a>PerformanceCounterConfiguration Element  
 I följande tabell beskrivs underordnade element:  

|Elementnamn|Beskrivning|  
|------------------|-----------------|  
|**Anteckning**|Obligatoriskt attribut:<br /><br /> **displayName** – visningsnamnet för räknaren<br /><br /> Valfritt attribut:<br /><br /> **nationella inställningar** -språket som används vid visning av räknarnamnet|  

## <a name="windowseventlog-element"></a>WindowsEventLog Element  
 I följande tabell beskrivs underordnade element:  

|Elementnamn|Beskrivning|  
|------------------|-----------------|  
|**DataSource**|Windows-händelseloggar att samla in. Obligatoriskt attribut:<br /><br /> **namn på** – XPath-frågan som beskriver windows-händelser som ska samlas in. Exempel:<br /><br /> `Application!*[System[(Level >= 3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level >= 3]]`<br /><br /> Om du vill samla in alla händelser, ange ”*”.|
