---
title: Krav för Azure Import/Export service | Microsoft Docs
description: Förstå kraven för Azure Import/Export service programvara och maskinvara.
author: alkohli
manager: jeconnoc
services: storage
ms.service: storage
ms.topic: article
ms.date: 06/06/2018
ms.author: alkohli
ms.openlocfilehash: 4c6e22f50f4550cb4a6e25960bcc13a4d92e9819
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2018
ms.locfileid: "34825075"
---
# <a name="azure-importexport-system-requirements"></a>Systemkrav för Azure Import/Export

Den här artikeln beskriver viktiga för din Azure Import/Export-tjänst. Vi rekommenderar att du läser informationen noggrant innan du använder Import/Export-tjänsten och sedan refererar tillbaka till den vid behov under åtgärden.

## <a name="supported-operating-systems"></a>Operativsystem som stöds

Förbereda hårddiskar med hjälp av verktyget WAImportExport följande **64-bitars operativsystem som stöder BitLocker-diskkryptering** stöds.


|Plattform |Version |
|---------|---------|
|Windows     | Windows 7 Enterprise, Windows 7 Ultimate <br> Windows 8 Pro, Windows 8 Enterprise, Windows 8.1 Pro, Windows 8.1 Enterprise <br> Windows 10        |
|Windows Server     |Windows Server 2008 R2 <br> Windows Server 2012, Windows Server 2012 R2         |



## <a name="supported-storage-accounts"></a>Stöds storage-konton

Azure Import/Export-tjänsten stöder följande Azure storage-konton.
- Klassisk
- Blob Storage-konton
- Allmänna ändamål v1 storage-konton. 

Varje jobb kan användas för att överföra data till eller från en enda storage-konto. Med andra ord kan inte ett enda import/export-jobb vara över flera lagringskonton. Information om hur du skapar ett nytt lagringskonto finns [hur du skapar ett Lagringskonto](storage-create-storage-account.md#create-a-storage-account).

> [!IMPORTANT] 
> Tjänsten Azure Import exportera har inte stöd för lagringskonton där den [virtuella nätverksslutpunkter](../../virtual-network/virtual-network-service-endpoints-overview.md) funktionen har aktiverats. 

## <a name="supported-storage-types"></a>Lagringstyper som stöds

Följande lista över lagringstyper stöds med Azure Import/Export-tjänsten.


|Jobb  |Storage  |Stöds  |Stöds inte  |
|---------|---------|---------|---------|
|Importera     |  Azure Blob storage. <br>Blockblobbar, sidblobbar som stöds. <br> Azure-filer stöds.       |         |
|Exportera     |   Azure Blob storage. <br>Blockblobbar, sidblobbar och Lägg till blobs stöds.       | Azure-filer stöds inte.        |


## <a name="supported-hardware"></a>Maskinvara som stöds 

För tjänsten Azure Import/Export måste stöds diskar och stöd för SATA-anslutningsprogram för att kopiera data.

### <a name="supported-disks"></a>Stöds diskar

Följande lista över diskar stöds för användning med Import/Export-tjänsten.


|Disktyp  |Storlek  |Stöds |Stöds inte  |
|---------|---------|---------|---------|
|SSD    |   2,5-tums      |         |         |
|HDD     |  2,5-tums<br>3,5-tums       |SATA II SATA III         |Externa hårddiskar med inbyggd USB-adapter <br> Disk i versaler och gemener i en extern Hårddisk         |


Ett enda import/export-jobb kan ha:
- Högst 10 Hårddisk/SSD.
- En blandning av Hårddisk/SSD av valfri storlek.

Stort antal enheter går att sprida över flera jobb och det finns inga begränsningar för antalet jobb som kan skapas. 

För importjobb bearbetas bara den första datavolymen på enheten. Datavolym måste vara formaterad med NTFS.

### <a name="supported-external-usb-adaptors"></a>Stöd för externa USB-adaptrar

När förbereder hårddiskar och kopiering av data med hjälp av verktyget WAImportExport, kan du använda följande (ut-the-shelp) externa USB-adaptrar: 
- Anker 68UPSATAA - 02 för
- Anker 68UPSHHDS-för
- Startech SATADOCK22UE
- Orico 6628SUS3-C-BK (6628-serien)
- Thermaltake BlacX frekvent växlingen SATA externa hårddisken enhet dockningsstation (USB 2.0 & eSATA)


## <a name="next-steps"></a>Nästa steg

* [Konfigurera verktyget WAImportExport](storage-import-export-tool-how-to.md)
* [Överföra data med kommandoradsverktyget AzCopy](storage-use-azcopy.md)
* [Azure Import exportera REST API-exemplet](https://azure.microsoft.com/documentation/samples/storage-dotnet-import-export-job-management/)

