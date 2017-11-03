---
title: Lagra och visa diagnostiska Data i Azure Storage | Microsoft Docs
description: "Hämta Azure diagnostikdata till Azure Storage och visa den"
services: cloud-services
documentationcenter: .net
author: rboucher
manager: jwhit
editor: tysonn
ms.assetid: 18e0780d-43e7-41e4-b8e9-f1fb9a36eb03
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2016
ms.author: robb
ms.openlocfilehash: 374cc179e13c00e439415e3df16e0c6d5ccba5e3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="store-and-view-diagnostic-data-in-azure-storage"></a>Lagra och visa diagnostiska data i Azure Storage
Diagnostikdata lagras inte permanent om du överför den till Microsoft Azure storage-emulatorn eller till Azure-lagring. En gång i lagring, den kan visas med en av flera tillgängliga verktyg.

## <a name="specify-a-storage-account"></a>Ange ett lagringskonto
Du kan ange det lagringskonto som du vill använda i ServiceConfiguration.cscfg-filen. Kontoinformationen är definierad som en anslutningssträng i en konfigurationsinställning. I följande exempel visas standardanslutningssträngen som skapats för ett nytt Cloud Service-projekt i Visual Studio:

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
    </ConfigurationSettings>
```

Du kan ändra den här anslutningssträngen för att ange kontoinformationen för ett Azure storage-konto.

Beroende på vilken typ av diagnostiska data som samlas in, använder Azure-diagnostik Blob-tjänsten eller tabelltjänsten. I följande tabell visas de datakällor som är beständiga och deras format.

| Datakälla | Lagringsformat |
| --- | --- |
| Azure-loggar |Tabell |
| IIS 7.0-loggar |Blob |
| Azure Diagnostics infrastruktur loggar |Tabell |
| Kunde inte begära spårningsloggar |Blob |
| Windows-händelseloggar |Tabell |
| Prestandaräknare |Tabell |
| Krasch minnesdumpar |Blob |
| Anpassade fel |Blob |

## <a name="transfer-diagnostic-data"></a>Överför diagnostikdata
För SDK-2.5 och senare, kan det uppstå begäran att överföra diagnostikdata via konfigurationsfilen. Du kan överföra diagnostikdata med schemalagda intervall som anges i konfigurationen.

För SDK-2.4 och tidigare kan du begära för att överföra diagnostiska data via konfigurationsfilen som programmässigt. Den programmässiga metoden kan du göra på begäran-överföringar.

> [!IMPORTANT]
> När du överför diagnostikdata till ett Azure storage-konto innebära kostnader för lagringsresurser som använder din diagnostikdata.
> 
> 

## <a name="store-diagnostic-data"></a>Lagra diagnostikdata
Loggdata lagras i Blob eller tabell med följande namn:

**Tabeller**

* **WadLogsTable** - loggar som skrivs i kod med spårningslyssnaren.
* **WADDiagnosticInfrastructureLogsTable** -diagnostik ändringar av Övervakare och konfiguration.
* **WADDirectoriesTable** – kataloger som övervakar diagnostikövervakare.  Detta inkluderar IIS-loggar, IIS kunde inte loggar begäran och egna kataloger.  Platsen för blob-loggfilen anges i fältet behållare och RelativePath fältet är namnet på blob.  Fältet AbsolutePath indikerar platsen och namnet på filen som den fanns på den virtuella Azure-datorn.
* **WADPerformanceCountersTable** – prestandaräknare.
* **WADWindowsEventLogsTable** – Windows-händelseloggarna.

**Blobbar**

* **bomullstuss-kontroll-container** – (endast för SDK-2.4 och tidigare) innehåller XML-konfigurationsfiler som styr Azure-diagnostik.
* **bomullstuss-iis-failedreqlogfiles** – innehåller information från IIS kunde inte begära loggar.
* **bomullstuss-iis-loggfiler** – innehåller information om IIS-loggar.
* **”anpassad”** – en anpassade container baserat på Konfigurera kataloger som övervakas av diagnostiska övervakaren.  Namnet på den här blobbehållaren ska anges i WADDirectoriesTable.

## <a name="tools-to-view-diagnostic-data"></a>Verktyg för att visa diagnostikdata
Det finns flera verktyg för att visa data när den överförs till lagring. Exempel:

* Server Explorer i Visual Studio - om du har installerat Azure-verktyg för Microsoft Visual Studio, du kan använda Azure Storage-noden i Server Explorer visa skrivskyddad blob- och tabelldata från Azure storage-konton. Du kan visa data från ditt konto för lokal lagring emulatorn och även från storage-konton du har skapat för Azure. Mer information finns i [webbsökning och hantera lagringsresurser med Server Explorer](../vs-azure-tools-storage-resources-server-explorer-browse-manage.md).
* [Microsoft Azure Lagringsutforskaren](../vs-azure-tools-storage-manage-with-storage-explorer.md) är en fristående app som gör det enkelt att arbeta med Azure Storage-data i Windows, OSX och Linux.
* [Azure Management Studio](http://www.cerebrata.com/products/azure-management-studio/introduction) innehåller Azure Diagnostics Manager där du kan visa, hämta och hantera diagnostikdata som samlas in av program som körs på Azure.

## <a name="next-steps"></a>Nästa steg
[Spåra flödet i Cloud Services-program med Azure-diagnostik](cloud-services-dotnet-diagnostics-trace-flow.md)

