---
title: Krav för Azure Import/Export-tjänsten | Microsoft-dokument
description: Förstå programvaru- och maskinvarukraven för Azure Import/Export-tjänsten.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 08/12/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 58997b20c01f33037a5e5e149caa59e1630373ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255319"
---
# <a name="azure-importexport-system-requirements"></a>Systemkrav för Azure Import/Export

I den här artikeln beskrivs de viktiga kraven för din Azure Import/Export-tjänst. Vi rekommenderar att du granskar informationen noggrant innan du använder tjänsten Importera/exportera och sedan hänvisar tillbaka till den efter behov under åtgärden.

## <a name="supported-operating-systems"></a>Operativsystem som stöds

Om du vill förbereda hårddiskarna med verktyget WAImportExport stöds följande **64-bitars operativsystem som stöder BitLocker-diskkryptering.**


|Plattform |Version |
|---------|---------|
|Windows     | Windows 7 Företag, Windows 7 Ultimate <br> Windows 8 Pro, Windows 8 Enterprise, Windows 8.1 Pro, Windows 8.1 Enterprise <br> Windows 10        |
|Windows Server     |Windows Server 2008 R2 <br> Windows Server 2012, Windows Server 2012 R2         |

## <a name="other-required-software-for-windows-client"></a>Annan programvara som krävs för Windows-klienten

|Plattform |Version |
|---------|---------|
|.NET Framework    | 4.5.1       |
| BitLocker        |  _          |


## <a name="supported-storage-accounts"></a>Lagringskonton som stöds

Azure Import/Export-tjänsten stöder följande typer av lagringskonton:

- Standardkonton för allmänt ändamål v2-lagring (rekommenderas för de flesta scenarier)
- Blob Storage-konton
- General Purpose v1-lagringskonton (både Classic- eller Azure Resource Manager-distributioner),

Mer information om lagringskonton finns i [översikt över Azure storage-konton](storage-account-overview.md).

Varje jobb kan användas för att överföra data till eller från endast ett lagringskonto. Med andra ord kan ett enda import-/exportjobb inte sträcka sig över flera lagringskonton. Information om hur du skapar ett nytt lagringskonto finns i [Så här skapar du ett lagringskonto](storage-account-create.md).

> [!IMPORTANT]
> Azure Import Export-tjänsten stöder inte lagringskonton där funktionen Slutpunkter för virtual network [service](../../virtual-network/virtual-network-service-endpoints-overview.md)har aktiverats. 

## <a name="supported-storage-types"></a>Lagringstyper som stöds

Följande lista över lagringstyper stöds med Azure Import/Export-tjänsten.


|Jobb  |Lagringstjänst |Stöds  |Stöds inte  |
|---------|---------|---------|---------|
|Importera     |  Azure Blob Storage <br><br> Azure File Storage       | Blockera blobbar och sidblobar som stöds <br><br> Filer som stöds          |
|Exportera     |   Azure Blob Storage       | Blockera blobbar, sidblobar och Lägg till blobbar stöds         | Azure-filer stöds inte


## <a name="supported-hardware"></a>Maskinvara som stöds

För Azure Import/Export-tjänsten behöver du diskar som stöds för att kopiera data.

### <a name="supported-disks"></a>Diskar som stöds

Följande lista över diskar stöds för användning med tjänsten Importera/exportera.


|Disktyp  |Storlek  |Stöds |
|---------|---------|---------|
|SSD    |   2.5"      |SATA III          |
|HDD     |  2.5"<br>3.5"       |SATA II, SATA III         |

Följande disktyper stöds inte:
- Usbs.
- Extern hårddisk med inbyggd USB-adapter.
- Diskar som finns inuti höljet på en extern hårddisk.

Ett enda import-/exportjobb kan ha:
- Högst 10 hdd/ssd.a maximum of 10 HDD/SSD.
- En blandning av hårddisk / SSD av alla storlekar.

Ett stort antal enheter kan spridas över flera jobb och det finns inga gränser för hur många jobb som kan skapas. För importjobb bearbetas endast den första datavolymen på enheten. Datavolymen måste formateras med NTFS.

När du förbereder hårddiskar och kopierar data med verktyget WAImportExport kan du använda externa USB-adaptrar. De flesta färdiga USB 3.0 eller senare adaptrar bör fungera.


## <a name="next-steps"></a>Nästa steg

* [Konfigurera verktyget WAImportExport](storage-import-export-tool-how-to.md)
* [Överföra data med kommandoradsverktyget AzCopy](storage-use-azcopy.md)
* [Azure Import Export REST API-exempel](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/)
