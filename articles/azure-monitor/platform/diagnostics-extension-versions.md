---
title: Versionshistorik för konfigurationsschema för Konfigurationsschema för Windows Azure Diagnostics-tillägg (WAD)
description: Relevant för att samla in perf-räknare i Virtuella Azure-datorer, VM-skalningsuppsättningar, serviceinfrastruktur och molntjänster.
ms.subservice: diagnostic-extension
ms.topic: reference
author: bwren
ms.author: bwren
ms.date: 01/29/2020
ms.openlocfilehash: 4dd91363cdebf18e6303238816e8269065a6a317
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672250"
---
# <a name="windows-azure-diagnostics-extension-wad-configuration-schema-versions-and-history"></a>Konfigurationsschemaversioner och historik för KONFIGURATIONSSCHEMA (Windows Azure Diagnostics Extension)
Den här artikeln innehåller versionshistoriken [för Azure Diagnostics-schemaversioner för Windows (WAD)](diagnostics-extension-overview.md) som levereras som en del av Microsoft Azure SDK.  


## <a name="azure-sdk-and-diagnostics-versions-shipping-chart"></a>Leveransdiagram för Azure SDK och diagnostikversioner  

|Azure SDK-version | Tilläggsversion av diagnostik | Modell|  
|------------------|-------------------------------|------|  
|1.x               |1.0                            |Plugin|  
|2.0 - 2.4         |1.0                            |Plugin|  
|2.5               |1.2                            |Förlängning|  
|2,6               |1.3                            |"|  
|2.7               |1.4                            |"|  
|2.8               |1.5                            |"|  
|2.9               |1.6                            |"|
|2.96              |1.7                            |"|
|2.96              |1.8                            |"|
|2.96              |1.8.1                          |"|
|2.96              |1.9                            |"|
|2.96              |1.11                           |"|


 Azure Diagnostics version 1.0 levererades först i en plug-in-modell – vilket innebär att när du installerade Azure SDK fick du den version av Azure-diagnostiken som levererades med den.  

 Från och med SDK 2.5 (diagnostik version 1.2) gick Azure-diagnostik till en tilläggsmodell. Verktygen för att använda nya funktioner var bara tillgängliga i nyare Azure SDK:er, men alla tjänster som använder Azure-diagnostik skulle hämta den senaste leveransversionen direkt från Azure. Alla som fortfarande använder SDK 2.5 läser till exempel in den senaste versionen som visas i föregående tabell, oavsett om de använder de nyare funktionerna.  

## <a name="schemas-index"></a>Schemaindex  
Olika versioner av Azure-diagnostik använder olika konfigurationsscheman. Schema 1.0 och 1.2 har inaktuellt. Mer information om version 1.3 och senare finns i [Diagnostik 1.3 och senare konfigurationsschema](diagnostics-extension-schema-windows.md)  

## <a name="version-history"></a>Versionshistorik

### <a name="diagnostics-extension-111"></a>Tillägg för diagnostik 1,11
Lade till stöd för Azure Monitor-diskbänken. Den här diskhon är endast tillämplig på prestandaräknare. Gör det möjligt att skicka prestandaräknare som samlats in på din virtuella dator, VMSS eller molntjänst till Azure Monitor som anpassade mått. Azure Monitor-diskbänken stöder:
* Hämtar alla prestandaräknare som skickas till Azure Monitor via [Azure Monitor-måttens API:er.](https://docs.microsoft.com/rest/api/monitor/metrics/list)
* Avisering om alla prestandaräknare som skickas till Azure Monitor via den nya [enhetliga aviseringsupplevelsen](../../azure-monitor/platform/alerts-overview.md) i Azure Monitor
* Behandla jokerteckenoperator i prestandaräknare som dimensionen "Instans" på måttet. Till exempel om du samlat in\*räknaren "LogicalDisk( )/DiskWrites/sec" skulle du kunna filtrera och dela på dimensionen "Instans" för att rita eller avisera på diskskrivandena/sek för varje logisk disk (C:, D:, etc.)

Definiera Azure Monitor som en ny mottagare i konfigurationen för diagnostiktillägg
```json
"SinksConfig": {
    "Sink": [
        {
            "name": "AzureMonitorSink",
            "AzureMonitor": {}
        },
    ]
}
```

```XML
<SinksConfig>  
  <Sink name="AzureMonitorSink">
      <AzureMonitor/>
  </Sink>
</SinksConfig>
```
> [!NOTE]
> Konfigurera Azure Monitor sink för klassiska virtuella datorer och Classic CLoud Service kräver fler parametrar som ska definieras i Diagnostik-tilläggets privata konfiguration.
>
> Mer information finns i dokumentationen till det detaljerade schemat för [diagnostiktillägg.](diagnostics-extension-schema-windows.md)

Därefter kan du konfigurera dina prestandaräknare som ska dirigeras till Azure Monitor Sink.
```json
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
```
```XML
<PerformanceCounters scheduledTransferPeriod="PT1M", sinks="AzureMonitorSink">  
  <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />  
</PerformanceCounters>
```

### <a name="diagnostics-extension-19"></a>Tillägg för diagnostik 1,9
Lade till Docker-stöd.


### <a name="diagnostics-extension-181"></a>Tillägg för diagnostik 1.8.1
Kan ange en SAS-token i stället för en lagringskontonyckel i den privata konfigurationen. Om en SAS-token tillhandahålls ignoreras lagringskontonyckeln.


```json
{
    "storageAccountName": "diagstorageaccount",
    "storageAccountEndPoint": "https://core.windows.net",
    "storageAccountSasToken": "{sas token}",
    "SecondaryStorageAccounts": {
        "StorageAccount": [
            {
                "name": "secondarydiagstorageaccount",
                "endpoint": "https://core.windows.net",
                "sasToken": "{sas token}"
            }
        ]
    }
}
```

```xml
<PrivateConfig>
    <StorageAccount name="diagstorageaccount" endpoint="https://core.windows.net" sasToken="{sas token}" />
    <SecondaryStorageAccounts>
        <StorageAccount name="secondarydiagstorageaccount" endpoint="https://core.windows.net" sasToken="{sas token}" />
    </SecondaryStorageAccounts>
</PrivateConfig>
```


### <a name="diagnostics-extension-18"></a>Tillägg för diagnostik 1,8
Lade till lagringstyp i PublicConfig. StorageType kan vara *Tabell,* *Blob*, *TableAndBlob*. *Tabellen* är standard.


```json
{
    "WadCfg": {
    },
    "StorageAccount": "diagstorageaccount",
    "StorageType": "TableAndBlob"
}
```

```xml
<PublicConfig>
    <WadCfg />
    <StorageAccount>diagstorageaccount</StorageAccount>
    <StorageType>TableAndBlob</StorageType>
</PublicConfig>
```


### <a name="diagnostics-extension-17"></a>Diagnostik förlängning 1,7
Lade till möjligheten att dirigera till EventHub.

### <a name="diagnostics-extension-15"></a>Tillägg för diagnostik 1,5
Lade till sinks-elementet och möjligheten att skicka diagnostikdata till [Application Insights](../../azure-monitor/app/cloudservices.md) vilket gör det enklare att diagnostisera problem i hela programmet samt system- och infrastrukturnivå.

### <a name="azure-sdk-26-and-diagnostics-extension-13"></a>Azure SDK 2.6 och diagnostiktillägg 1.3
För Cloud Service-projekt i Visual Studio gjordes följande ändringar. (Dessa ändringar gäller även för senare versioner av Azure SDK.)

* Den lokala emulatorn stöder nu diagnostik. Den här ändringen innebär att du kan samla in diagnostikdata och se till att ditt program skapar rätt spårningar medan du utvecklar och testar i Visual Studio. Anslutningssträngen `UseDevelopmentStorage=true` aktiverar insamling av diagnostikdata medan du kör ditt molntjänstprojekt i Visual Studio med hjälp av Azure-lagringsemulatorn. Alla diagnostikdata samlas in i lagringskontot (Development Storage).
* Anslutningssträngen för diagnostiklagringskonto (Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString) lagras återigen i tjänstkonfigurationsfilen (.cscfg). I Azure SDK 2.5 angavs diagnostiklagringskontot i filen diagnostics.wadcfgx.

Det finns några anmärkningsvärda skillnader mellan hur anslutningssträngen fungerade i Azure SDK 2.4 och tidigare och hur den fungerar i Azure SDK 2.6 och senare.

* I Azure SDK 2.4 och tidigare användes anslutningssträngen vid körning av diagnostikinsticksprogrammet för att hämta lagringskontoinformationen för överföring av diagnostikloggar.
* I Azure SDK 2.6 och senare använder Visual Studio anslutningssträngen diagnostik för att konfigurera diagnostiktillägget med lämplig lagringskontoinformation under publicering. Med anslutningssträngen kan du definiera olika lagringskonton för olika tjänstkonfigurationer som Visual Studio ska använda vid publicering. Men eftersom diagnostik plugin inte längre är tillgänglig (efter Azure SDK 2.5), kan CSCFG-filen i sig inte aktivera Diagnostiktillägget. Du måste aktivera tillägget separat via verktyg som Visual Studio eller PowerShell.
* För att förenkla processen för att konfigurera diagnostiktillägget med PowerShell innehåller paketutdata från Visual Studio även den offentliga konfigurations-XML:en för diagnostiktillägget för varje roll. Visual Studio använder anslutningssträngen för diagnostik för att fylla i lagringskontoinformationen som finns i den offentliga konfigurationen. De offentliga config-filerna skapas i mappen `PaaSDiagnostics.<RoleName>.PubConfig.xml`Tillägg och följer mönstret . Alla PowerShell-baserade distributioner kan använda det här mönstret för att mappa varje konfiguration till en roll.
* Anslutningssträngen i CSCFG-filen används också av Azure-portalen för att komma åt diagnostikdata så att den kan visas på fliken **Övervakning.** Anslutningssträngen behövs för att konfigurera tjänsten så att den visar utförliga övervakningsdata i portalen.

#### <a name="migrating-projects-to-azure-sdk-26-and-later"></a>Migrera projekt till Azure SDK 2.6 och senare
När du migrerar från Azure SDK 2.5 till Azure SDK 2.6 eller senare, om du har angett ett diagnostiklagringskonto i .wadcfgx-filen, kommer det att finnas kvar där. Om du vill dra nytta av flexibiliteten att använda olika lagringskonton för olika lagringskonfigurationer måste du lägga till anslutningssträngen manuellt i projektet. Om du migrerar ett projekt från Azure SDK 2.4 eller tidigare till Azure SDK 2.6 bevaras anslutningarna för diagnostik. Observera dock ändringarna i hur anslutningssträngar behandlas i Azure SDK 2.6 enligt föregående avsnitt.

#### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>Så här avgör Visual Studio lagringskontot för diagnostik
* Om en diagnostikanslutningssträng anges i CSCFG-filen använder Visual Studio den för att konfigurera diagnostiktillägget vid publicering och när du genererar xml-filer för offentlig konfiguration under förpackningen.
* Om ingen anslutningssträng för diagnostik anges i CSCFG-filen, återgår Visual Studio till att använda det lagringskonto som anges i WADCFGX-filen för att konfigurera diagnostiktillägget vid publicering och genererar xml-filer för offentlig konfiguration när förpackningen.
* Anslutningssträngen för diagnostik i CSCFG-filen har företräde framför lagringskontot i .wadcfgx-filen. Om en diagnostikanslutningssträng anges i CSCFG-filen använder Visual Studio det och ignorerar lagringskontot i .wadcfgx.

#### <a name="what-does-the-update-development-storage-connection-strings-checkbox-do"></a>Vad betyder "Uppdatera anslutningssträngar för utvecklingslagring..." kryssruta göra?
Kryssrutan för **Uppdatera utvecklingslagringsanslutningssträngar för diagnostik och cachelagring med Microsoft Azure-lagringskontoautentiseringsuppgifter när du publicerar till Microsoft Azure** ger dig ett bekvämt sätt att uppdatera alla anslutningssträngar för utvecklingslagringskonto med det Azure-lagringskonto som anges under publiceringen.

Anta till exempel att du markerar den här kryssrutan `UseDevelopmentStorage=true`och att anslutningssträngen för diagnostik anger . När du publicerar projektet i Azure uppdaterar Visual Studio automatiskt diagnostikanslutningssträngen med det lagringskonto som du angav i publiceringsguiden. Men om ett riktigt lagringskonto angavs som diagnostikanslutningssträngen används det kontot i stället.

### <a name="diagnostics-functionality-differences-between-azure-sdk-24-and-earlier-and-azure-sdk-25-and-later"></a>Skillnader i diagnostikfunktioner mellan Azure SDK 2.4 och tidigare och Azure SDK 2.5 och senare
Om du uppgraderar projektet från Azure SDK 2.4 till Azure SDK 2.5 eller senare bör du tänka på följande skillnader i diagnostikfunktioner.

* **Konfigurations-API:er är inaktuella** – Programmatisk konfiguration av diagnostik är tillgänglig i Azure SDK 2.4 eller tidigare versioner, men är föråldrad i Azure SDK 2.5 och senare. Om diagnostikkonfigurationen för närvarande definieras i kod måste du konfigurera om dessa inställningar från grunden i det migrerade projektet för att diagnostiken ska fortsätta fungera. Diagnostikkonfigurationsfilen för Azure SDK 2.4 är diagnostics.wadcfg och diagnostics.wadcfgx för Azure SDK 2.5 och senare.
* **Diagnostik för molntjänstprogram kan bara konfigureras på rollnivå, inte på instansnivå.**
* **Varje gång du distribuerar din app uppdateras diagnostikkonfigurationen** – Detta kan orsaka paritetsproblem om du ändrar diagnostikkonfigurationen från Server Explorer och sedan distribuerar om appen.
* **I Azure SDK 2.5 och senare konfigureras kraschdumpar i konfigurationsfilen för diagnostik, inte i kod** – Om du har kraschdumpar konfigurerade i kod måste du överföra konfigurationen manuellt från koden till konfigurationsfilen, eftersom kraschdumparna inte överförs under migreringen till Azure SDK 2.6.

