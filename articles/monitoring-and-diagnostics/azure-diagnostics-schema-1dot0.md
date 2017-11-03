---
title: Azure Diagnostics 1.0 Konfigurationsschemat | Microsoft Docs
description: "Detta gäller endast om du använder Azure SDK 2.4 och under med Azure virtuella datorer, virtuella datorer, Service Fabric eller molntjänster."
services: monitoring-and-diagnostics
documentationcenter: .net
author: rboucher
manager: carmonm
editor: 
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/15/2017
ms.author: robb
ms.openlocfilehash: a8fdfb52d5091d3fc9779657737c7430fcfada51
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-diagnostics-10-configuration-schema"></a>Azure Diagnostics 1.0 Konfigurationsschemat
> [!NOTE]
> Azure Diagnostics är en komponent som används för att samla in prestandaräknare och annan statistik från Azure virtuella datorer, virtuella datorer, Service Fabric och Cloud Services.  Den här sidan gäller endast om du använder någon av dessa tjänster.
>

Azure Diagnostics används tillsammans med andra produkter från Microsoft diagnostics som Azure-Monitor, Application Insights och logganalys.

Azure-diagnostik konfigurationsfilen definierar värden som används för att initiera diagnostik övervakaren. Den här filen används för att initiera diagnostiska konfigurationsinställningar när diagnostik monitor startar.  

 Som standard installeras schemat för Azure-diagnostik konfigurationsfilen till den `C:\Program Files\Microsoft SDKs\Azure\.NET SDK\<version>\schemas` directory. Ersätt `<version>` med den installerade versionen av den [Azure SDK](http://www.windowsazure.com/develop/downloads/).  

> [!NOTE]
>  Konfigurationsfilen diagnostik används vanligtvis med startåtgärder som kräver diagnostikdata ska samlas in tidigare i startprocessen. Mer information om hur du använder Azure-diagnostik finns [samla in Data för loggning med hjälp av Azure-diagnostik](assetId:///83a91c23-5ca2-4fc9-8df3-62036c37a3d7).  

## <a name="example-of-the-diagnostics-configuration-file"></a>Exempel på konfigurationsfilen diagnostik  
 I följande exempel visas en typisk diagnostik konfigurationsfil:  

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

## <a name="diagnosticsconfiguration-namespace"></a>DiagnosticsConfiguration Namespace  
 XML-namnområdet för diagnostik-konfigurationsfilen är:  

```  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  
```  

## <a name="schema-elements"></a>Schemaelement  
 Diagnostik konfigurationsfilen innehåller följande element.


## <a name="diagnosticmonitorconfiguration-element"></a>DiagnosticMonitorConfiguration Element  
Det översta elementet i konfigurationsfilen diagnostik.  

Attribut:

|Attribut  |Typ   |Krävs| Standard | Beskrivning|  
|-----------|-------|--------|---------|------------|  
|**configurationChangePollInterval**|Varaktighet|Valfri | PT1M| Anger intervallet i diagnostikövervakare ska avsöka för diagnostiska konfigurationsändringar.|  
|**overallQuotaInMB**|unsignedInt|Valfri| 4000 MB. Om du anger ett värde får inte överskrida den här mängden |Den totala mängden filen systemlagringsutrymme som allokerats för alla loggning buffertar.|  

## <a name="diagnosticinfrastructurelogs-element"></a>DiagnosticInfrastructureLogs Element  
Definierar buffert konfigurationen för de loggar som genereras av den underliggande diagnostik-infrastrukturen.

Överordnade Element: [DiagnosticMonitorConfiguration elementet](#DiagnosticMonitorConfiguration).  

Attribut:

|Attribut|Typ|Beskrivning|  
|---------|----|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Valfri. Anger maximal mängd fillagring för system som är tillgängliga för angivna data.<br /><br /> Standardvärdet är 0.|  
|**scheduledTransferLogLevelFilter**|Sträng|Valfri. Anger den lägsta allvarlighetsgraden för loggposter som överförs. Standardvärdet är **Undefined**. Andra möjliga värden är **utförlig**, **Information**, **varning**, **fel**, och **kritisk**.|  
|**scheduledTransferPeriod**|Varaktighet|Valfri. Anger intervallet mellan schemalagda dataöverföringar, avrundat till närmaste minut.<br /><br /> Standardvärdet är PT0S.|  

## <a name="logs-element"></a>Loggar Element  
 Definierar konfigurationen buffert för grundläggande Azure loggar.

 Överordnade element: [DiagnosticMonitorConfiguration elementet](#DiagnosticMonitorConfiguration).  

Attribut:  

|Attribut|Typ|Beskrivning|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Valfri. Anger maximal mängd fillagring för system som är tillgängliga för angivna data.<br /><br /> Standardvärdet är 0.|  
|**scheduledTransferLogLevelFilter**|Sträng|Valfri. Anger den lägsta allvarlighetsgraden för loggposter som överförs. Standardvärdet är **Undefined**. Andra möjliga värden är **utförlig**, **Information**, **varning**, **fel**, och **kritisk**.|  
|**scheduledTransferPeriod**|Varaktighet|Valfri. Anger intervallet mellan schemalagda dataöverföringar, avrundat till närmaste minut.<br /><br /> Standardvärdet är PT0S.|  

## <a name="directories-element"></a>Kataloger Element  
Definierar konfigurationen för filbaserade loggar som du kan definiera buffert.

Överordnade element: [DiagnosticMonitorConfiguration elementet](#DiagnosticMonitorConfiguration).  


Attribut:  

|Attribut|Typ|Beskrivning|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Valfri. Anger maximal mängd fillagring för system som är tillgängliga för angivna data.<br /><br /> Standardvärdet är 0.|  
|**scheduledTransferPeriod**|Varaktighet|Valfri. Anger intervallet mellan schemalagda dataöverföringar, avrundat till närmaste minut.<br /><br /> Standardvärdet är PT0S.|  

## <a name="crashdumps-element"></a>CrashDumps Element  
 Definierar krascher Dumpar katalogen.

 Överordnade Element: [kataloger elementet](#Directories).  

Attribut:  

|Attribut|Typ|Beskrivning|  
|---------------|----------|-----------------|  
|**behållaren**|Sträng|Namnet på behållaren där innehållet i katalogen ska överföras.|  
|**directoryQuotaInMB**|unsignedInt|Valfri. Anger den maximala storleken för katalogen i megabyte.<br /><br /> Standardvärdet är 0.|  

## <a name="failedrequestlogs-element"></a>FailedRequestLogs Element  
 Definierar loggkatalogen misslyckade begäranden.

 Överordnade Element [kataloger elementet](#Directories).  

Attribut:  

|Attribut|Typ|Beskrivning|  
|---------------|----------|-----------------|  
|**behållaren**|Sträng|Namnet på behållaren där innehållet i katalogen ska överföras.|  
|**directoryQuotaInMB**|unsignedInt|Valfri. Anger den maximala storleken för katalogen i megabyte.<br /><br /> Standardvärdet är 0.|  

##  <a name="iislogs-element"></a>IISLogs Element  
 Definierar loggkatalogen IIS.

 Överordnade Element [kataloger elementet](#Directories).  

Attribut:  

|Attribut|Typ|Beskrivning|  
|---------------|----------|-----------------|  
|**behållaren**|Sträng|Namnet på behållaren där innehållet i katalogen ska överföras.|  
|**directoryQuotaInMB**|unsignedInt|Valfri. Anger den maximala storleken för katalogen i megabyte.<br /><br /> Standardvärdet är 0.|  

## <a name="datasources-element"></a>Datakällor Element  
 Definierar noll eller flera ytterligare log-kataloger.

 Överordnade Element: [kataloger elementet](#Directories).

## <a name="directoryconfiguration-element"></a>DirectoryConfiguration Element  
 Definierar katalogen loggfiler för att övervaka.

 Överordnade Element: [datakällor elementet](#DataSources).

Attribut:

|Attribut|Typ|Beskrivning|  
|---------------|----------|-----------------|  
|**behållaren**|Sträng|Namnet på behållaren där innehållet i katalogen ska överföras.|  
|**directoryQuotaInMB**|unsignedInt|Valfri. Anger den maximala storleken för katalogen i megabyte.<br /><br /> Standardvärdet är 0.|  

## <a name="absolute-element"></a>Absolut Element  
 Definierar en absolut sökväg på katalogen som ska övervakas med valfritt miljö expandering.

 Överordnade Element: [DirectoryConfiguration elementet](#DirectoryConfiguration).  

Attribut:  

|Attribut|Typ|Beskrivning|  
|---------------|----------|-----------------|  
|**sökväg**|Sträng|Krävs. Den absoluta sökvägen till katalogen som ska övervakas.|  
|**expandEnvironment**|Booleskt värde|Krävs. Om värdet **SANT**, miljövariabler i sökvägen expanderas.|  

## <a name="localresource-element"></a>LocalResource Element  
 Definierar en sökväg i förhållande till en lokal resurs som definierats i tjänstdefinitionen.

 Överordnade Element: [DirectoryConfiguration elementet](#DirectoryConfiguration).  

Attribut:  

|Attribut|Typ|Beskrivning|  
|---------------|----------|-----------------|  
|**Namn**|Sträng|Krävs. Namnet på den lokala resursen som innehåller katalogen som ska övervakas.|  
|**relativePath**|Sträng|Krävs. Sökväg i förhållande till den lokala resursen du övervakar.|  

## <a name="performancecounters-element"></a>PerformanceCounters elementet  
 Definierar sökvägen till prestandaräknaren för att samla in.

 Överordnade Element: [DiagnosticMonitorConfiguration elementet](#DiagnosticMonitorConfiguration).


 Attribut:  

|Attribut|Typ|Beskrivning|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Valfri. Anger maximal mängd fillagring för system som är tillgängliga för angivna data.<br /><br /> Standardvärdet är 0.|  
|**scheduledTransferPeriod**|Varaktighet|Valfri. Anger intervallet mellan schemalagda dataöverföringar, avrundat till närmaste minut.<br /><br /> Standardvärdet är PT0S.|  

## <a name="performancecounterconfiguration-element"></a>PerformanceCounterConfiguration Element  
 Definierar prestandaräknaren för att samla in.

 Överordnade Element: [PerformanceCounters elementet](#PerformanceCounters).  

 Attribut:  

|Attribut|Typ|Beskrivning|  
|---------------|----------|-----------------|  
|**counterSpecifier**|Sträng|Krävs. Sökvägen till prestandaräknaren för att samla in.|  
|**sampleRate**|Varaktighet|Krävs. Den hastighet med vilken prestandaräknaren ska samlas in.|  

## <a name="windowseventlog-element"></a>WindowsEventLog Element  
 Definierar händelseloggar för att övervaka.

 Överordnade Element: [DiagnosticMonitorConfiguration elementet](#DiagnosticMonitorConfiguration).

  Attribut:

|Attribut|Typ|Beskrivning|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Valfri. Anger maximal mängd fillagring för system som är tillgängliga för angivna data.<br /><br /> Standardvärdet är 0.|  
|**scheduledTransferLogLevelFilter**|Sträng|Valfri. Anger den lägsta allvarlighetsgraden för loggposter som överförs. Standardvärdet är **Undefined**. Andra möjliga värden är **utförlig**, **Information**, **varning**, **fel**, och **kritisk**.|  
|**scheduledTransferPeriod**|Varaktighet|Valfri. Anger intervallet mellan schemalagda dataöverföringar, avrundat till närmaste minut.<br /><br /> Standardvärdet är PT0S.|  

## <a name="datasource-element"></a>DataSource-Element  
 Definierar händelseloggen för att övervaka.

 Överordnade Element: [WindowsEventLog elementet](#windowsEventLog).  

 Attribut:

|Attribut|Typ|Beskrivning|  
|---------------|----------|-----------------|  
|**Namn**|Sträng|Krävs. Ett XPath-uttryck som anger att loggen ska samlas in.|  
