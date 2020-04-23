---
title: Krav för tjänsten Azure import/export | Microsoft Docs
description: Förstå program-och maskin varu kraven för Azure import/export-tjänsten.
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

I den här artikeln beskrivs viktiga krav för Azure import/export-tjänsten. Vi rekommenderar att du läser informationen noggrant innan du använder import/export-tjänsten och sedan refererar tillbaka till den vid behov under åtgärden.

## <a name="supported-operating-systems"></a>Operativsystem som stöds

För att förbereda hård diskarna med WAImportExport-verktyget stöds följande **64-bitars operativ system som stöder BitLocker-diskkryptering** .


|Plattform |Version |
|---------|---------|
|Windows     | Windows 7 Enterprise, Windows 7 Ultimate <br> Windows 8 Pro, Windows 8 Enterprise, Windows 8,1 Pro, Windows 8,1 Enterprise <br> Windows 10        |
|Windows Server     |Windows Server 2008 R2 <br> Windows Server 2012, Windows Server 2012 R2         |

## <a name="other-required-software-for-windows-client"></a>Annan nödvändig program vara för Windows-klient

|Plattform |Version |
|---------|---------|
|.NET Framework    | 4.5.1       |
| BitLocker        |  _          |


## <a name="supported-storage-accounts"></a>Lagringskonton som stöds

Tjänsten Azure import/export stöder följande typer av lagrings konton:

- Standard Generell användning v2-lagrings konton (rekommenderas för de flesta scenarier)
- Blob Storage-konton
- Generell användning v1-lagrings konton (både klassiska eller Azure Resource Manager distributioner),

Mer information om lagrings konton finns i [Översikt över Azure Storage-konton](storage-account-overview.md).

Varje jobb kan användas för att överföra data till eller från ett enda lagrings konto. Med andra ord kan ett enda import/export-jobb inte omfatta över flera lagrings konton. Information om hur du skapar ett nytt lagrings konto finns i [så här skapar du ett lagrings konto](storage-account-create.md).

> [!IMPORTANT]
> Azure import export-tjänsten stöder inte lagrings konton där funktionen för [Virtual Network tjänst slut punkter](../../virtual-network/virtual-network-service-endpoints-overview.md)har Aktiver ATS. 

## <a name="supported-storage-types"></a>Lagringstyper som stöds

Följande lista över lagrings typer stöds med Azure import/export-tjänsten.


|Jobb  |Lagrings tjänst |Stöds  |Stöds inte  |
|---------|---------|---------|---------|
|Importera     |  Azure Blob Storage <br><br> Azure File Storage       | Blockera blobbar och sid-blobar som stöds <br><br> Filer som stöds          |
|Exportera     |   Azure Blob Storage       | Block-blobar, Page blobbar och bifogade blobbar stöds         | Azure Files stöds inte


## <a name="supported-hardware"></a>Maskin vara som stöds

För tjänsten Azure import/export behöver du diskar som stöds för att kopiera data.

### <a name="supported-disks"></a>Diskar som stöds

Följande lista över diskar stöds för användning med import/export-tjänsten.


|Disktyp  |Storlek  |Stöds |
|---------|---------|---------|
|SSD    |   2,5 "      |SATA III          |
|HDD     |  2,5 "<br>3,5 "       |SATA II, SATA III         |

Följande disk typer stöds inte:
- USBs.
- Extern hård disk med inbyggd USB-adapter.
- Diskar som är i höljet för en extern hård disk.

Ett enda import/export-jobb kan ha:
- Högst 10 hård disk-/SSD.
- En blandning av hård diskar/SSD i valfri storlek.

Ett stort antal enheter kan spridas över flera jobb och det finns ingen gräns för antalet jobb som kan skapas. För import jobb bearbetas bara den första data volymen på enheten. Data volymen måste vara formaterad med NTFS.

När du förbereder hård diskar och kopierar data med hjälp av WAImportExport-verktyget kan du använda externa USB-anpassningar. De flesta USB 3,0-och senare-anpassningarna i rack bör fungera.


## <a name="next-steps"></a>Nästa steg

* [Konfigurera verktyget WAImportExport](storage-import-export-tool-how-to.md)
* [Överföra data med kommando rads verktyget AzCopy](storage-use-azcopy.md)
* [Exempel på Azure import-export REST API](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/)
