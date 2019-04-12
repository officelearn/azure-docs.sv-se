---
title: Schema för Azure Diagnostics 1.0-konfiguration
description: Detta gäller endast om du använder Azure SDK 2.4 och nedan med Azure Virtual Machines, Virtual Machine Scale Sets, Service Fabric och Cloud Services.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/15/2017
ms.author: robb
ms.subservice: diagnostic-extension
ms.openlocfilehash: ac2b79d670b803573a359dfc9f8738f972f2d9b5
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/11/2019
ms.locfileid: "59492727"
---
# <a name="azure-diagnostics-10-configuration-schema"></a>Schema för Azure Diagnostics 1.0-konfiguration
> [!NOTE]
> Azure-diagnostik är den komponent som används för att samla in prestandaräknare och annan statistik från Azure Virtual Machines, Virtual Machine Scale Sets, Service Fabric och Cloud Services.  Den här sidan gäller endast om du använder någon av dessa tjänster.
>

Azure-diagnostik används med andra Microsoft-produkter för diagnostik som Azure Monitor, som innehåller Application Insights och Log Analytics.

Azure Diagnostics-konfigurationsfilen definierar värden som används för att initiera diagnostik övervakaren. Den här filen används för att initiera diagnostiska inställningar när diagnostik monitor startar.  

 Som standard installeras Azure Diagnostics schema konfigurationsfilen till den `C:\Program Files\Microsoft SDKs\Azure\.NET SDK\<version>\schemas` directory. Ersätt `<version>` med den installerade versionen av den [Azure SDK](https://www.windowsazure.com/develop/downloads/).  

> [!NOTE]
>  Diagnostik-konfigurationsfilen används vanligtvis med startåtgärder som kräver diagnostikdata ska samlas in tidigare i startprocessen. Läs mer om hur du använder Azure Diagnostics [samla in Data för loggning av med hjälp av Azure Diagnostics](assetId:///83a91c23-5ca2-4fc9-8df3-62036c37a3d7).  

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
 Diagnostik-konfigurationsfilen innehåller följande element.


## <a name="diagnosticmonitorconfiguration-element"></a>DiagnosticMonitorConfiguration Element  
Det översta elementet i konfigurationsfilen för diagnostik.  

Attribut:

|Attribut  |Typ   |Krävs| Standard | Beskrivning|  
|-----------|-------|--------|---------|------------|  
|**configurationChangePollInterval**|varaktighet|Valfri | PT1M| Anger intervall för diagnostikövervakare ska avsöka för diagnostiska konfigurationsändringar.|  
|**overallQuotaInMB**|unsignedInt|Valfri| 4000 MB. Om du anger ett värde får inte överskrida den mängden |Den totala mängden lagringsutrymme för filsystem tilldelat för alla buffertar för loggning.|  

## <a name="diagnosticinfrastructurelogs-element"></a>DiagnosticInfrastructureLogs Element  
Definierar konfigurationen av buffert för loggarna som genereras av den underliggande infrastrukturen för diagnostik.

Överordnade Element: DiagnosticMonitorConfiguration Element.  

Attribut:

|Attribut|Typ|Beskrivning|  
|---------|----|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Valfri. Anger den maximala mängden lagringsutrymme för filsystem som är tillgänglig för angivna data.<br /><br /> Standardvärdet är 0.|  
|**scheduledTransferLogLevelFilter**|sträng|Valfri. Anger den lägsta allvarlighetsgraden för loggposter som överförs. Standardvärdet är **Undefined**. Andra möjliga värden är **utförlig**, **Information**, **varning**, **fel**, och **kritisk**.|  
|**scheduledTransferPeriod**|varaktighet|Valfri. Anger intervallet mellan schemalagda överföring av data, avrundat uppåt till närmaste minut.<br /><br /> Standardvärdet är PT0S.|  

## <a name="logs-element"></a>Loggar Element  
 Definierar konfigurationen buffert för grundläggande Azure loggar.

 Överordnade element: DiagnosticMonitorConfiguration Element.  

Attribut:  

|Attribut|Typ|Beskrivning|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Valfri. Anger den maximala mängden lagringsutrymme för filsystem som är tillgänglig för angivna data.<br /><br /> Standardvärdet är 0.|  
|**scheduledTransferLogLevelFilter**|sträng|Valfri. Anger den lägsta allvarlighetsgraden för loggposter som överförs. Standardvärdet är **Undefined**. Andra möjliga värden är **utförlig**, **Information**, **varning**, **fel**, och **kritisk**.|  
|**scheduledTransferPeriod**|varaktighet|Valfri. Anger intervallet mellan schemalagda överföring av data, avrundat uppåt till närmaste minut.<br /><br /> Standardvärdet är PT0S.|  

## <a name="directories-element"></a>Directories Element  
Definierar konfigurationen av buffert för filbaserade loggar som du kan definiera.

Överordnade element: DiagnosticMonitorConfiguration Element.  


Attribut:  

|Attribut|Typ|Beskrivning|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Valfri. Anger den maximala mängden lagringsutrymme för filsystem som är tillgänglig för angivna data.<br /><br /> Standardvärdet är 0.|  
|**scheduledTransferPeriod**|varaktighet|Valfri. Anger intervallet mellan schemalagda överföring av data, avrundat uppåt till närmaste minut.<br /><br /> Standardvärdet är PT0S.|  

## <a name="crashdumps-element"></a>CrashDumps Element  
 Definierar kraschen Dumpar katalogen.

 Överordnade Element: Directories Element.  

Attribut:  

|Attribut|Typ|Beskrivning|  
|---------------|----------|-----------------|  
|**container**|sträng|Namnet på behållaren där innehållet i katalogen ska överföras.|  
|**directoryQuotaInMB**|unsignedInt|Valfri. Anger den maximala storleken för katalogen i megabyte.<br /><br /> Standardvärdet är 0.|  

## <a name="failedrequestlogs-element"></a>FailedRequestLogs Element  
 Definierar loggkatalogen misslyckade begäranden.

 Överordnade Element kataloger Element.  

Attribut:  

|Attribut|Typ|Beskrivning|  
|---------------|----------|-----------------|  
|**container**|sträng|Namnet på behållaren där innehållet i katalogen ska överföras.|  
|**directoryQuotaInMB**|unsignedInt|Valfri. Anger den maximala storleken för katalogen i megabyte.<br /><br /> Standardvärdet är 0.|  

##  <a name="iislogs-element"></a>IISLogs Element  
 Definierar IIS log-katalogen.

 Överordnade Element kataloger Element.  

Attribut:  

|Attribut|Typ|Beskrivning|  
|---------------|----------|-----------------|  
|**container**|sträng|Namnet på behållaren där innehållet i katalogen ska överföras.|  
|**directoryQuotaInMB**|unsignedInt|Valfri. Anger den maximala storleken för katalogen i megabyte.<br /><br /> Standardvärdet är 0.|  

## <a name="datasources-element"></a>DataSources Element  
 Definierar noll eller flera ytterligare log-kataloger.

 Överordnade Element: Directories Element.

## <a name="directoryconfiguration-element"></a>DirectoryConfiguration Element  
 Definierar katalogen för loggfiler som ska övervaka.

 Överordnade Element: DataSources Element.

Attribut:

|Attribut|Typ|Beskrivning|  
|---------------|----------|-----------------|  
|**container**|sträng|Namnet på behållaren där innehållet i katalogen ska överföras.|  
|**directoryQuotaInMB**|unsignedInt|Valfri. Anger den maximala storleken för katalogen i megabyte.<br /><br /> Standardvärdet är 0.|  

## <a name="absolute-element"></a>Absolute Element  
 Definierar en absolut sökväg på katalogen som ska övervakas med valfri miljö expansion.

 Överordnade Element: DirectoryConfiguration Element.  

Attribut:  

|Attribut|Typ|Beskrivning|  
|---------------|----------|-----------------|  
|**sökväg**|sträng|Krävs. Den absoluta sökvägen till katalogen som ska övervakas.|  
|**expandEnvironment**|boolesk|Krävs. Om inställd **SANT**, miljövariabler i sökvägen expanderas.|  

## <a name="localresource-element"></a>LocalResource Element  
 Definierar en sökväg i förhållande till en lokal resurs som definierats i tjänstdefinitionen för.

 Överordnade Element: DirectoryConfiguration Element.  

Attribut:  

|Attribut|Typ|Beskrivning|  
|---------------|----------|-----------------|  
|**namn**|sträng|Krävs. Namnet på den lokala resursen som innehåller katalogen som ska övervakas.|  
|**relativePath**|sträng|Krävs. Sökväg i förhållande till den lokala resursen du övervakar.|  

## <a name="performancecounters-element"></a>PerformanceCounters Element  
 Definierar sökvägen till prestandaräknaren för att samla in.

 Överordnade Element: DiagnosticMonitorConfiguration Element.


 Attribut:  

|Attribut|Typ|Beskrivning|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Valfri. Anger den maximala mängden lagringsutrymme för filsystem som är tillgänglig för angivna data.<br /><br /> Standardvärdet är 0.|  
|**scheduledTransferPeriod**|varaktighet|Valfri. Anger intervallet mellan schemalagda överföring av data, avrundat uppåt till närmaste minut.<br /><br /> Standardvärdet är PT0S.|  

## <a name="performancecounterconfiguration-element"></a>PerformanceCounterConfiguration Element  
 Definierar prestandaräknaren för att samla in.

 Överordnade Element: PerformanceCounters Element.  

 Attribut:  

|Attribut|Typ|Beskrivning|  
|---------------|----------|-----------------|  
|**counterSpecifier**|sträng|Krävs. Sökvägen till prestandaräknaren för att samla in.|  
|**sampleRate**|varaktighet|Krävs. Den hastighet med vilken prestandaräknaren ska samlas in.|  

## <a name="windowseventlog-element"></a>WindowsEventLog Element  
 Definierar händelseloggar för att övervaka.

 Överordnade Element: DiagnosticMonitorConfiguration Element.

  Attribut:

|Attribut|Typ|Beskrivning|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Valfri. Anger den maximala mängden lagringsutrymme för filsystem som är tillgänglig för angivna data.<br /><br /> Standardvärdet är 0.|  
|**scheduledTransferLogLevelFilter**|sträng|Valfri. Anger den lägsta allvarlighetsgraden för loggposter som överförs. Standardvärdet är **Undefined**. Andra möjliga värden är **utförlig**, **Information**, **varning**, **fel**, och **kritisk**.|  
|**scheduledTransferPeriod**|varaktighet|Valfri. Anger intervallet mellan schemalagda överföring av data, avrundat uppåt till närmaste minut.<br /><br /> Standardvärdet är PT0S.|  

## <a name="datasource-element"></a>DataSource Element  
 Definierar händelseloggen för att övervaka.

 Överordnade Element: WindowsEventLog Element.  

 Attribut:

|Attribut|Typ|Beskrivning|  
|---------------|----------|-----------------|  
|**namn**|sträng|Krävs. Ett XPath-uttryck som anger att samla in loggen.|  

