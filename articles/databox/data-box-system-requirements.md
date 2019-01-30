---
title: Systemkrav för Microsoft Azure Data Box | Microsoft Docs
description: Lär dig mer om programvaran och nätverkskraven för din Azure Data Box-enhet
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 01/23/2019
ms.author: alkohli
ms.openlocfilehash: 7d52af9e3948f40936795efab5b6671c3f71007a
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55206755"
---
# <a name="azure-data-box-system-requirements"></a>Azure Data Box-systemkrav

Den här artikeln beskriver viktiga systemkraven för din Microsoft Azure Data Box och för klienter som ansluter till Data Box. Vi rekommenderar att du har läst informationen noggrant innan du distribuerar din Data box-enhet och sedan refererar tillbaka till det som behövs under distributionen och efterföljande driften.

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

Här är en lista över lagringskontona som stöds och lagringstyper för Data Box-enhet. En fullständig lista över alla olika typer av lagringskonton och deras fullständiga funktioner Se [typer av lagringskonton](/azure/storage/common/storage-account-overview#types-of-storage-accounts).

| **Storage-konto / stöds lagringstyper** | **Blockblob** |**Sidblob*** |**Azure Files** |**Anteckningar**|
| --- | --- | -- | -- | -- |
| Klassiska Standard | Y | Y | Y |
| General-Purpose v1-Standard  | Y | Y | Y | Frekvent och lågfrekvent stöds.|
| General-Purpose v1 Premium  |  | Y| | |
| Gpv2 Standard  | Y | Y | Y | Frekvent och lågfrekvent stöds.|
| Generell användning v2 Premium  |  |Y | | |
| BLOB-lagring Standard |Y | | |Frekvent och lågfrekvent stöds. |

\* *-Data som överförs till sidblobar måste vara 512 byte justerad, till exempel virtuella hårddiskar.*

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

Datacentret måste ha höghastighetsnätverk. Vi rekommenderar starkt att du har en anslutning på minst 10 GbE. Om en 10 GbE-anslutning inte är tillgänglig användas en 1 GbE-datalänk för att kopiera data men kopian hastighet som påverkas.

## <a name="next-step"></a>Nästa steg

* [Distribuera Azure Data Box](data-box-deploy-ordered.md)

