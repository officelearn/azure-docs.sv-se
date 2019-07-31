---
title: Konfigurations schema för Azure-diagnostik-tillägg 1,2
description: ENDAST relevant om du använder Azure SDK 2,5 med Azure Virtual Machines Virtual Machine Scale Sets, Service Fabric eller Cloud Services.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/15/2017
ms.author: robb
ms.subservice: diagnostic-extension
ms.openlocfilehash: dae74e730d6e175fa3e447150adce4caecd3d7a3
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2019
ms.locfileid: "60237844"
---
# <a name="azure-diagnostics-12-configuration-schema"></a>Azure-diagnostik 1,2-konfigurations schema
> [!NOTE]
> Azure-diagnostik är den komponent som används för att samla in prestanda räknare och annan statistik från Azure Virtual Machines, Virtual Machine Scale Sets, Service Fabric och Cloud Services.  Den här sidan är bara relevant om du använder någon av dessa tjänster.
>

Azure-diagnostik används med andra Microsoft Diagnostics-produkter som Azure Monitor, som innehåller Application Insights och Log Analytics.

Det här schemat definierar de möjliga värden som du kan använda för att initiera diagnostiska konfigurations inställningar när diagnostikprogrammet startar.  


 Hämta schema definitionen för den offentliga konfigurations filen genom att köra följande PowerShell-kommando:  

```powershell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  

 Mer information om hur du använder Azure-diagnostik finns i [Aktivera diagnostik i Azure Cloud Services](https://azure.microsoft.com/documentation/articles/cloud-services-dotnet-diagnostics/).  

## <a name="example-of-the-diagnostics-configuration-file"></a>Exempel på konfigurations filen för diagnostik  
 I följande exempel visas en typisk konfigurations fil för diagnostik:  

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

## <a name="diagnostics-configuration-namespace"></a>Namn område för diagnostik-konfiguration  
 XML-namnområdet för konfigurations filen för diagnostik är:  

```  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  
```  

## <a name="publicconfig-element"></a>PublicConfig-element  
 Toppnivå element i konfigurations filen för diagnostik. I följande tabell beskrivs elementen i konfigurations filen.  

|Element namn|Beskrivning|  
|------------------|-----------------|  
|**WadCfg**|Obligatoriskt. Konfigurations inställningar för telemetri-data som ska samlas in.|  
|**StorageAccount**|Namnet på det Azure Storage konto som data ska lagras i. Detta kan också anges som en parameter när du kör cmdleten Set-AzureServiceDiagnosticsExtension.|  
|**LocalResourceDirectory**|Den katalog på den virtuella datorn som ska användas av övervaknings agenten för att lagra händelse data. Om den inte anges används standard katalogen:<br /><br /> För en Worker/Web-roll:`C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> För en virtuell dator:`C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> Obligatoriska attribut är:<br /><br /> -                      **sökväg** – katalogen i systemet som ska användas av Azure-diagnostik.<br /><br /> -                      **expandEnvironment** – styr om miljövariablerna expanderas i Sök vägs namnet.|  

## <a name="wadcfg-element"></a>WadCFG Element  
Definierar konfigurations inställningar för telemetri-data som ska samlas in. I följande tabell beskrivs underordnade element:  

|Elementnamn|Beskrivning|  
|------------------|-----------------|  
|**DiagnosticMonitorConfiguration**|Obligatoriskt. Valfria attribut är:<br /><br /> -                     **overallQuotaInMB** – den maximala mängden lokalt disk utrymme som kan användas av de olika typerna av diagnostikdata som samlas in av Azure-diagnostik. Standardvärdet är 5120MB.<br /><br /> -                     **useProxyServer** – konfigurera Azure-diagnostik att använda inställningarna för proxyservern som anges i IE-inställningar.|  
|**CrashDumps**|Aktivera samling av krasch dum par. Valfria attribut är:<br /><br /> -                     **containerName** – namnet på BLOB-behållaren i Azure Storage-kontot som ska användas för att lagra krasch dum par.<br /><br /> -                     **crashDumpType** – konfigurerar Azure-diagnostik att samla in mini-eller fullständiga krasch dum par.<br /><br /> -                     **directoryQuotaPercentage**– konfigurerar procent andelen **overallQuotaInMB** som ska reserveras för krasch dum par på den virtuella datorn.|  
|**DiagnosticInfrastructureLogs**|Aktivera samling av loggar som genereras av Azure-diagnostik. De diagnostiska infrastruktur loggarna är användbara för att felsöka själva diagnostik systemet. Valfria attribut är:<br /><br /> -                     **scheduledTransferLogLevelFilter** – konfigurerar den lägsta allvarlighets graden för loggarna som samlas in.<br /><br /> -                     **scheduledTransferPeriod** – intervallet mellan schemalagda överföringar till lagring avrundat uppåt till närmaste minut. Värdet är en [data typ för XML-varaktighet.](https://www.w3schools.com/xml/schema_dtypes_date.asp)|  
|**Kataloger**|Aktiverar insamling av innehållet i en katalog, IIS nekade åtkomst till begär ande loggar och/eller IIS-loggar. Valfritt attribut:<br /><br /> **scheduledTransferPeriod** – intervallet mellan schemalagda överföringar till lagring avrundat uppåt till närmaste minut. Värdet är en [data typ för XML-varaktighet.](https://www.w3schools.com/xml/schema_dtypes_date.asp)|  
|**EtwProviders**|Konfigurerar insamling av ETW-händelser från EventSource och/eller ETW-manifest baserade providers.|  
|**Mått**|Med det här elementet kan du generera en prestanda räknar tabell som är optimerad för snabba frågor. Varje prestanda räknare som definieras i **PerformanceCounters** -elementet lagras i mått tabellen förutom prestanda räknar tabellen. Nödvändigt attribut:<br /><br /> **resourceId** – detta är resurs-ID för den virtuella dator som du distribuerar Azure-diagnostik till. Hämta **resourceID** från [Azure Portal](https://portal.azure.com). Välj **Bläddra** -> **resurs grupper** ->  **< namn.\>** Klicka på panelen **Egenskaper** och kopiera värdet från fältet **ID** .|  
|**PerformanceCounters**|Aktiverar insamling av prestanda räknare. Valfritt attribut:<br /><br /> **scheduledTransferPeriod** – intervallet mellan schemalagda överföringar till lagring avrundat uppåt till närmaste minut. Värdet är en [data typ för XML-varaktighet.](https://www.w3schools.com/xml/schema_dtypes_date.asp)|  
|**WindowsEventLog**|Aktiverar insamling av händelse loggar i Windows. Valfritt attribut:<br /><br /> **scheduledTransferPeriod** – intervallet mellan schemalagda överföringar till lagring avrundat uppåt till närmaste minut. Värdet är en [data typ för XML-varaktighet.](https://www.w3schools.com/xml/schema_dtypes_date.asp)|  

## <a name="crashdumps-element"></a>CrashDumps-element  
 Aktiverar samling av krasch dum par. I följande tabell beskrivs underordnade element:  

|Element namn|Beskrivning|  
|------------------|-----------------|  
|**CrashDumpConfiguration**|Obligatoriskt. Nödvändigt attribut:<br /><br /> **processName** – namnet på den process som du vill Azure-diagnostik för att samla in en kraschdump för.|  
|**crashDumpType**|Konfigurerar Azure-diagnostik att samla in mini-eller fullständiga krasch dum par.|  
|**directoryQuotaPercentage**|Konfigurerar procent andelen av **overallQuotaInMB** som ska reserveras för krasch dum par på den virtuella datorn.|  

## <a name="directories-element"></a>Katalog element  
 Aktiverar insamling av innehållet i en katalog, IIS nekade åtkomst till begär ande loggar och/eller IIS-loggar. I följande tabell beskrivs underordnade element:  

|Element namn|Beskrivning|  
|------------------|-----------------|  
|**Data källor**|En lista över kataloger som ska övervakas.|  
|**FailedRequestLogs**|Genom att inkludera det här elementet i konfigurationen kan du samla in loggar över misslyckade förfrågningar till en IIS-webbplats eller ett program. Du måste också aktivera spårnings alternativ under **system. Webb server** i **Web. config**.|  
|**IISLogs**|Genom att inkludera det här elementet i konfigurationen möjliggörs insamling av IIS-loggar:<br /><br /> **containerName** – namnet på BLOB-behållaren i Azure Storage-kontot som ska användas för att lagra IIS-loggarna.|  

## <a name="datasources-element"></a>Data källa element  
 En lista över kataloger som ska övervakas. I följande tabell beskrivs underordnade element:  

|Element namn|Beskrivning|  
|------------------|-----------------|  
|**DirectoryConfiguration**|Obligatoriskt. Nödvändigt attribut:<br /><br /> **containerName** – namnet på BLOB-behållaren i Azure Storage-kontot som ska användas för att lagra loggfilerna.|  

## <a name="directoryconfiguration-element"></a>DirectoryConfiguration-element  
 **DirectoryConfiguration** kan innehålla antingen det **absoluta** eller **LocalResource** -elementet, men inte båda. I följande tabell beskrivs underordnade element:  

|Element namn|Beskrivning|  
|------------------|-----------------|  
|**Absolutvärde**|Den absoluta sökvägen till den katalog som ska övervakas. Följande attribut krävs:<br /><br /> -                     **Sökväg** – den absoluta sökvägen till den katalog som ska övervakas.<br /><br /> -                      **expandEnvironment** – konfigurerar om miljövariabler i sökvägen ska expanderas.|  
|**LocalResource**|Sökvägen i förhållande till en lokal resurs som ska övervakas. Obligatoriska attribut är:<br /><br /> -                     **Namn** – den lokala resurs som innehåller den katalog som ska övervakas<br /><br /> -                     **relativePath** – sökvägen är relativ till det namn som innehåller den katalog som ska övervakas|  

## <a name="etwproviders-element"></a>EtwProviders-element  
 Konfigurerar insamling av ETW-händelser från EventSource och/eller ETW-manifest baserade providers. I följande tabell beskrivs underordnade element:  

|Element namn|Beskrivning|  
|------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|Konfigurerar insamling av händelser som genereras från [EventSource-klassen](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). Nödvändigt attribut:<br /><br /> **Provider** – klass namnet för EventSource-händelsen.<br /><br /> Valfria attribut är:<br /><br /> -                     **scheduledTransferLogLevelFilter** – den minsta allvarlighets grad som ska överföras till ditt lagrings konto.<br /><br /> -                     **scheduledTransferPeriod** – intervallet mellan schemalagda överföringar till lagring avrundat uppåt till närmaste minut. Värdet är en [data typ för XML-varaktighet](https://www.w3schools.com/xml/schema_dtypes_date.asp).|  
|**EtwManifestProviderConfiguration**|Nödvändigt attribut:<br /><br /> **Provider** – GUID för händelse leverantören<br /><br /> Valfria attribut är:<br /><br /> - **scheduledTransferLogLevelFilter** – den minsta allvarlighets grad som ska överföras till ditt lagrings konto.<br /><br /> -                     **scheduledTransferPeriod** – intervallet mellan schemalagda överföringar till lagring avrundat uppåt till närmaste minut. Värdet är en [data typ för XML-varaktighet](https://www.w3schools.com/xml/schema_dtypes_date.asp).|  

## <a name="etweventsourceproviderconfiguration-element"></a>EtwEventSourceProviderConfiguration Element  
 Konfigurerar insamling av händelser som genereras från [EventSource-klassen](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). I följande tabell beskrivs underordnade element:  

|Element namn|Beskrivning|  
|------------------|-----------------|  
|**DefaultEvents**|Valfritt attribut:<br /><br /> **eventDestination** – namnet på den tabell där händelserna ska lagras|  
|**Händelse**|Nödvändigt attribut:<br /><br /> **ID** – händelsens ID.<br /><br /> Valfritt attribut:<br /><br /> **eventDestination** – namnet på den tabell där händelserna ska lagras|  

## <a name="etwmanifestproviderconfiguration-element"></a>EtwManifestProviderConfiguration Element  
 I följande tabell beskrivs underordnade element:  

|Element namn|Beskrivning|  
|------------------|-----------------|  
|**DefaultEvents**|Valfritt attribut:<br /><br /> **eventDestination** – namnet på den tabell där händelserna ska lagras|  
|**Händelse**|Nödvändigt attribut:<br /><br /> **ID** – händelsens ID.<br /><br /> Valfritt attribut:<br /><br /> **eventDestination** – namnet på den tabell där händelserna ska lagras|  

## <a name="metrics-element"></a>Mått element  
 Gör att du kan generera en prestanda räknar tabell som är optimerad för snabba frågor. I följande tabell beskrivs underordnade element:  

|Element namn|Beskrivning|  
|------------------|-----------------|  
|**MetricAggregation**|Nödvändigt attribut:<br /><br /> **scheduledTransferPeriod** – intervallet mellan schemalagda överföringar till lagring avrundat uppåt till närmaste minut. Värdet är en [data typ för XML-varaktighet](https://www.w3schools.com/xml/schema_dtypes_date.asp).|  

## <a name="performancecounters-element"></a>PerformanceCounters-element  
 Aktiverar insamling av prestanda räknare. I följande tabell beskrivs underordnade element:  

|Element namn|Beskrivning|  
|------------------|-----------------|  
|**PerformanceCounterConfiguration**|Följande attribut krävs:<br /><br /> -                     **counterSpecifier** – namnet på prestanda räknaren. Till exempel `\Processor(_Total)\% Processor Time`. Om du vill hämta en lista över prestanda räknare på värden kör du `typeperf`kommandot.<br /><br /> -                     **sampleRate** – hur ofta räknaren ska samplas.<br /><br /> Valfritt attribut:<br /><br /> **enhet** – enhets måttet för räknaren.|  

## <a name="performancecounterconfiguration-element"></a>PerformanceCounterConfiguration-element  
 I följande tabell beskrivs underordnade element:  

|Element namn|Beskrivning|  
|------------------|-----------------|  
|**antecknings**|Nödvändigt attribut:<br /><br /> **DisplayName** – visnings namnet för räknaren<br /><br /> Valfritt attribut:<br /><br /> **locale** – det språk som ska användas när räknarens namn visas|  

## <a name="windowseventlog-element"></a>WindowsEventLog-element  
 I följande tabell beskrivs underordnade element:  

|Element namn|Beskrivning|  
|------------------|-----------------|  
|**DataSource**|Händelse loggarna i Windows som ska samlas in. Nödvändigt attribut:<br /><br /> **namn** – XPath-frågan som beskriver de Windows-händelser som ska samlas in. Exempel:<br /><br /> `Application!*[System[(Level >= 3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level >= 3]]`<br /><br /> Om du vill samla in alla händelser anger du "*".|

