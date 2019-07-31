---
title: Azure-diagnostik 1,0-konfigurations schema
description: ENDAST relevant om du använder Azure SDK 2,4 och nedan med Azure Virtual Machines Virtual Machine Scale Sets, Service Fabric eller Cloud Services.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/15/2017
ms.author: robb
ms.subservice: diagnostic-extension
ms.openlocfilehash: ac2b79d670b803573a359dfc9f8738f972f2d9b5
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2019
ms.locfileid: "60237846"
---
# <a name="azure-diagnostics-10-configuration-schema"></a>Azure-diagnostik 1,0-konfigurations schema
> [!NOTE]
> Azure-diagnostik är den komponent som används för att samla in prestanda räknare och annan statistik från Azure Virtual Machines, Virtual Machine Scale Sets, Service Fabric och Cloud Services.  Den här sidan är bara relevant om du använder någon av dessa tjänster.
>

Azure-diagnostik används med andra Microsoft Diagnostics-produkter som Azure Monitor, som innehåller Application Insights och Log Analytics.

Den Azure-diagnostik konfigurations filen definierar värden som används för att initiera diagnostik-övervakaren. Den här filen används för att initiera diagnostiska konfigurations inställningar när diagnostikprogrammet startar.  

 Som standard installeras den Azure-diagnostik konfigurations schema filen i `C:\Program Files\Microsoft SDKs\Azure\.NET SDK\<version>\schemas` katalogen. Ersätt `<version>` med den installerade versionen av [Azure SDK](https://www.windowsazure.com/develop/downloads/).  

> [!NOTE]
>  Konfigurations filen för diagnostik används vanligt vis med start uppgifter som kräver att diagnostikdata samlas in tidigare under start processen. Mer information om hur du använder Azure-diagnostik finns i [samla in loggnings data med hjälp av Azure-diagnostik](assetId:///83a91c23-5ca2-4fc9-8df3-62036c37a3d7).  

## <a name="example-of-the-diagnostics-configuration-file"></a>Exempel på konfigurations filen för diagnostik  
 I följande exempel visas en typisk konfigurations fil för diagnostik:  

```xml  
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticMonitorConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"  
      configurationChangePollInterval="PT1M"  
      overallQuotaInMB="4096">  
   <DiagnosticInfrastructureLogs bufferQuotaInMB="1024"  
      scheduledTransferLogLevelFilter="Verbose"  
      scheduledTransferPeriod="PT1M" />  
   <Logs bufferQuotaInMB="1024"  
      scheduledTransferLogLevelFilter="Verbose"  
      scheduledTransferPeriod="PT1M" />  

   <Directories bufferQuotaInMB="1024"   
      scheduledTransferPeriod="PT1M">  

      <!-- These three elements specify the special directories   
           that are set up for the log types -->  
      <CrashDumps container="wad-crash-dumps" directoryQuotaInMB="256" />  
      <FailedRequestLogs container="wad-frq" directoryQuotaInMB="256" />  
      <IISLogs container="wad-iis" directoryQuotaInMB="256" />  

      <!-- For regular directories the DataSources element is used -->  
      <DataSources>  
         <DirectoryConfiguration container="wad-panther" directoryQuotaInMB="128">  
            <!-- Absolute specifies an absolute path with optional environment expansion -->  
            <Absolute expandEnvironment="true" path="%SystemRoot%\system32\sysprep\Panther" />  
         </DirectoryConfiguration>  
         <DirectoryConfiguration container="wad-custom" directoryQuotaInMB="128">  
            <!-- LocalResource specifies a path relative to a local   
                 resource defined in the service definition -->  
            <LocalResource name="MyLoggingLocalResource" relativePath="logs" />  
         </DirectoryConfiguration>  
      </DataSources>  
   </Directories>  

   <PerformanceCounters bufferQuotaInMB="512" scheduledTransferPeriod="PT1M">  
      <!-- The counter specifier is in the same format as the imperative   
           diagnostics configuration API -->  
      <PerformanceCounterConfiguration   
         counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT5S" />  
   </PerformanceCounters>  

   <WindowsEventLog bufferQuotaInMB="512"  
      scheduledTransferLogLevelFilter="Verbose"  
      scheduledTransferPeriod="PT1M">  
      <!-- The event log name is in the same format as the imperative   
           diagnostics configuration API -->  
      <DataSource name="System!*" />  
   </WindowsEventLog>  
</DiagnosticMonitorConfiguration>  
```  

## <a name="diagnosticsconfiguration-namespace"></a>DiagnosticsConfiguration-namnrymd  
 XML-namnområdet för konfigurations filen för diagnostik är:  

```  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  
```  

## <a name="schema-elements"></a>Schema element  
 Konfigurations filen för diagnostik innehåller följande element.


## <a name="diagnosticmonitorconfiguration-element"></a>DiagnosticMonitorConfiguration Element  
Elementet på den översta nivån i konfigurations filen för diagnostik.  

Attribut:

|Attribut  |type   |Obligatorisk| Standard | Beskrivning|  
|-----------|-------|--------|---------|------------|  
|**configurationChangePollInterval**|duration|Valfritt | PT1M| Anger det intervall som diagnostiken ska avsökas vid vid ändringar av diagnostisk konfiguration.|  
|**overallQuotaInMB**|unsignedInt|Valfritt| 4000 MB. Om du anger ett värde får det inte överskrida den här mängden |Den totala mängden fil system lagring som allokerats för alla loggnings buffertar.|  

## <a name="diagnosticinfrastructurelogs-element"></a>DiagnosticInfrastructureLogs-element  
Definierar buffertens konfiguration för de loggar som genereras av den underliggande diagnostik infrastrukturen.

Överordnat element: DiagnosticMonitorConfiguration-element.  

Attribut:

|Attribut|type|Beskrivning|  
|---------|----|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Valfri. Anger den maximala mängden fil system lagring som är tillgänglig för angivna data.<br /><br /> Standardvärdet är 0.|  
|**scheduledTransferLogLevelFilter**|sträng|Valfri. Anger den lägsta allvarlighets graden för logg poster som överförs. Standardvärdet är **odefinierat**. Andra möjliga värden är **utförlig**, **information**, **Varning**, **fel**och **kritisk**.|  
|**scheduledTransferPeriod**|duration|Valfri. Anger intervallet mellan schemalagda data överföringar, avrundade uppåt till närmaste minut.<br /><br /> Standardvärdet är PT0S.|  

## <a name="logs-element"></a>Loggar element  
 Definierar buffertens konfiguration för grundläggande Azure-loggar.

 Överordnat element: DiagnosticMonitorConfiguration-element.  

Attribut:  

|Attribut|type|Beskrivning|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Valfri. Anger den maximala mängden fil system lagring som är tillgänglig för angivna data.<br /><br /> Standardvärdet är 0.|  
|**scheduledTransferLogLevelFilter**|sträng|Valfri. Anger den lägsta allvarlighets graden för logg poster som överförs. Standardvärdet är **odefinierat**. Andra möjliga värden är **utförlig**, **information**, **Varning**, **fel**och **kritisk**.|  
|**scheduledTransferPeriod**|duration|Valfri. Anger intervallet mellan schemalagda data överföringar, avrundade uppåt till närmaste minut.<br /><br /> Standardvärdet är PT0S.|  

## <a name="directories-element"></a>Katalog element  
Definierar buffertens konfiguration för filbaserade loggar som du kan definiera.

Överordnat element: DiagnosticMonitorConfiguration-element.  


Attribut:  

|Attribut|type|Beskrivning|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Valfri. Anger den maximala mängden fil system lagring som är tillgänglig för angivna data.<br /><br /> Standardvärdet är 0.|  
|**scheduledTransferPeriod**|duration|Valfri. Anger intervallet mellan schemalagda data överföringar, avrundade uppåt till närmaste minut.<br /><br /> Standardvärdet är PT0S.|  

## <a name="crashdumps-element"></a>CrashDumps-element  
 Definierar katalogen för krasch dum par.

 Överordnat element: Katalog element.  

Attribut:  

|Attribut|type|Beskrivning|  
|---------------|----------|-----------------|  
|**fönster**|sträng|Namnet på behållaren där katalogens innehåll ska överföras.|  
|**directoryQuotaInMB**|unsignedInt|Valfri. Anger den maximala storleken på katalogen i megabyte.<br /><br /> Standardvärdet är 0.|  

## <a name="failedrequestlogs-element"></a>FailedRequestLogs-element  
 Definierar logg katalog för misslyckade begär Anden.

 Element för överordnade element kataloger.  

Attribut:  

|Attribut|type|Beskrivning|  
|---------------|----------|-----------------|  
|**fönster**|sträng|Namnet på behållaren där katalogens innehåll ska överföras.|  
|**directoryQuotaInMB**|unsignedInt|Valfri. Anger den maximala storleken på katalogen i megabyte.<br /><br /> Standardvärdet är 0.|  

##  <a name="iislogs-element"></a>IISLogs-element  
 Definierar logg katalogen för IIS.

 Element för överordnade element kataloger.  

Attribut:  

|Attribut|type|Beskrivning|  
|---------------|----------|-----------------|  
|**fönster**|sträng|Namnet på behållaren där katalogens innehåll ska överföras.|  
|**directoryQuotaInMB**|unsignedInt|Valfri. Anger den maximala storleken på katalogen i megabyte.<br /><br /> Standardvärdet är 0.|  

## <a name="datasources-element"></a>Data källa element  
 Definierar noll eller flera ytterligare logg kataloger.

 Överordnat element: Katalog element.

## <a name="directoryconfiguration-element"></a>DirectoryConfiguration-element  
 Definierar katalogen för de loggfiler som ska övervakas.

 Överordnat element: Data källans element.

Attribut:

|Attribut|type|Beskrivning|  
|---------------|----------|-----------------|  
|**fönster**|sträng|Namnet på behållaren där katalogens innehåll ska överföras.|  
|**directoryQuotaInMB**|unsignedInt|Valfri. Anger den maximala storleken på katalogen i megabyte.<br /><br /> Standardvärdet är 0.|  

## <a name="absolute-element"></a>Absolut element  
 Definierar en absolut sökväg till den katalog som ska övervakas med valfri miljö expansion.

 Överordnat element: DirectoryConfiguration-element.  

Attribut:  

|Attribut|type|Beskrivning|  
|---------------|----------|-----------------|  
|**Sökväg**|sträng|Obligatoriskt. Den absoluta sökvägen till den katalog som ska övervakas.|  
|**expandEnvironment**|boolean|Obligatoriskt. Om värdet är **True**expanderas miljövariabler i sökvägen.|  

## <a name="localresource-element"></a>LocalResource-element  
 Definierar en sökväg i förhållande till en lokal resurs som definierats i tjänst definitionen.

 Överordnat element: DirectoryConfiguration-element.  

Attribut:  

|Attribut|type|Beskrivning|  
|---------------|----------|-----------------|  
|**name**|sträng|Obligatoriskt. Namnet på den lokala resurs som innehåller den katalog som ska övervakas.|  
|**relativePath**|sträng|Obligatoriskt. Sökvägen i förhållande till den lokala resurs som ska övervakas.|  

## <a name="performancecounters-element"></a>PerformanceCounters-element  
 Definierar sökvägen till prestanda räknaren som ska samlas in.

 Överordnat element: DiagnosticMonitorConfiguration-element.


 Attribut:  

|Attribut|type|Beskrivning|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Valfri. Anger den maximala mängden fil system lagring som är tillgänglig för angivna data.<br /><br /> Standardvärdet är 0.|  
|**scheduledTransferPeriod**|duration|Valfri. Anger intervallet mellan schemalagda data överföringar, avrundade uppåt till närmaste minut.<br /><br /> Standardvärdet är PT0S.|  

## <a name="performancecounterconfiguration-element"></a>PerformanceCounterConfiguration-element  
 Definierar den prestanda räknare som ska samlas in.

 Överordnat element: PerformanceCounters-element.  

 Attribut:  

|Attribut|type|Beskrivning|  
|---------------|----------|-----------------|  
|**counterSpecifier**|sträng|Obligatoriskt. Sökvägen till prestanda räknaren som ska samlas in.|  
|**sampleRate**|duration|Obligatoriskt. Den hastighet med vilken prestanda räknaren ska samlas in.|  

## <a name="windowseventlog-element"></a>WindowsEventLog-element  
 Definierar de händelse loggar som ska övervakas.

 Överordnat element: DiagnosticMonitorConfiguration-element.

  Attribut:

|Attribut|type|Beskrivning|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Valfri. Anger den maximala mängden fil system lagring som är tillgänglig för angivna data.<br /><br /> Standardvärdet är 0.|  
|**scheduledTransferLogLevelFilter**|sträng|Valfri. Anger den lägsta allvarlighets graden för logg poster som överförs. Standardvärdet är **odefinierat**. Andra möjliga värden är **utförlig**, **information**, **Varning**, **fel**och **kritisk**.|  
|**scheduledTransferPeriod**|duration|Valfri. Anger intervallet mellan schemalagda data överföringar, avrundade uppåt till närmaste minut.<br /><br /> Standardvärdet är PT0S.|  

## <a name="datasource-element"></a>DataSource Element  
 Definierar händelse loggen som ska övervakas.

 Överordnat element: WindowsEventLog-element.  

 Attribut:

|Attribut|type|Beskrivning|  
|---------------|----------|-----------------|  
|**name**|sträng|Obligatoriskt. Ett XPath-uttryck som anger vilken logg som ska samlas in.|  

