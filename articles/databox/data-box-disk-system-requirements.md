---
title: Systemkrav för Microsoft Azure Data Box-Disk | Microsoft Docs
description: Läs mer om programvara och nätverkskrav för din Azure Data Box-Disk
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/06/2018
ms.author: alkohli
ms.openlocfilehash: aaa4e4bb24ca42adb9d283e6286dbef879bcb1ea
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44299856"
---
# <a name="azure-data-box-disk-system-requirements-preview"></a>Systemkrav för Azure Data Box-Disk (förhandsversion)

Den här artikeln beskriver viktiga systemkraven för din Microsoft Azure Data Box-Disk-lösning och för klienter som ansluter till Data Box-Disk. Vi rekommenderar att du läser informationen noggrant innan du distribuerar din Data Box-disken och sedan refererar tillbaka till det som behövs under distributionen och efterföljande driften.

> [!IMPORTANT]
> Data Box-disken är i förhandsversion. Granska den [villkor för användning av förhandsversionen av](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) innan du distribuerar den här lösningen. 

Systemkraven omfattar plattformarna som stöds för klienter som ansluter till diskar, stöds storage-konton och lagringstyper.


## <a name="supported-operating-systems-for-clients"></a>Operativsystem som stöds för klienter

Här är en lista över operativsystem som stöds för disken låsa upp och Datakopieringen via klienter som är anslutna till Data Box-Disk.

| **Operativsystem** | **Testade versioner** |
| --- | --- |
| Windows Server |2008 R2 SP1 <br> 2012 <br> 2012 R2 <br> 2016 |
| Windows |7, 8, 10 |
|Linux <br> <li> Ubuntu </li><li> Debian </li><li> Red Hat Enterprise Linux (RHEL) </li><li> CentOS| <br>14.04, 16.04, 18.04 <br> 8.11, 9 <br> 7.0 <br> 6.5, 6,9, 7.0, 7.5 |  

## <a name="other-required-software-for-windows-clients"></a>Annan nödvändig programvara för Windows-klienter

För Windows-klienten installeras även följande.

| **Programvara**| **Version** |
| --- | --- |
| Windows PowerShell |5.0 |
| .NET Framework |4.5.1 |
| Windows Management Framework |5.0|
| BitLocker| - |

## <a name="other-required-software-for-linux-clients"></a>Annan nödvändig programvara för Linux-klienter

Verktygsuppsättningen för Data Box-Disk installerar följande nödvändig programvara för Linux-klient:

- dislocker
- OpenSSL

## <a name="supported-storage-accounts"></a>Stöds storage-konton

Här är en lista över typerna som stöds för Data Box-Disk.

| **Lagringskonto** | **Anteckningar** |
| --- | --- |
| Klassisk | Standard |
| Generellt syfte  |Standard. både V1 och V2 stöds. Både frekventa och lågfrekventa nivåer stöds. |


## <a name="supported-storage-types"></a>Lagringstyper som stöds

Här är en lista över typerna som stöds för Data Box-Disk.

| **Filformat** | **Anteckningar** |
| --- | --- |
| Azure blockblob | |
| Sida för Azure-blob  | |


## <a name="next-step"></a>Nästa steg

* [Distribuera Azure Data Box-Disk](data-box-disk-deploy-ordered.md)

