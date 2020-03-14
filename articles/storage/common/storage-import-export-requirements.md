---
title: Krav för tjänsten Azure Import/Export | Microsoft Docs
description: Förstå kraven på programvara och maskinvara för tjänsten Azure Import/Export.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 08/12/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 58997b20c01f33037a5e5e149caa59e1630373ff
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79255319"
---
# <a name="azure-importexport-system-requirements"></a>Azure Import/Export-systemkrav

Den här artikeln beskriver viktiga för din Azure Import/Export-tjänst. Vi rekommenderar att du läser informationen noggrant innan du använder Import/Export-tjänsten och sedan refererar tillbaka till det som behövs under åtgärden.

## <a name="supported-operating-systems"></a>Operativsystem som stöds

För att förbereda hård diskarna med WAImportExport-verktyget stöds följande **64-bitars operativ system som stöder BitLocker-diskkryptering** .


|Plattform |Version |
|---------|---------|
|Windows     | Windows 7 Enterprise, Windows 7 Ultimate <br> Windows 8 Pro, Windows 8 Enterprise, Windows 8.1 Pro, Windows 8.1 Enterprise <br> Windows 10        |
|Windows Server     |Windows Server 2008 R2 <br> Windows Server 2012, Windows Server 2012 R2         |

## <a name="other-required-software-for-windows-client"></a>Annan nödvändig programvara för Windows-klienten

|Plattform |Version |
|---------|---------|
|.NET Framework    | 4.5.1       |
| BitLocker        |  _          |


## <a name="supported-storage-accounts"></a>Lagringskonton som stöds

Azure Import/Export-tjänsten stöder följande typer av lagringskonton:

- Standard Generell användning v2-lagrings konton (rekommenderas för de flesta scenarier)
- Blob Storage-konton
- Generell användning v1-lagrings konton (både klassiska eller Azure Resource Manager distributioner),

Mer information om lagrings konton finns i [Översikt över Azure Storage-konton](storage-account-overview.md).

Varje jobb kan användas för att överföra data till eller från endast en storage-konto. Med andra ord kan inte en enda import/export-jobbet omfatta över flera lagringskonton. Information om hur du skapar ett nytt lagrings konto finns i [så här skapar du ett lagrings konto](storage-account-create.md).

> [!IMPORTANT]
> Azure import export-tjänsten stöder inte lagrings konton där funktionen för [Virtual Network tjänst slut punkter](../../virtual-network/virtual-network-service-endpoints-overview.md) har Aktiver ATS. 

## <a name="supported-storage-types"></a>Lagringstyper som stöds

Följande lista över lagringstyper stöds med Azure Import/Export-tjänsten.


|Jobb  |Storage-tjänst |Stöds  |Stöds inte  |
|---------|---------|---------|---------|
|Importera     |  Azure Blob Storage <br><br> Azure-fillagring       | Blockblobbar och sidblobbar som stöds <br><br> Filer som stöds          |
|Exportera     |   Azure Blob Storage       | Blockblobar, sidblobar och Lägg till blobbar som stöds         | Azure Files som inte stöds


## <a name="supported-hardware"></a>Maskinvara som stöds

För tjänsten Azure Import/Export behöver du stöds diskar för att kopiera data.

### <a name="supported-disks"></a>Diskar som stöds

Följande lista över diskar stöds för användning med Import/Export-tjänsten.


|Disktyp  |Storlek  |Stöds |
|---------|---------|---------|
|SSD    |   2,5-tums      |SATA III          |
|HDD     |  2,5-tums<br>3,5-tums       |SATA II, SATA III         |

Följande disk typer stöds inte:
- USBs.
- Extern hård disk med inbyggd USB-adapter.
- Diskar som är i höljet för en extern hård disk.

En enda import/export-jobbet kan ha:
- Högst 10 HDD/SSD-enheter.
- En blandning av HDD/SSD med valfri storlek.

Stort antal enheter kan spridas till flera jobb och det finns inga gränser för antalet jobb som kan skapas. För importjobb bearbetas bara den första datavolymen på enheten. Datavolymen måste vara formaterad med NTFS.

När förbereda hårddiskar och kopiera data med hjälp av verktyget WAImportExport, kan du använda externa USB-adaptrar. Den mest startklara USB 3.0 eller senare efter adaptrar ska fungera.


## <a name="next-steps"></a>Nästa steg

* [Konfigurera verktyget WAImportExport](storage-import-export-tool-how-to.md)
* [Överföra data med kommandoradsverktyget AzCopy](storage-use-azcopy.md)
* [Exempel på Azure import-export REST API](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/)
