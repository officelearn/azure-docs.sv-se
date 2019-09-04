---
title: Microsoft Azure Data Box Disk System krav | Microsoft Docs
description: Läs om program-och nätverks kraven för din Azure Data Box Disk
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: f054d0665c49ee048451681a7cc34d2a648a6e9e
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70240228"
---
::: zone target="docs"

# <a name="azure-data-box-disk-system-requirements"></a>Azure Data Box Disk System krav

I den här artikeln beskrivs viktiga system krav för din Microsoft Azure Data Box Disk-lösning och för klienter som ansluter till Data Box Disk. Vi rekommenderar att du läser informationen noggrant innan du distribuerar din Data Box Disk och sedan går tillbaka till den vid behov under distributionen och efterföljande åtgärder.

System kraven omfattar de plattformar som stöds för klienter som ansluter till diskar, lagrings konton som stöds och lagrings typer.

::: zone-end

::: zone target="chromeless"

# <a name="review-prerequisites"></a>Granska krav

1. Du måste ha ordnat data Box disk med hjälp [av självstudierna: Beställ dina Azure Data Box Disk](data-box-disk-deploy-ordered.md). Du har fått diskarna och en anslutnings kabel per disk.
2. Du har en tillgänglig klientdator som du kan kopiera data från. Klientdatorn måste:

    - Kör ett operativ system som stöds.
    - Ha annan nödvändig program vara installerad.

::: zone-end

## <a name="supported-operating-systems-for-clients"></a>Operativ system som stöds för klienter

Här är en lista över de operativ system som stöds för disk upplåsningen och data kopierings åtgärden via klienterna som är anslutna till Data Box Disk.

| **Operativsystem** | **Testade versioner** |
| --- | --- |
| Windows Server |2008 R2 SP1 <br> 2012 <br> 2012 R2 <br> 2016 |
| Windows (64-bitars) |7, 8, 10 |
|Linux <br> <li> Ubuntu </li><li> Debian </li><li> Red Hat Enterprise Linux (RHEL) </li><li> CentOS| <br>14.04, 16.04, 18.04 <br> 8,11, 9 <br> 7.0 <br> 6,5, 6,9, 7,0, 7,5 |  

## <a name="other-required-software-for-windows-clients"></a>Annan nödvändig program vara för Windows-klienter

För Windows-klienten bör även följande vara installerat.

| **Programvara**| **Version** |
| --- | --- |
| Windows PowerShell |5.0 |
| .NET Framework |4.5.1 |
| Windows Management Framework |5.0|
| BitLocker| - |

## <a name="other-required-software-for-linux-clients"></a>Annan nödvändig program vara för Linux-klienter

För Linux-klienten installerar Data Box Disk tools följande nödvändiga program vara:

- Lås upp
- OpenSSL

::: zone target="docs"

## <a name="supported-connection"></a>Anslutning som stöds

Klient datorn som innehåller data måste ha en USB-port på 3,0 eller senare. Diskarna ansluter till den här klienten med den angivna kabeln.

## <a name="supported-storage-accounts"></a>Stöds storage-konton

Här är en lista över de lagrings typer som stöds för Data Box Disk.

| **Lagringskonto** | **Anteckningar** |
| --- | --- |
| Klassisk | Standard |
| Generellt syfte  |Standard både v1 och v2 stöds. Både frekventa och låg frekventa nivåer stöds. |
| Blob Storage-konto | |

>[!NOTE]
> Konton för Azure Data Lake Storage gen 2 stöds inte.


## <a name="supported-storage-types-for-upload"></a>Lagrings typer som stöds för uppladdning

Här är en lista över de lagrings typer som stöds för uppladdning till Azure med hjälp av Data Box Disk.

| **Fil format** | **Anteckningar** |
| --- | --- |
| Azure Block-Blob | |
| Azure Page BLOB  | |
| Azure Files  | |
| Managed Disks | |


## <a name="next-step"></a>Nästa steg

* [Distribuera Azure Data Box Disk](data-box-disk-deploy-ordered.md)

::: zone-end

