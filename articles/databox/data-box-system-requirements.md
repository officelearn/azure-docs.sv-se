---
title: Systemkrav för Microsoft Azure Data Box | Microsoft Docs
description: Lär dig mer om programvaran och nätverkskraven för din Azure Data Box-enhet
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 12/27/2018
ms.author: alkohli
ms.openlocfilehash: af7bcf2a83259b9d883a824b05312316f9f1f4f8
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/27/2018
ms.locfileid: "53794013"
---
# <a name="azure-data-box-system-requirements"></a>Azure Data Box-systemkrav

Den här artikeln beskriver viktiga systemkraven för din Microsoft Azure Data Box och för klienter som ansluter till Data Box. Vi rekommenderar att du läser informationen noggrant innan du distribuerar din Data box-enhet och sedan refererar tillbaka till det som behövs under distributionen och efterföljande driften.

Systemkraven är:

* **Programvarukrav för värdar som ansluter till Data Box** -beskriver plattformarna som stöds, webbläsare för det lokala webbgränssnittet, SMB-klienter och ytterligare krav för värdar som kan ansluta till Data Box.
* **Nätverkskrav för Data Box** – innehåller information om nätverkskrav för optimala driften av Data Box.


## <a name="software-requirements"></a>Programvarukrav

Programvarukrav innehålla information om de operativsystem som stöds och webbläsare som stöds för det lokala webbgränssnittet SMB-klienter.

### <a name="supported-operating-systems-for-clients"></a>Operativsystem som stöds för klienter

Här är en lista över operativsystem som stöds för själva Datakopieringen via klienter som är anslutna till Data Box-enhet.

| **Operativsystem** | **Versioner** | 
| --- | --- | 
| Windows Server |2008 R2 SP1 <br> 2012 <br> 2012 R2 <br> 2016 | 
| Windows |7, 8, 10 | 
|Linux    |         |

### <a name="supported-file-systems-for-linux-clients"></a>Filsystem som stöds för Linux-klienter

| **Protokoll** | **Versioner** | 
| --- | --- | 
| SMB |2.X och senare |
| NFS | Alla versioner upp till och inklusive 4.1|

### <a name="supported-storage-accounts"></a>Stöds storage-konton

Här är en lista över typerna som stöds för Data Box-enhet.

| **Lagringskonto** | **Anteckningar** |
| --- | --- |
| Klassisk | Standard |
| Generellt syfte  |Standard. både V1 och V2 stöds. |
| Blob |Frekvent och lågfrekvent stöds. |

>[!NOTE]
> Azure Data Lake Storage Gen 2-konton stöds inte.


### <a name="supported-storage-types"></a>Lagringstyper som stöds

Här är en lista över typerna som stöds för Data Box-enhet.

| **Filformat** | **Anteckningar** |
| --- | --- |
| Azure blockblob | |
| Sida för Azure-blob  | Data ska vara 512 byte justerad.|
| Azure Files | |


### <a name="supported-web-browsers"></a>Webbläsare som stöds

Här är en lista över webbläsare som stöds för det lokala webbgränssnittet.

| **Webbläsare** | **Versioner** | **Ytterligare krav/anteckningar** |
| --- | --- | --- |
| Google Chrome |Senaste versionen |Testats med Chrome|
| Microsoft Edge |Senaste versionen | |
| FireFox | Senaste versionen | Testats med FireFox|
| Internet Explorer |Senaste versionen |Om du inte logga in, kontrollerar du om cookies och Javascript är aktiverat. Lägg till IP-Adressen för enheten att om du vill aktivera åtkomst till Användargränssnittet **Sekretessåtgärder** så att enheten kan komma åt cookies. |


## <a name="networking-requirements"></a>Nätverkskrav

Datacentret måste ha höghastighetsnätverk. Vi rekommenderar starkt att du har en anslutning på minst 10 GbE. Om en 10 GbE-anslutning inte är tillgänglig en 1 GbE-datalänk kan användas för att kopiera data men kopia hastighet som påverkas.

## <a name="next-step"></a>Nästa steg

* [Distribuera Azure Data Box](data-box-deploy-ordered.md)

