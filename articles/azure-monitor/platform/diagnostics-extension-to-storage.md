---
title: Lagra och visa diagnostikdata i Azure Storage
description: Hämta Azure diagnostics-data till Azure Storage och visa den
services: azure-monitor
author: jpconnock
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 08/01/2016
ms.author: jeconnoc
ms.component: diagnostic-extension
ms.openlocfilehash: f0d1a51a3c787bd4d9602eeaad9ac92eda7bacb7
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54103748"
---
# <a name="store-and-view-diagnostic-data-in-azure-storage"></a>Lagra och visa diagnostikdata i Azure Storage
Diagnostiska data lagras inte permanent såvida inte du överföra den till Microsoft Azure storage-emulatorn eller Azure storage. En gång i lagring, den kan visas med en av flera tillgängliga verktyg.

## <a name="specify-a-storage-account"></a>Ange ett lagringskonto
Du anger det lagringskonto som du vill använda i ServiceConfiguration.cscfg-filen. Informationen definieras som en anslutningssträng i en konfigurationsinställning. I följande exempel visas standardanslutningssträngen som skapats för en ny Cloud Service-projekt i Visual Studio:

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
    </ConfigurationSettings>
```

Du kan ändra den här anslutningssträngen för att ange kontoinformationen för ett Azure storage-konto.

Beroende på vilken typ av diagnostiska data som samlas in, använder Azure Diagnostics Blob service eller Table service. I följande tabell visar de datakällor som är beständiga och deras format.

| Datakälla | Lagringsformat |
| --- | --- |
| Azure loggar |Tabell |
| IIS 7.0-loggar |Blob |
| Azure Diagnostics-infrastrukturloggar |Tabell |
| Det gick inte begära spårningsloggar |Blob |
| Windows-händelseloggar |Tabell |
| Prestandaräknare |Tabell |
| Kraschdumpar |Blob |
| Anpassa felloggar |Blob |

## <a name="transfer-diagnostic-data"></a>Diagnostikdata för överföring
För SDK 2.5 och senare, kan förfrågan om att överföra diagnostikdata ske via konfigurationsfilen. Du kan överföra diagnostikdata med schemalagda intervall som anges i konfigurationen.

För SDK 2.4 och tidigare kan du begära för att överföra diagnostiska data igenom konfigurationsfilen som programmässigt. Den programmässiga metoden kan du göra på begäran-överföringar.

> [!IMPORTANT]
> När du överför diagnostiska data till ett Azure storage-konto medföra kostnader för lagringsresurser som använder dina diagnostiska data.
> 
> 

## <a name="store-diagnostic-data"></a>Diagnostikdata för Store
Loggdata som lagras i Blob eller Table storage med följande namn:

**Tabeller**

* **WadLogsTable** – loggar som skrivits i kod med hjälp av spårning lyssnaren.
* **WADDiagnosticInfrastructureLogsTable** -diagnostik och övervaka konfigurationsändringar.
* **WADDirectoriesTable** – kataloger som övervakar diagnostikövervakare.  Detta omfattar IIS-loggar, IIS kunde inte begära loggar och anpassade kataloger.  Platsen för loggfilen blob har angetts i fältet behållare och namnet på bloben är i fältet RelativePath.  Fältet AbsolutePath indikerar platsen och namnet på filen som den fanns på Azure-dator.
* **WADPerformanceCountersTable** – prestandaräknare.
* **WADWindowsEventLogsTable** – Windows-händelseloggar.

**Blobbar**

* **wad kontrollbehållare** – (endast för SDK 2.4 och tidigare) innehåller XML-filerna som styr Azure-diagnostik.
* **wad-iis-failedreqlogfiles** – innehåller information från IIS det gick inte begära loggar.
* **wad-iis-loggfiler** – innehåller information om IIS-loggar.
* **”anpassad”** – en anpassad behållare baserat på Konfigurera kataloger som övervakas av diagnostikövervakare.  Namnet på den här blobbehållaren kommer att anges i WADDirectoriesTable.

## <a name="tools-to-view-diagnostic-data"></a>Verktyg för att visa diagnostikdata
Det finns flera verktyg för att visa data när den överförs till lagring. Exempel:

* Server Explorer i Visual Studio - om du har installerat Azure Tools för Microsoft Visual Studio, du kan använda Azure Storage-noden i Server Explorer visa skrivskyddade blob- och tabelldata från Azure storage-konton. Du kan visa data från lokala emulatorn lagringskontot och även från storage-konton du har skapat för Azure. Mer information finns i [webbsökning och hantera lagringsresurser med Server Explorer](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage).
* [Microsoft Azure Lagringsutforskaren](../../vs-azure-tools-storage-manage-with-storage-explorer.md) är en fristående app som gör det enkelt att arbeta med Azure Storage-data i Windows, OSX och Linux.
* [Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) innehåller Azure Diagnostics Manager där du kan visa, ladda ned och hantera diagnostics-data som samlas in av programmen som körs på Azure.

## <a name="next-steps"></a>Nästa steg
[Spåra flödet i Cloud Services-program med Azure Diagnostics](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)

