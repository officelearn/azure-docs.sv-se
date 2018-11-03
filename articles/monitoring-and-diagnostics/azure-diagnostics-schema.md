---
title: Azure Diagnostics-tillägget configuration version schemahistorik
description: Relevant för insamling av prestandaräknare i Azure Virtual Machines, VM Scale Sets, Service Fabric och Cloud Services.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: reference
ms.date: 09/20/2018
ms.author: robb
ms.component: diagnostic-extension
ms.openlocfilehash: 603ed12bf74f2d75543fd7e60834e695138c991d
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50963809"
---
# <a name="azure-diagnostics-extension-configuration-schema-versions-and-history"></a>Azure Diagnostics-tillägget configuration schemat versioner och historik
Den här sidan index Azure Diagnostics-tillägg-schemat versioner levereras som en del av Microsoft Azure SDK.  

> [!NOTE]
> Azure Diagnostics-tillägget är den komponent som används för att samla in prestandaräknare och annan statistik från:
> - Azure Virtual Machines
> - Virtual Machine Scale Sets
> - Service Fabric
> - Cloud Services
> - Nätverkssäkerhetsgrupper
>
> Den här sidan gäller endast om du använder någon av dessa tjänster.

Azure Diagnostics-tillägget används med andra Microsoft-produkter för diagnostik som Azure Monitor, Application Insights och Log Analytics. Mer information finns i [övervakning verktyg översikt över Microsoft](../azure-monitor/overview.md).

## <a name="azure-sdk-and-diagnostics-versions-shipping-chart"></a>Azure SDK och diagnostik versioner leverans diagram  

|Azure SDK-version | Diagnostik-tilläggsversion | Modell|  
|------------------|-------------------------------|------|  
|1.x               |1.0                            |plug-in|  
|2.0 - 2.4         |1.0                            |plug-in|  
|2.5               |1.2                            |Tillägget|  
|2.6               |1.3                            |"|  
|2.7               |1.4                            |"|  
|2.8               |1.5                            |"|  
|2.9               |1.6                            |"|
|2.96              |1.7                            |"|
|2.96              |1.8                            |"|
|2.96              |1.8.1                          |"|
|2.96              |1.9                            |"|
|2.96              |1.11                           |"|


 Azure Diagnostics version 1.0 första leverans i en plugin-programmet modell – vilket innebär att du fick versionen av Azure-diagnostik vid installation av Azure SDK levereras med den.  

 Från och med SDK 2.5 (diagnostics version 1.2), Azure-diagnostik ledde till en modell för tillägget. Verktyg för att använda nya funktioner bara var tillgängliga i nyare Azure SDK: er, men någon tjänst med hjälp av Azure-diagnostik skulle hämta den senaste versionen levereras direkt från Azure. Till exempel skulle alla som fortfarande använder SDK 2.5 laddas den senaste versionen som visas i föregående tabell, oavsett om de använder de nya funktionerna.  

## <a name="schemas-index"></a>Scheman index  
Olika versioner av Azure-diagnostik använder olika konfigurationsscheman.

[Diagnostik 1.0-konfigurationsschema](azure-diagnostics-schema-1dot0.md)  

[Diagnostik 1.2 konfigurationsschema](azure-diagnostics-schema-1dot2.md)  

[Diagnostik 1.3 och senare konfigurationsschema](azure-diagnostics-schema-1dot3-and-later.md)  

## <a name="version-history"></a>Versionshistorik

### <a name="diagnostics-extension-111"></a>Diagnostiktillägget 1.11
Tillagt stöd för Azure Monitor-mellanlagringsplatsen. Den här mottagare kan bara användas till prestandaräknare. Gör det möjligt att skicka prestandaräknare som samlats in på din VM, VMSS eller tjänst i molnet till Azure Monitor som anpassade mått. Azure Monitor-mellanlagringsplatsen har stöd för:
* Hämtar alla prestandaräknare skickas till Azure Monitor via den [Azure Monitor metrics API: er.](https://docs.microsoft.com/rest/api/monitor/metrics/list)
* Varna vid alla prestandaräknare skickas till Azure Monitor via den nya [unified aviseringsgränssnittet](monitoring-overview-unified-alerts.md) i Azure Monitor
* Behandla jokertecken operator i prestandaräknare som dimensionen ”instans” på din mått. Till exempel om du samlat in den ”logisk disk (\*) / DiskWrites/sek” räknare du skulle kunna filtrera och dela upp på dimensionen ”instans” för diagram eller Varna vid den Diskskrivningar/sek för varje logisk Disk (C:, D: osv.)

Definiera Azure Monitor som en ny mottagare i konfigurationen av diagnostiktillägget
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
> Konfigurera Azure Monitor-kanalmottagare för klassiska virtuella datorer och klassiskt Molntjänsten kräver fler parametrar ska definieras i diagnostik tilläggets privata konfiguration.
>
> För mer information kan du referens i [detaljerad diagnostik tillägget schemat dokumentation.](azure-diagnostics-schema-1dot3-and-later.md)

Du kan därefter konfigurera dina prestandaräknare som ska vidarebefordras till mottagare för Azure Monitor.
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

### <a name="diagnostics-extension-19"></a>Diagnostiktillägget 1.9
Docker-stöd har lagts till.


### <a name="diagnostics-extension-181"></a>Diagnostiktillägget 1.8.1
Ange en SAS-token i stället för en lagringskontonyckel i privata config. Om en SAS-token anges, ignoreras lagringskontonyckeln.


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


### <a name="diagnostics-extension-18"></a>Diagnostiktillägget 1.8
Har lagts till lagringstyp som ska PublicConfig. StorageType kan vara *tabell*, *Blob*, *TableAndBlob*. *Tabellen* är standard.


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


### <a name="diagnostics-extension-17"></a>Diagnostiktillägget 1.7
Lagt till möjligheten att dirigera till EventHub.

### <a name="diagnostics-extension-15"></a>Diagnostiktillägget 1.5
Lagt till elementet mottagare och möjligheten att skicka diagnostikdata till [Application Insights](../application-insights/app-insights-cloudservices.md) vilket gör det enklare att diagnostisera problem i ditt program, samt nivån system och infrastruktur.

### <a name="azure-sdk-26-and-diagnostics-extension-13"></a>Azure SDK 2.6 och diagnostik-tillägg 1.3
Följande ändringar har gjorts för Cloud Service-projekt i Visual Studio. (Dessa ändringar gäller även för senare versioner av Azure SDK.)

* Den lokala emulatorn har nu stöd för diagnostik. Den här ändringen innebär att du kan samla in diagnostikdata och se till att programmet skapar rätt spårningarna när du utvecklar och testar i Visual Studio. Anslutningssträngen `UseDevelopmentStorage=true` aktiverar diagnostik datainsamling när du kör cloud service-projekt i Visual Studio med hjälp av Azure storage-emulatorn. Alla diagnostics-data som samlas in i storage-konto (Utvecklingslagring).
* Den diagnostik anslutningssträngen för lagringskonto (Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString) lagras igen i tjänstekonfigurationsfilen (.cscfg). Lagringskonto för startdiagnostik har angetts i filen diagnostics.wadcfgx i Azure SDK 2.5.

Det finns några viktiga skillnader mellan hur anslutningssträngen fungerade i Azure SDK 2.4 och tidigare och hur det fungerar i Azure SDK 2.6 och senare.

* I Azure SDK 2.4 och tidigare användes anslutningssträngen vid körning av diagnostik-plugin-programmet att hämta information om storage-konto för att överföra diagnostikloggar.
* I Azure SDK 2.6 och senare, använder Visual Studio anslutningssträngen diagnostik för att konfigurera diagnostiktillägget med lämpliga lagringskontoinformation under publiceringen. Anslutningssträngen kan du definiera olika lagringskonton för olika konfigurationer som Visual Studio ska använda när du publicerar. Eftersom diagnostik plugin-programmet inte längre är tillgängliga (efter Azure SDK 2.5) kan inte .cscfg-filen påverkar i sig aktivera Diagnostiktillägget. Du måste aktivera tillägget separat via verktyg som Visual Studio eller PowerShell.
* Paketet utdata från Visual Studio innehåller också den offentliga konfigurationen-XML för diagnostics-tillägg för varje roll för att förenkla konfigureringen av diagnostiktillägget med PowerShell. Visual Studio använder anslutningssträngen diagnostik för att fylla i informationen om lagring finns i den offentliga konfigurationen. De offentliga konfigurationsfilerna skapas i mappen tillägg och följer mönstret PaaSDiagnostics. <RoleName>. PubConfig.xml. PowerShell-baserade distributioner kan använda det här mönstret för att mappa varje konfiguration tilldelas en roll.
* Anslutningssträngen i .cscfg-filen används också av Azure portal på dataåtkomst diagnostik så visas den i den **övervakning** fliken. Strängen som behövs för att konfigurera tjänsten för att visa utförlig övervakningsdata i portalen.

#### <a name="migrating-projects-to-azure-sdk-26-and-later"></a>Migrerar projekt till Azure SDK 2.6 och senare
När du migrerar från Azure SDK 2.5 Azure SDK 2.6 eller senare om du hade en diagnostiklagringskonto som anges i filen .wadcfgx, sedan förblir den det. Om du vill dra nytta av flexibiliteten i att använda olika konton för olika lagringskonfigurationer, måste du manuellt lägger till anslutningssträngen i projektet. Om du migrerar ett projekt från Azure SDK 2.4 eller tidigare till Azure SDK 2.6, bevaras anslutningssträngar för diagnostik. Observera dock att ändras i hur anslutningssträngar behandlas på Azure SDK 2.6 som anges i föregående avsnitt.

#### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>Hur Visual Studio anger lagringskonto för diagnostik
* Om en anslutningssträng för diagnostik har angetts i .cscfg-filen använder den för att konfigurera diagnostics-tillägg när du publicerar och när du genererar offentliga XML-konfigurationsfilerna under paketering i Visual Studio.
* Om inga diagnostik anslutningssträngen har angetts i .cscfg-filen, sedan återgår Visual Studio till att använda storage-konto som anges i filen .wadcfgx för att konfigurera diagnostiktillägget när du publicerar och generera xml-filer för offentliga konfiguration När paketering.
* Diagnostik-anslutningssträngen i .cscfg-filen har företräde framför storage-konto i filen .wadcfgx. Om en anslutningssträng för diagnostik har angetts i .cscfg-filen, Visual Studio använder som och ignorerar lagringskontot i .wadcfgx.

#### <a name="what-does-the-update-development-storage-connection-strings-checkbox-do"></a>Vad gör kryssrutan ”Uppdatera utveckling storage-anslutningssträngar...”?
Kryssrutan för **uppdatera utveckling storage-anslutningssträngar för diagnostik- och cachelagring med autentiseringsuppgifterna för Microsoft Azure storage-konto när du publicerar till Microsoft Azure** blir det lättare att uppdatera alla utveckling lagringskontots anslutningssträngar med Azure storage-kontot som angavs vid publicering.

Anta exempelvis att du väljer den här kryssrutan och diagnostik anslutningssträngen anger `UseDevelopmentStorage=true`. När du publicerar projektet till Azure, uppdateras automatiskt diagnostik anslutningssträngen med storage-konto som du angav i guiden Publicera i Visual Studio. Men om en verklig storage-konto har angetts som anslutningssträng för diagnostik används detta konto i stället.

### <a name="diagnostics-functionality-differences-between-azure-sdk-24-and-earlier-and-azure-sdk-25-and-later"></a>Diagnostik funktioner skillnader mellan Azure SDK 2.4 och tidigare och Azure SDK 2.5 och senare
Om du uppgraderar ditt projekt från Azure SDK 2.4 till Azure SDK 2.5 eller senare, bör du ha i åtanke följande diagnostik funktioner skillnader.

* **Konfiguration av API: er är inaktuella** – programkonfiguration av diagnostik är tillgänglig i Azure SDK 2.4 eller tidigare versioner, men är inaktuell i Azure SDK 2.5 och senare. Om din diagnostik-konfiguration har definierats i koden, måste du konfigurera om inställningarna från början i migrerade projektet för diagnostik kan fortsätta att arbeta. Konfigurationsfilen diagnostik för Azure SDK 2.4 är diagnostics.wadcfg och diagnostics.wadcfgx för Azure SDK 2.5 och senare.
* **Diagnostik för molnprogram för tjänsten kan endast konfigureras på rollnivå, inte på instansnivå.**
* **Varje gång som du har distribuerat din app diagnostikkonfigurationen uppdateras** – om du ändrar konfigurationen diagnostik från Server Explorer och distribuera om din app kan det medföra problem för paritet.
* **I Azure SDK 2.5 och senare, kraschdumpar är konfigurerade i inte i kod i konfigurationsfilen diagnostik** – om du har kraschdumpar som konfigurerats i koden, måste du manuellt överföra konfigurationen från kod till konfigurationsfil, eftersom den kraschdumpar överförs inte under migreringen till Azure SDK 2.6.
