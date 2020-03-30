---
title: Lagra och visa diagnostikdata i Azure Storage
description: Lär dig hur du samlar in Azure-diagnostikdata i ett Azure Storage-konto så att du kan visa dem med ett av flera tillgängliga verktyg.
services: azure-monitor
author: bwren
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 08/01/2016
ms.author: bwren
ms.subservice: diagnostic-extension
ms.openlocfilehash: 17430330d068fb55b45f073afecb8ba348286cb5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472679"
---
# <a name="store-and-view-diagnostic-data-in-azure-storage"></a>Lagra och visa diagnostikdata i Azure Storage
Diagnostikdata lagras inte permanent om du inte överför dem till Microsoft Azure-lagringsemulatorn eller till Azure-lagring. Väl i lager kan den ses med ett av flera tillgängliga verktyg.

## <a name="specify-a-storage-account"></a>Ange ett lagringskonto
Du anger det lagringskonto som du vill använda i filen ServiceConfiguration.cscfg. Kontoinformationen definieras som en anslutningssträng i en konfigurationsinställning. I följande exempel visas standardanslutningssträngen som skapats för ett nytt Molntjänstprojekt i Visual Studio:

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
    </ConfigurationSettings>
```

Du kan ändra den här anslutningssträngen för att ange kontoinformation för ett Azure-lagringskonto.

Beroende på vilken typ av diagnostikdata som samlas in använder Azure Diagnostics antingen Blob-tjänsten eller tabelltjänsten. I följande tabell visas de datakällor som sparats och deras format.

| Datakälla | Lagringsformat |
| --- | --- |
| Azure-loggar |Tabell |
| IIS 7.0 loggar |Blob |
| Azure Diagnostics-infrastrukturloggar |Tabell |
| Misslyckade spårningsloggar för begäran |Blob |
| Windows-händelseloggar |Tabell |
| Prestandaräknare |Tabell |
| Kraschdumpar |Blob |
| Anpassa felloggar |Blob |

## <a name="transfer-diagnostic-data"></a>Överföra diagnostikdata
För SDK 2.5 och senare kan begäran om överföring av diagnostikdata ske via konfigurationsfilen. Du kan överföra diagnostikdata med schemalagda intervall enligt konfigurationen.

För SDK 2.4 och tidigare kan du begära att överföra diagnostikdata via konfigurationsfilen samt programmässigt. Den programmatiska metoden kan du också göra on-demand överföringar.

> [!IMPORTANT]
> När du överför diagnostikdata till ett Azure-lagringskonto ådrar du dig kostnader för de lagringsresurser som diagnostikdata använder.
> 
> 

## <a name="store-diagnostic-data"></a>Lagra diagnostikdata
Loggdata lagras i antingen Blob- eller Table-lagring med följande namn:

**Tabeller**

* **WadLogsTable** - Loggar skrivna i kod med hjälp av spårningslyssnaren.
* **WADDiagnosticInfrastructureLogsTable** - Diagnostikövervakare och konfigurationsändringar.
* **WADDirectoriesTable** – Kataloger som diagnostikövervakaren övervakar.  Detta inkluderar IIS-loggar, IIS-misslyckade begärandeloggar och anpassade kataloger.  Platsen för blob-loggfilen anges i fältet Behållare och namnet på blobben finns i fältet RelativPath.  AbsolutePath-fältet anger platsen och namnet på filen som den fanns på den virtuella Azure-datorn.
* **WADPerformanceCountersTable** – Prestandaräknare.
* **WADWindowsEventLogsTable** – Windows-händelseloggar.

**Blobar**

* **wad-control-container** – (endast för SDK 2.4 och föregående) Innehåller XML-konfigurationsfiler som styr Azure-diagnostiken .
* **wad-iis-failedreqlogfiles** – Innehåller information från loggar för misslyckade begäranden i IIS.
* **wad-iis-logfiler** – Innehåller information om IIS-loggar.
* **"custom"** – En anpassad behållare som baseras på att konfigurera kataloger som övervakas av diagnostikövervakaren.  Namnet på den här blob-behållaren anges i WADDirectoriesTable.

## <a name="tools-to-view-diagnostic-data"></a>Verktyg för att visa diagnostikdata
Flera verktyg finns tillgängliga för att visa data när de har överförts till lagring. Ett exempel:

* Serverutforskaren i Visual Studio – Om du har installerat Azure Tools för Microsoft Visual Studio kan du använda noden Azure Storage i Server Explorer för att visa skrivskyddade blob- och tabelldata från dina Azure-lagringskonton. Du kan visa data från ditt lokala lagringsemulatorkonto och även från lagringskonton som du har skapat för Azure. Mer information finns i [Bläddra och hantera lagringsresurser med Server Explorer](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage).
* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) är en fristående app som gör att du enkelt kan arbeta med Azure Storage-data på Windows, OSX och Linux.
* [Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) innehåller Azure Diagnostics Manager som låter dig visa, ladda ned och hantera diagnostikdata som samlas in av programmen som körs på Azure.

## <a name="next-steps"></a>Efterföljande moment
[Spåra flödet i ett Cloud Services-program med Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)


